---
name: product-team
description: Coordinate designer, engineer, architect, and product manager subagents for a requested team review, feature plan, or implementation. Use when the user asks for these roles together or invokes product-team; ordinary solo tasks do not need this workflow.
---

# Product team

Keep the parent agent accountable for the integrated result. Use the roles needed by the request; use all four when the user asks for the full team. PM means product manager. Follow the user's requested mode: a review or plan does not authorize implementation.

Apply the shared AGENTS.md guidance on grounding recommendations and resolving consequential choices. Subagents surface unresolved choices to the parent with evidence, a recommendation, and tradeoffs. The parent coordinates questions to the user and shares answers with affected roles so they do not ask again. Continue independent work while dependent decisions are pending.

## Delegate

Give each subagent a bounded assignment containing the user goal, relevant project context and source paths, constraints, expected deliverable, success criteria, and permitted file ownership. Pass relevant AGENTS.md instructions explicitly when context is not inherited. Share the same requirements and decisions with every affected role.

Use the matching custom agent when the runtime exposes it. Otherwise read the corresponding file below and include its developer_instructions in the subagent's assignment; a role name alone is insufficient. These files are also the maintained sources for the personal custom agents.

- PM: `references/pm.toml`
- Designer: `references/designer.toml`
- Architect: `references/architect.toml`
- Engineer: `references/engineer.toml`

For feature work, establish scope and acceptance criteria first, then run independent design and architecture work concurrently where useful. Give the engineer the reconciled decisions before dependent implementation. For reviews, assign independent questions directly. Adapt to existing specifications instead of repeating completed planning. Respect available concurrency; queue roles when needed. Do not create separate user-facing chats for these subtasks.

## Coordinate and finish

Give concurrent writers disjoint file ownership. Assign the PM ownership of the applicable feature spec, bug record, or GDD when documentation writes are within scope; the parent remains accountable for requirements and integrates results. The PM reads the shared templates, captures the initial scope, and updates the authoritative document from other roles' handoffs. Avoid simultaneous parent and PM edits to that document. For read-only requests, the PM reports gaps and proposed updates instead. Surface disagreements with evidence and a recommendation; ask the user only when a consequential choice cannot be resolved from their instructions. Continue already authorized work without adding stage approval gates.

Require concise handoffs covering result, evidence and checks actually performed, uncertainties or risks, and what the next role needs. Omit empty sections. Check returned work against the assignment and request a targeted correction if needed. Verify the integrated outcome against acceptance criteria; agreement between agents does not establish correctness or subjective design acceptance.

Before reporting completion, have the PM reconcile documentation with delivered behavior and verification evidence, including remaining unchecked tasks and unresolved acceptance. The parent verifies this handoff along with the implementation. Report the authoritative document path and meaningful verification limits. Save consequential decisions and state in the project's established memory, following its existing rules, without duplicating the specification.

## Invocation examples

- "Use product-team to review this proposal. Return recommendations without editing the project."
- "Use product-team to implement this feature and verify its acceptance criteria."
- "Use the designer and architect roles to assess this feature before implementation."
