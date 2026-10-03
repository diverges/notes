# Testing and debugging C# code

Researched: 2026-09-27. General .NET practices; test examples use xUnit concepts and have not been compiled or run. Use the test framework already established by the actual project.

## Test the behavior a caller depends on

A useful test states a condition, performs an operation, and checks an observable result. Prefer meaningful rules and boundary cases over assertions that merely repeat a property assignment. Tests should help explain the contract and identify regressions without depending on private implementation details. Microsoft recommends readable, isolated tests and an arrange/act/assert structure.

For a spend operation, useful questions include exact balance, insufficient balance, negative input, and whether a refused operation changes state. Testing every getter would add much less evidence. Choose cases from the contract rather than a coverage percentage alone.

## A small example with a failure contract

The following is a self-contained illustration. The application rule permits zero spending, rejects negative input, and leaves the balance unchanged on ordinary refusal. The test checks refusal and state together.

```csharp
using System;
using Xunit;

public sealed class Allowance
{
    public int Remaining { get; private set; } = 5;

    public bool TrySpend(int amount)
    {
        if (amount < 0) throw new ArgumentOutOfRangeException(nameof(amount));
        if (amount > Remaining) return false;
        Remaining -= amount;
        return true;
    }
}

public sealed class AllowanceTests
{
    [Fact]
    public void TrySpend_WhenInsufficient_LeavesBalanceUnchanged()
    {
        var allowance = new Allowance();

        bool accepted = allowance.TrySpend(6);

        Assert.False(accepted);
        Assert.Equal(5, allowance.Remaining);
    }
}
```

In production, the rule and test normally live in separate source/test projects, with a project reference and the test packages/runner configured. `[Fact]` and the assertion calls here require xUnit; they are not built into C#. Microsoft's tutorial demonstrates the project and runner relationship. Exact package setup belongs to the project's chosen framework/version.

## Make uncertain inputs controllable

Our recommendation: separate deterministic rules from clocks, randomness, file/network access, and user input. Supply a timestamp or explicit input to a calculation when that makes its contract clearer. Introduce an interface or fake only where the dependency boundary earns its cost.

An in-memory fake can verify decision logic, but cannot prove the real service's serialization, authentication, or failure behavior. Use integration tests at important external boundaries. Avoid replacing so much of the system that tests only verify interactions between mocks.

For async tests, await completion and exercise cancellation and failure paths. Prefer explicit coordination to arbitrary sleeps. A timeout can bound a hung test; it is not evidence that work will complete reliably under every load.

## Debug by reducing uncertainty

Our workflow recommendation is to capture the actual inputs, expected result, observed result, environment, and smallest reproducible sequence. Form one plausible hypothesis, choose an observation that could disprove it, then inspect the relevant state. Change one responsible behavior and repeat the reproduction.

Breakpoints let execution stop at a chosen statement; conditional breakpoints and tracepoints can narrow investigation when the same line runs repeatedly. These Visual Studio capabilities are tools for collecting evidence, not substitutes for a reproduction.

Use the call stack and relevant variable values to distinguish where an error surfaced from where invalid state began. Keep diagnostic output focused and avoid recording secrets. For a performance complaint, measure representative release behavior; a debugger-stepped run does not establish normal timing.

## 2026-09-27: Completion evidence

Report the tests and environment actually used, the reproduced failure before/after the change, and remaining gaps. Preserve a regression case when it captures an important contract. Researching this workflow creates no implementation finding: a dedicated gotcha entry requires a problem observed during real coding or integration work.

## References

- [Microsoft: unit-test practices](https://learn.microsoft.com/en-us/dotnet/core/testing/unit-testing-best-practices)
- [Microsoft: C# tests with xUnit](https://learn.microsoft.com/en-us/dotnet/core/testing/unit-testing-csharp-with-xunit)
- [Microsoft: breakpoints](https://learn.microsoft.com/en-us/visualstudio/debugger/using-breakpoints?view=visualstudio)
