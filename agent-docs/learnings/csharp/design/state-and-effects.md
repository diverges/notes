# State, decisions, and effects

Researched: 2026-09-27. General C# design guidance. The example is illustrative and uncompiled, uses established C# syntax, and requires no framework package.

## Functional core, imperative shell

Separate decisions from external interactions. The core accepts explicit values and returns a result; the shell obtains inputs, invokes the decision, and performs effects such as saving or displaying output. Simple values at the boundary make decisions easier to test in isolation. The example below is our synthesis.

```csharp
using System;

public static class PurchaseRules
{
    public static bool TryCalculateRemaining(
        int balance, int price, out int remaining)
    {
        if (balance < 0)
            throw new ArgumentOutOfRangeException(nameof(balance));
        if (price < 0)
            throw new ArgumentOutOfRangeException(nameof(price));

        remaining = balance;
        if (price > balance) return false;
        remaining = balance - price;
        return true;
    }
}
```

This decision reads no clock, global state, or storage. Insufficient funds returns false without changing the proposed balance. A caller supplies the current balance and chooses how to commit the result. The `out` parameter returns an additional value; it does not imply mutation of a shared account.

Isolation does not provide atomicity. Two callers could calculate from the same stale balance. The shell still needs an appropriate transaction, synchronization, or version check when concurrent updates are possible. Tests of the calculation cannot establish correctness of that integration. Extract decisions where this separation improves clarity; copying a huge state graph for every operation may be a poor tradeoff.

## Own state and make transitions explicit

Our recommendation: place a state transition with the object or module responsible for its invariants. Rather than exposing writable `IsRunning`, `IsPaused`, and `IsFinished` flags, an operation might use `RunState` values and named `Start`, `Pause`, and `Finish` transitions. Specify allowed starting states, resulting state, and refusal behavior.

An enum and one transition function are often enough. Separate state objects become useful when states have substantial distinct behavior. Avoid creating a class for every enum value by default. If states contain different required data, model those payloads explicitly instead of leaving irrelevant nullable fields everywhere.

For a multi-step operation, identify the owner of the whole workflow too. It must decide what happens when step two fails after step one succeeds. Changing class structure does not supply rollback or compensation automatically.

## Commands request work; events describe facts

A command such as `ReserveStock` requests a particular action. An event such as `StockReserved` reports an outcome. Event notifications allow independent reactions, but chains of notifications can hide an obligatory workflow. Fowler specifically warns about disguising expected actions as events.

Our recommendation: keep required steps in an explicit coordinator unless there is a real reason to distribute the workflow. Use events for independently owned reactions, with clear publication timing and failure behavior. A method that must know whether inventory was reserved should receive that result explicitly.

For persistent messages, define delivery, duplicate handling, and recovery separately. A C# event subscription by itself establishes none of those guarantees.

## CQS and CQRS solve different problems

Command-query separation (CQS) distinguishes observing state from changing it. A query should be safe to call without accidentally performing a business action. Treat this as a clarity principle with practical exceptions, not a ban on an operation returning success or the value it removed.

CQRS separates read and write models when their requirements differ enough to justify that structure. It is a larger architectural choice, with additional models and coordination to maintain. Separate databases, asynchronous processing, and event sourcing are not prerequisites. Use it selectively where a shared model is demonstrably awkward.

Event sourcing makes recorded events the authoritative basis for reconstructing state. It can support history and replay, but requires handling event evolution and effects during replay. It is a separate choice from merely publishing notifications.

## 2026-09-27: Initial application rule

Start with explicit state ownership, ordinary calls for required collaboration, and isolated calculations where useful. Before adding asynchronous messaging or replay, write down the concrete requirement and failure semantics. No concurrency scenario, persistence flow, or code example was executed during this research.

## References

- [Bernhardt: Boundaries](https://www.destroyallsoftware.com/talks/boundaries): simple values and isolated testing; public abstract reviewed, not the full video.
- [Fowler: event-driven patterns](https://martinfowler.com/articles/201701-event-driven.html)
- [Fowler: command-query separation](https://martinfowler.com/bliki/CommandQuerySeparation.html)
- [Fowler: CQRS](https://martinfowler.com/bliki/CQRS.html)
