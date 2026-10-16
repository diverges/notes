# <Bug Title>

- **Tracking:** <Jira key / issue link, or "none">
- **Code repository:** <repo name; its local path is per machine — plain text, never a link>
- **Codebase:** <name of this codebase's agent-docs folder — read before planning, update as you learn>
- **Working branch:** <branch used for the fix to be reviewed>
- **Severity:** <blocker | critical | major | minor>
- **Status:** Open <!-- Open → Investigating → Root-caused → Fix proposed → In review → Done -->

## Summary

_One or two sentences: what's broken and its impact. One issue only — keep unrelated
improvements out._

## Environment

_Where it reproduces: version/build, platform, config, etc._

## Steps to Reproduce

1. ...

**Expected:** _what should happen._

**Actual:** _what actually happens, how often, and the evidence (logs, screenshots)._

## Root Cause

_The confirmed cause, or a clearly labelled **Hypothesis:** until it's confirmed._

## Fix

_Proposed / applied fix (code changes live in the code repo above), and the behavior
it risks regressing._

## Test Plan

_Strategy for validating the fix and preventing regression. State the fixed
behavior as a testable claim (Given/When/Then) so it doubles as a regression test._

- **Regression claim:** **Given** <repro state> **when** <trigger> **then** <fixed result>.
- **Unit tests:** _what to cover in isolation._
- **Integration tests:** _how components/systems are verified together._
- **Manual tests:** _steps to verify by hand._

## Tasks

- [ ] ...

## Verification

_Checks actually run and their results; what remains unverified or awaits human review.
Fill in as work lands — never record a check that didn't run._

- _<check> — <result> (<branch/commit>)_

## Definition of Done

- [ ] Root cause identified and documented above.
- [ ] Fix verified against the repro and covered by a regression test.
- [ ] Verification records the checks that ran and anything left unverified.
- [ ] Durable lessons recorded in the codebase's agent docs (`gotchas.md`).
- [ ] Traceability below is filled in.
- [ ] Status set to the correct lifecycle value.

## Traceability

- **Tracking:** <Jira key / issue>
- **Code repo PR(s):** <pull request(s) fixing this>
- **Commits:** <SHAs, once landed>

## References

_External URLs, related bugs/tickets, ongoing notes._
