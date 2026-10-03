# C# patterns for gameplay code

Researched: 2026-09-27. Scope: solo and small-team Unity games. These are selection recommendations informed by primary documentation, not a required architecture. The example uses ordinary C# compatible in intent with Unity 6.0; it has not been compiled or integrated.

## Choose a pattern for a concrete change

Start with the problem: repeated branching, hard-to-test rules, invisible dependencies, repeated object construction, or UI that mixes presentation with business rules. Name the change that is currently difficult. A pattern earns its place if it makes that change easier without hiding ownership.

Our recommendation is to begin with direct calls and small concrete classes. Use interfaces at boundaries where callers actually need interchangeable behavior, a test substitute, or isolation from an external service. An interface defines a contract; it does not by itself create useful decoupling.

For a small team, measure usefulness by whether another person can find the behavior, follow its call path, and change it safely. A diagram requiring a dozen objects for a simple pickup suggests the pattern may cost more than the feature needs.

## Strategy: vary a rule without duplicating its caller

A strategy isolates one interchangeable decision, such as damage calculation, target selection, or scoring. Here, the caller can supply different damage rules without modifying the health system:

```csharp
public interface IDamageRule
{
    int Calculate(int incomingDamage, int armor);
}

public sealed class FlatArmorRule : IDamageRule
{
    public int Calculate(int incomingDamage, int armor)
    {
        return System.Math.Max(0, incomingDamage - armor);
    }
}
```

The example assumes nonnegative, bounded inputs; deciding where to validate them is part of the real API contract. A constructor can provide this rule to a plain C# combat model. A MonoBehaviour can choose or construct the model at a deliberate setup point. A plain interface field is not automatically an Inspector authoring interface.

Our adoption test: extract a strategy when two real variants exist or a rule needs independent testing. A single short calculation can remain a method. Shared behavior does not require a shared base class, and a hierarchy such as `Enemy -> ArmoredEnemy -> FireArmoredEnemy` becomes awkward when abilities combine independently.

## State: make modes and transitions explicit

A state machine represents mutually exclusive modes and the allowed transitions between them. Start with an enum and a clear transition method for a small interaction: closed, opening, open. Separate state classes become useful when each mode has substantial entry, update, and exit behavior. Unity's state-pattern example demonstrates this structure for character modes.

Our recommendation: centralize transition ownership. Entering a new state should deliberately end the previous state's animation, subscription, or timed work where applicable. Name transition conditions in gameplay terms and test invalid transitions. Do not represent independent dimensions, such as equipped weapon and movement mode, by multiplying every possible combination into one giant state enum.

## Observer: report facts to independent listeners

Events suit completed facts with independent consumers: a quest system, sound system, and HUD might all react to `ItemCollected`. Direct calls suit required collaboration where the caller needs an answer, such as asking inventory to accept an item. An event whose success is necessary to continue gameplay often hides a required dependency.

Unity demonstrates ScriptableObject event channels as Inspector-visible communication assets. They can help designers wire reactions without binding scene objects directly, but this is an authoring choice rather than a replacement for every call.

Our recommendation: choose one local event mechanism per use case and make subscriptions discoverable. Record who publishes, who listens, and who disconnects. Include enough event data that listeners need not query a global object whose state may already have changed. Avoid using an event chain as a hidden sequence of mandatory operations.

## Factory and pool: distinguish creation policy from reuse

A factory gives creation one named entry point: select the prefab, supply dependencies, initialize its state, and return the ready object. It becomes useful when this setup is repeated or varies by product. A simple factory method is often enough; an inheritance hierarchy is optional. Unity describes factories as a way to encapsulate construction and allows them to work with pools.

A pool reuses objects to reduce repeated creation and destruction. Unity provides `ObjectPool<T>` for this purpose. Reuse creates a lifecycle contract: returning an object must reset mutable state, disconnect listeners, and stop owned work as needed before another borrower receives it. Adopt pooling when profiling or a clear workload justifies the retained objects and reset complexity.

## Commands and presenters: separate intentions from display

A command represents an operation as an object or data record. Our recommendation is to use it when queuing, undo, input remapping, or replay needs a stable description of player intent. A direct method is sufficient for a one-off action. Replay also requires controlled time, randomness, and state; storing commands alone does not make a simulation deterministic.

A presenter coordinates UI events and a model so gameplay rules can be checked without rendering the view. Unity's pattern overview describes MVP as separating displayed state from the state itself.

For an inventory screen, let the model decide whether an item fits and let the presenter update slots and feedback. Do not add presenter layers to static labels with no behavior. On 2026-09-27, the working decision is to treat patterns as tools selected per problem, then validate their value through an actual feature change.

## References

- [Microsoft interface reference](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/interface)
- [Unity state-pattern tutorial](https://learn.unity.com/course/design-patterns/tutorial/develop-a-modular-flexible-codebase-with-the-state-programming-pattern)
- [Unity event-channel guide](https://unity.com/how-to/scriptableobjects-event-channels-game-code)
- [Unity factory tutorial](https://learn.unity.com/tutorial/65e0df08edbc2a2447bf0b98?version=6.0)
- [Unity pooling tutorial](https://learn.unity.com/tutorial/use-object-pooling-to-boost-performance-of-c-scripts-in-unity)
- [Unity game-pattern overview](https://unity.com/blog/game-programming-patterns-update-ebook)
