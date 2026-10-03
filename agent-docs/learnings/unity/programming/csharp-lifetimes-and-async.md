# C# lifetimes and asynchronous work in Unity

Researched: 2026-09-27. Unity 6.0 (6000.0) is the API baseline. These are documented behaviors and proposed working practices. The C# example is illustrative, not compiled or tested in a Unity project.

## Ownership comes before cleanup syntax

For each long-lived object or operation, answer who starts it, who owns its resources, and what ends it. Our practical lifetime categories are application, scene, enabled component, UI interaction, and individual request. A menu search should usually end when that request is replaced; a download managed by an application service may intentionally survive a menu closing.

Keep cleanup with the owner. Acquiring a resource and handing it elsewhere requires an explicit ownership transfer. For .NET resources implementing `IDisposable`, use `using` when the resource belongs to a lexical scope; a longer-lived owner must arrange disposal at its own endpoint. Disposal releases a resource deterministically, rather than waiting for garbage collection.

Unity objects have their own engine lifetime. A destroyed Unity object can retain a managed wrapper that compares equal to null through Unity's operator. Null-conditional and null-coalescing operators do not implement that engine check. Use an explicit Unity-aware existence check when a referenced scene object may have been destroyed. This distinction applies to `UnityEngine.Object`, not every ordinary C# class.

## Events: match subscription lifetime to behavior

A C# event publisher retains subscribed delegates, so an unwanted subscription can keep a subscriber reachable. Unsubscribe when the subscriber's work ends. Named handlers are convenient when later removal is needed; an anonymous delegate requires retaining the same delegate instance to remove it.

Our Unity recommendation: subscribe in `OnEnable` and unsubscribe in `OnDisable` when listening belongs to the enabled state. Use a different owner when listening must continue while disabled. Do not mechanically pair every subscription with destruction; pooled objects and temporarily hidden interfaces can remain alive long after they should stop responding. If the publisher reference can change, unsubscribe from the actual publisher originally used.

## Coroutine, Task, or Awaitable

| Mechanism | Useful starting point | Contract to consider |
| --- | --- | --- |
| Coroutine | Frame-based sequences and effects | Yielding schedules later work; it does not make synchronous work run on another thread |
| `Task` / `Task<T>` |.NET library operations and results shared among callers | Define cancellation, exception observation, and the continuation's execution context |
| Unity `Awaitable` | Unity asynchronous APIs and frame waits | Instances are pooled; await a given instance only once |

Unity coroutines stop when their GameObject is disabled or their MonoBehaviour is destroyed. Setting only the component's `enabled` property to false does not stop its coroutines. Explicitly stop owned routines if their intended lifetime is shorter.

Unity's `Awaitable` supports async methods and Unity operations with reduced allocation through pooling. Its single-await restriction means a cached `Awaitable` is not a general replacement for a shared `Task`. Choose the abstraction that matches callers' needs before optimizing allocations.

## Cancellation: request an end and prevent stale results

Cancellation in .NET is cooperative: a token conveys a request, and the operation must observe it. The code that owns a `CancellationTokenSource` also owns disposing it; disposal alone is not a cancellation request. Pass tokens through operations rather than burying an unrelated lifetime inside each helper.

```csharp
using System.Threading;
using UnityEngine;

public static class FrameDelay
{
    public static async Awaitable WaitOneFrameAsync(
        CancellationToken cancellationToken)
    {
        cancellationToken.ThrowIfCancellationRequested();
        await Awaitable.NextFrameAsync(cancellationToken);
        cancellationToken.ThrowIfCancellationRequested();
    }
}
```

Call this helper on the main thread: `NextFrameAsync` requires that thread and completes there. The caller chooses the cancellation lifetime.

In Unity 6.0, `destroyCancellationToken` represents a MonoBehaviour's destruction and must be obtained before destruction. It does not represent disabling or returning an object to a pool. An operation tied to an enabled state needs an appropriate shorter-lived cancellation source.

Our recommendation for replaceable UI requests: cancel the old request and also check that the completing request is still current before presenting its result. Some operations cannot cancel immediately. Cancellation requests and business validity are related but separate checks.

## Continuations and failures need an owner

Unity `Awaitable` continuations execute synchronously on completion. A custom method that switches to a background thread can therefore resume its caller there unless it deliberately switches back. Most Unity APIs require the main thread; return there before touching scene objects.

Prefer awaitable return types for ordinary async methods so callers can observe completion and exceptions. `async void` is appropriate only where a void event contract requires it; callers cannot await it or catch its asynchronous exception through the call. Such an entry point must handle failures within its own async flow.

## 2026-09-27: Verification to perform in a game

For a new asynchronous feature, exercise normal completion, immediate cancellation, repeated requests, owner disable/destruction, and an actual error. Verify both the resulting game state and resource cleanup. These are proposed checks; none were run during this research. Record an implementation incident only after observing it during coding or integration work.

## References

- [Microsoft dispose guidance](https://learn.microsoft.com/en-us/dotnet/standard/design-guidelines/dispose-pattern)
- [Unity Object reference](https://docs.unity3d.com/6000.0/Documentation/ScriptReference/Object.html)
- [Microsoft event subscription guidance](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/events/how-to-subscribe-to-and-unsubscribe-from-events)
- [Unity coroutine manual](https://docs.unity3d.com/6000.0/Documentation/Manual/Coroutines.html)
- [Unity Awaitable introduction](https://docs.unity3d.com/6000.0/Documentation/Manual/async-awaitable-introduction.html)
- [Microsoft cancellation model](https://learn.microsoft.com/en-us/dotnet/standard/threading/cancellation-in-managed-threads)
- [Unity NextFrameAsync reference](https://docs.unity3d.com/6000.0/Documentation/ScriptReference/Awaitable.NextFrameAsync.html)
- [Unity destruction token](https://docs.unity3d.com/6000.0/Documentation/ScriptReference/MonoBehaviour-destroyCancellationToken.html)
- [Unity continuation documentation](https://docs.unity3d.com/6000.0/Documentation/Manual/async-awaitable-continuations.html)
- [Microsoft async return types](https://learn.microsoft.com/en-us/dotnet/csharp/asynchronous-programming/async-return-types)
