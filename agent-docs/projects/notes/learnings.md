# Agent learnings

## 2026-09-27: Route queries before loading detailed knowledge

- Status: User-confirmed preference; retrieval effectiveness is unverified.
- Context: The initial Unity AI research emphasized coding assistance; the user clarified that the intended focus was agent files and workflow that select relevant sections without unnecessary context.
- Evidence: Explicit user clarification in the Unity knowledge-base session.
- Action: Keep entry points concise, choose sections matching the task, and expand only for a concrete information gap. Store technical guidance in its subject note rather than detailed agent instructions.
- Applicability: Knowledge organization for future Unity work; validate with real queries before claiming reduced context use.

### 2026-09-27: Correction to link-heavy routing

The user found the generated notes too dependent on links and requested compact, self-contained Markdown that preserves findings like a diary. The earlier emphasis on routes between notes is superseded by topic directories and independently readable sections. Include the situation, finding, rationale, relevant warning, and evidence limits in the section itself; keep links for navigation and provenance. Brief context repetition is acceptable when it prevents mandatory cross-note reading. This preference is confirmed; reduced context usage remains unmeasured.

## 2026-09-26: Resolve shared memory independently of the working directory

- Status: Validated failure report; corrective guidance awaits verification in another project.
- Context: Global guidelines referenced shared notes using relative `agent-docs/` paths.
- Evidence: The user reported that a chat working in `D:\Repos\GMTK-Jam-2023` looked for that repository's nonexistent `agent-docs` folder and acknowledged previously describing an expected path as though it existed.
- Action: Name the absolute shared root in global guidelines, verify its index before use, and use absolute paths for file operations. Use the profile link to discover the root on other machines. Verify paths before reporting them as available.
- Applicability: Shared agent memory accessed from multiple repositories. Loading global instructions does not prove that a chat resolved their supporting files correctly.

## 2026-09-26: Shared guideline links on Windows

- Status: Validated on this machine.
- Context: Expose this repository's guidelines and templates through the Codex profile for other projects.
- Evidence: Agent-run symbolic-link creation failed with `Administrator privilege required for this operation`. The user ran the two creation commands. Subsequent inspection confirmed both symbolic-link targets, matching file hashes for the guidelines, index, and templates, and no global override. The user then confirmed loading from another project.
- Action: Inspect existing profile entries before setup. If Windows refuses link creation, follow the setup skill's recovery guidance and verify the links afterward. Sandbox approval alone did not supply the Windows privilege required in this attempt.
- Applicability: This Windows setup. Successful reads do not establish write permission from another project's sandbox or prove future sessions saved notes.
