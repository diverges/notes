# First Light: Agent workflow learnings

## 2026-09-30: Encode source before opening it for replacement

Validated on Windows with Python 3.14 during combat v2 implementation: pathlib.write_text without an explicit encoding used cp1252. Writing a new Unicode arrow raised UnicodeEncodeError after opening the untracked BootstrapSession.cs for writing, leaving it empty. The complete latest source was recovered from this session's earlier tool-read output, then restored as UTF-8. Subsequent compilation, 208 Play Mode tests and both combat pointer smoke checks passed.

For Windows source edits, always specify UTF-8 when reading/writing and encode the complete replacement before opening the destination. Prefer apply_patch or a temporary file followed by replacement. Do not rely on Git recovery when the project is untracked. Recover from a verified recent source copy; never reconstruct missing methods from memory. This is an agent file-editing lesson, not a gameplay or Unity runtime issue.
