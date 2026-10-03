# C# types and data modeling

Researched: 2026-09-27. General C#; examples are illustrative and uncompiled. Recommendations describe design choices, not mandatory language rules.

## Understand what assignment copies

A value-type variable holds its value; a reference-type variable holds a reference to an object. Ordinary assignment copies that variable's value. For a class, two variables can therefore point to the same mutable object. Copying a struct copies its fields, including references to any nested objects; it is not a deep clone.

```csharp
using System.Collections.Generic;

public sealed class AssignmentExample
{
    public void Run()
    {
        int original = 4;
        int copy = original;
        copy = 9;

        var first = new List<int> { original };
        var second = first;
        second.Add(copy);
        // original is still 4; first and second both refer to [4, 9].
    }
}
```

Reason about copying and ownership before thinking about memory placement. “Struct means stack, class means heap” is not a sufficient model of C# behavior: a value can be a field of another object, and reference values themselves are copied. A practical review question is whether the caller expects an independent snapshot or access to shared state.

## Choose a shape that matches meaning

Our recommendation: use a class for an entity with identity and controlled mutable state; consider a small immutable struct for a value that should be copied and compared by meaning. A coordinate is a natural value; an account with transactions has identity. Large structs or frequently copied mutable structs need careful justification.

Records offer compiler-generated value equality, but distinguish record classes from record structs and verify language support: record classes require C# 9; record structs require C# 10. Do not assume a record deeply freezes referenced lists or other mutable members. The important question is the intended equality and mutation contract.

For a small group of named alternatives, an enum makes intent clearer than magic integers. When storing enum values externally, treat the representation as a versioned contract rather than freely reordering values.

## Express absence deliberately

With nullable reference analysis enabled, `string?` declares that absence is expected, while `string` states a non-null intention. These annotations produce compiler analysis; they do not add runtime validation. The null-forgiving operator suppresses a warning rather than fixing a possible null value. Nullable reference types require C# 8.

Our recommendation: use absence when it is a meaningful result, and validate required inputs at a clear boundary. Do not use null interchangeably for “not found,” “not loaded,” and “operation failed” if callers need to distinguish those states. An empty collection can mean a successful query with no matches; it should not silently hide a failed query.

## Protect invariants at the point of mutation

An invariant is a condition the object promises to maintain, such as a balance never becoming negative. Prefer operations such as `TrySpend(amount)` over a publicly writable balance if callers must respect that rule. A constructor can establish required initial state, while methods preserve it.

Before implementing the operation, state whether zero is allowed, what happens for negative input, whether failure changes state, and whether concurrent callers are supported. These decisions make a type useful; adding a property or interface does not decide them automatically. This is our API-design recommendation.

## 2026-09-27: Practical adoption

When reviewing a model, ask: what is its identity, what gets copied, who may mutate it, what does absence mean, and how is equality used? Resolve those questions before adding inheritance or generic infrastructure. Verify compiler and runtime support in the target project; this note does not establish serialization behavior for a particular framework.

## References

- [Microsoft: value types](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/value-types)
- [Microsoft: equality operators](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/equality-operators)
- [Microsoft: nullable references](https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/null-safety/nullable-reference-types)
