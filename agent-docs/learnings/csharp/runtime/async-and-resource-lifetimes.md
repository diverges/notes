# C# async work and resource lifetimes

Researched: 2026-09-27. General .NET Task-based programming; illustrative examples are uncompiled. Host-specific execution contexts and threading restrictions must be checked separately.

## Async is not another word for parallel

An async method can suspend while awaiting an incomplete operation and resume when it completes. It does not automatically move all of its code to another thread. For I/O-bound work, use the API's asynchronous operation when available. CPU-bound work may justify background execution where the host supports it and measurement shows a benefit.

Our recommendation: identify whether the problem is waiting, computation, or coordination before choosing a mechanism. Wrapping every operation in `Task.Run` can obscure the actual need. Avoid blocking on task results in code intended to stay asynchronous; blocking changes responsiveness and can interact badly with a captured context.

## Give completion and failure a caller

Return `Task` or `Task<T>` from ordinary async operations so callers can await them and observe completion or exceptions. `async void` is generally for event contracts that require a void return; its caller cannot await it or catch its later asynchronous failure through the call.

Our recommendation: name the owner of every started operation. If work is deliberately independent of a request, assign it a longer-lived owner that still observes errors and handles shutdown. “Fire and forget” is not a cleanup or error-handling policy.

## Cancellation is cooperative

A cancellation token carries a request; the operation must check it or pass it to a supporting API. The owner of a `CancellationTokenSource` also owns its disposal. Disposing a source is not equivalent to requesting cancellation.

```csharp
using System;
using System.Threading;
using System.Threading.Tasks;

public static class DelayExample
{
    public static async Task<int> ReadAfterDelayAsync(
        Func<int> readValue, CancellationToken cancellationToken)
    {
        if (readValue == null) throw new ArgumentNullException(nameof(readValue));
        await Task.Delay(100, cancellationToken);
        cancellationToken.ThrowIfCancellationRequested();
        return readValue();
    }
}
```

The delay is illustrative, not an actual data-loading implementation. The check prevents invoking the delegate if cancellation has already been observed at that point; it does not interrupt a delegate already running. Cancellation can race with completion, so an operation that must reject obsolete results needs an explicit validity rule too.

For replaceable searches, our recommendation is to cancel the previous request and confirm that a result still belongs to the current request before displaying it. For irreversible external effects, define separately whether cancellation stops waiting, stops the operation, or requires compensating work.

## Resource lifetime is separate from object reachability

Use `using` when a disposable resource belongs to a lexical scope. Its disposal runs when control exits the scope, including exceptional exits. `await using` supports asynchronous disposal for compatible `IAsyncDisposable` resources and language/runtime combinations. Garbage collection is not a substitute for a resource's disposal contract.

Our recommendation: state whether a method owns, borrows, or transfers a resource. A helper should not dispose a caller-owned stream merely because it finished reading a portion. Likewise, returning deferred work from inside a disposed resource scope can make the work unusable later.

## Bound concurrency and protect shared state

Our design recommendation is to set a deliberate concurrency limit when processing many independent operations, based on the resource being consumed. Starting every operation at once may exhaust connections, memory, or downstream capacity. Define failure and cancellation behavior for the group as well as for each item.

Avoid shared mutable state where independent results can be combined afterward. When state must be shared, define synchronization and ordering explicitly. Async syntax alone does not make a type thread-safe, and an awaited operation does not promise a particular application thread unless its host/context contract provides that guarantee.

## 2026-09-27: Review questions

Ask who starts the operation, who awaits it, what ends its lifetime, who handles errors, who disposes resources, and what prevents a stale result from changing current state. Test these paths in the intended host before declaring the workflow reliable.

## References

- [Microsoft: async scenarios](https://learn.microsoft.com/en-us/dotnet/csharp/asynchronous-programming/async-scenarios)
- [Microsoft: async return types](https://learn.microsoft.com/en-us/dotnet/csharp/asynchronous-programming/async-return-types)
- [Microsoft: managed cancellation](https://learn.microsoft.com/en-us/dotnet/standard/threading/cancellation-in-managed-threads)
- [Microsoft: using statement](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/statements/using)
