# Choosing patterns for maintainable code

Researched: 2026-09-27. General software design with C# applications in mind. These are research-informed recommendations, not project-validated results.

## Start with the change that is difficult

A useful design makes a likely change understandable and contained. Before choosing a pattern, name the difficulty: rules mixed with file access, one feature scattered across unrelated folders, impossible state combinations, or a workflow hidden behind notifications. A pattern earns its place when it reduces that difficulty more than it adds indirection.

Our working review criteria are: can a reader find the rule, identify who owns its state, follow its dependencies, and verify a change without running the whole application? These are practical questions, not a numerical maintainability score. Fewer classes or more interfaces alone prove little.

## Choose the smallest useful structure

This selection guide is our synthesis. Each row describes a possible response, not a requirement to introduce the pattern.

| Observed difficulty | Pattern to consider | First implementation | Cost to watch |
| --- | --- | --- | --- |
| Changing one feature touches many technical folders | Feature modules or vertical slices | Group one use case and its collaborators | Duplicated rules across features |
| Business rules depend on storage or UI details | Ports and adapters | One narrow application-facing boundary | Interfaces and mapping with no real purpose |
| Decisions require elaborate environment setup to test | Functional core, imperative shell | Extract a calculation using explicit inputs | Oversized snapshots or excessive copying |
| Unrelated callers freely mutate the same state | Encapsulation | One owner with named operations | An owner that grows to absorb unrelated responsibilities |
| Many booleans permit contradictory modes | Explicit state machine | Enum plus a transition function | A class per state before behavior warrants it |
| Mandatory work is scattered across event handlers | Explicit orchestration | One method showing the required sequence | A coordinator that also contains every rule |
| One policy varies independently | Strategy | A supplied function or small policy object | Abstractions for hypothetical variations |
| Subsystems need clearer boundaries but ship together | Modular monolith | Modules with explicit public contracts | Boundaries that exist only as folder names |

For example, a purchase operation can validate a price through a pure rule, change an inventory through its owner, and persist through an adapter. That does not require a message broker, a generic repository hierarchy, or separate deployments. Add those only for a concrete requirement.

## Use principles as questions, not quotas

Separation of concerns and encapsulation keep implementation details behind contracts. Explicit dependencies make required collaborators visible. Dependency inversion lets application rules depend on an abstraction while an infrastructure implementation satisfies it. Microsoft's architectural guidance explains these principles in a web application setting; applying them to desktop or game tooling requires choosing appropriate boundaries.

Our interpretation: “single responsibility” does not mean one method per class. Group behavior that protects the same rule or changes for the same reason. Likewise, do not merge two similar calculations solely because their current code matches: first establish that they represent the same policy and should change together. A shared abstraction makes future changes shared too.

## Adopt a pattern through one real change

Use a narrow, reversible experiment:

1. Describe the behavior to preserve and the next change the design should support.
2. Capture relevant behavior with focused tests or other reproducible checks.
3. Extract one boundary while preserving the existing public behavior.
4. Implement the intended change through it.
5. Review whether dependencies, state ownership, and failure handling are clearer.

Avoid judging the result by matching an architecture diagram. A useful review can trace a successful operation and a failed one, explain what remains committed after failure, and show where a new variation belongs. Integration checks remain necessary even when isolated rules are easy to test.

## 2026-09-27: Initial recommendation

For solo and small-team work, begin with cohesive modules, explicit dependencies, a clear state owner, and ordinary method calls. Introduce stronger separation where changes demonstrate a need. This is a working default to refine through implementation, not evidence that one architecture suits every project. No code or architecture was exercised during this research.

## References

- [Microsoft: architectural principles](https://learn.microsoft.com/en-us/dotnet/architecture/modern-web-apps-azure/architectural-principles)
