# <Spec Title>

- **Tracking:** <Jira key / issue link, or "none">
- **Code repository:** <repo name; its local path is per machine — plain text, never a link>
- **Codebase:** <name of this codebase's agent-docs folder — read before planning, update as you learn>
- **Working branch:** <branch used for the changes to be reviewed>
- **Status:** Draft <!-- Draft → Spec-ready → In progress → In review → Done -->

## Overview

_What is this about and why does it matter?_

## Context

_Relevant background, prior decisions, current behavior. Inline the substance — don't
point at other docs. Cite code as plain text: `path/to/File.cs:42`._

## Proposal

_Proposed approach for the code changes (which live in the code repo above)._

- **In scope:** _what this includes._
- **Out of scope:** _what it deliberately excludes._

## Acceptance Criteria

_Testable claims — one behavior each, no ambiguity about trigger or response.
Prefer Given/When/Then (or EARS). These are the spec's contract; the Test Plan
below verifies them._

- **Given** <initial state> **when** <trigger> **then** <expected result>.

## Test Plan

_How each acceptance criterion above gets validated._

- **Unit tests:** _what to cover in isolation._
- **Integration tests:** _how components/systems are verified together._
- **Manual tests:** _steps to verify by hand._

## Open Questions

_What is not yet decided. Write `TBD — <what's needed to resolve>`; an explicit unknown
beats a confident guess._

- _<question>_

## Tasks

- [ ] ...

## Verification

_Checks actually run and their results; what remains unverified or awaits human review.
Fill in as work lands — never record a check that didn't run._

- _<check> — <result> (<branch/commit>)_

## Definition of Done

- [ ] Acceptance criteria are complete and testable.
- [ ] Proposal + Test Plan cover every acceptance criterion.
- [ ] Verification records the checks that ran and anything left unverified.
- [ ] Durable decisions/lessons recorded in the codebase's agent docs.
- [ ] Traceability below is filled in.
- [ ] Status set to the correct lifecycle value.

## Traceability

- **Tracking:** <Jira key / issue>
- **Code repo PR(s):** <pull request(s) implementing this>
- **Commits:** <SHAs, once landed>

## References

_External URLs and ongoing notes. No links to other docs in this repo — inline what
matters._
