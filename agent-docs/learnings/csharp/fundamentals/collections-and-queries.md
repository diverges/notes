# C# collections and queries

Researched: 2026-09-27. General C# and established .NET collection APIs. Examples are illustrative and uncompiled; no performance claims were measured.

## Choose by the operation you need

| Need | Starting choice | Reason |
| --- | --- | --- |
| Fixed-length indexed sequence | `T[]` | Length is fixed after allocation |
| Ordered, resizable sequence | `List<T>` | Index access and append-oriented work |
| Lookup by unique key | `Dictionary<TKey, TValue>` | Models a key-to-value relationship |
| Membership without duplicates | `HashSet<T>` | Models a set rather than a sequence |
| First-in, first-out work | `Queue<T>` | Makes processing order explicit |
| Last-in, first-out work | `Stack<T>` | Makes reverse processing order explicit |

These are starting choices, not universal performance rankings. Consider collection size, mutation frequency, ordering requirements, and the comparison contract. Generic collections preserve element type information instead of requiring everything to be handled as `object`.

Our recommendation: keep one authoritative representation until a measured need justifies a second index or cache. Maintaining a list and dictionary for the same objects adds a synchronization responsibility. If traversal order affects behavior, represent that order explicitly rather than depending on incidental enumeration behavior.

## Know when a query executes

Many LINQ operators describe work that happens during enumeration. `Where` uses deferred execution: constructing the query does not immediately run the predicate over every element.

```csharp
using System.Collections.Generic;
using System.Linq;

public static class QueryExample
{
    public static int[] CaptureBeforeChange()
    {
        var scores = new List<int> { 2, 7 };
        IEnumerable<int> passing = scores.Where(score => score >= 5);
        int[] snapshot = passing.ToArray();
        scores.Add(9);
        return snapshot;
    }
}
```

The returned array contains only `7`. Enumerating `passing` after the addition would also find `9`. This example distinguishes a query from its captured results. Do not place required side effects inside a predicate whose execution count depends on how callers enumerate it.

`ToArray` evaluates the sequence and materializes results. For reference-type elements, the array contains references, not deep copies of objects. Materialization trades storage for a captured sequence; it is a deliberate choice, not a mandatory ending for every query.

## Be explicit about what a caller receives

Our recommendation: use `IEnumerable<T>` when a caller only needs traversal, but document if enumeration is expensive, stateful, or tied to a resource lifetime. A read-only interface limits operations through that interface; it does not promise the underlying data cannot change elsewhere.

For example, a live view of active requests and a completed-request snapshot have different meanings even if both can be enumerated. Name the API and describe its lifetime accordingly. Avoid returning an iterator whose required stream has already been disposed before enumeration starts.

## Evaluate allocations in context

Boxing wraps a value type as an object; unboxing extracts the boxed value with the required type relationship. Generic APIs can avoid boxing that would occur when values pass through `object`-based APIs.

Our performance recommendation is to inspect a real workload before replacing readable queries with specialized code. Repeated materialization, temporary strings, captured state, and collection growth are candidates to measure, not proof of a bottleneck. Check both CPU cost and retained memory. Do not claim that every LINQ query allocates the same way or that every `foreach` loop must allocate; implementation and runtime matter.

## 2026-09-27: Review questions

Ask what order matters, who owns mutation, when enumeration happens, whether repeated enumeration repeats work, and whether the caller needs a snapshot. Benchmark only an identified performance question with representative data and the intended runtime. No collection benchmark was run for this note.

## References

- [Microsoft: selecting collections](https://learn.microsoft.com/en-us/dotnet/standard/collections/selecting-a-collection-class)
- [Microsoft: Enumerable.Where](https://learn.microsoft.com/en-us/dotnet/api/system.linq.enumerable.where)
- [Microsoft: Enumerable.ToArray](https://learn.microsoft.com/en-us/dotnet/api/system.linq.enumerable.toarray)
- [Microsoft: boxing and unboxing](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/types/boxing-and-unboxing)
