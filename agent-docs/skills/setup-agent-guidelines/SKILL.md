---
name: setup-agent-guidelines
description: Set up or check shared AGENTS.md and agent-docs links on Windows using PowerShell. Use when configuring another computer or checking whether personal guidelines are connected.
---

# Set up shared guidelines

Use the PowerShell commands below. For a check-only request, skip creation. Keep existing files and incorrect links intact; explain conflicts before changing them.

## Choose the folders

The notes repository's path differs per machine (for example `D:\Repos\notes`). Confirm it with the user, or use the folder containing this repository's `AGENTS.md`, and set it below before running anything.

```powershell
$ErrorActionPreference = 'Stop'
$sharedRoot = '<path to the notes repository on this machine>'
$codexRoot = if ($env:CODEX_HOME) { $env:CODEX_HOME } else {
    Join-Path $env:USERPROFILE '.codex'
}
```

## Check

Confirm the shared files are readable:

```powershell
'AGENTS.md', 'agent-docs\INDEX.md',
'agent-docs\template\spec.md', 'agent-docs\template\bug.md' |
    ForEach-Object {
        $pathToCheck = Join-Path $sharedRoot $_
        Get-Item -LiteralPath $pathToCheck | Select-Object FullName, Length
        Get-Content -LiteralPath $pathToCheck | Select-Object -First 3
    }
```

Inspect existing links, files, and overrides:

```powershell
Get-ChildItem -LiteralPath $codexRoot -Force |
    Where-Object Name -In 'AGENTS.md', 'agent-docs', 'AGENTS.override.md' |
    Select-Object Name, LinkType, Target
```

If the Codex folder does not exist, report that setup is missing. Both links should point to the corresponding items in the selected shared folder. An ordinary file, wrong target, or broken link needs attention. If AGENTS.override.md exists, read it: a nonempty override takes precedence over AGENTS.md.

## Create missing links

Run only for a setup request, after checking for conflicts. Run each New-Item link command only if its destination is absent. Never use -Force to replace an existing entry.

```powershell
New-Item -ItemType Directory -Path $codexRoot -Force | Out-Null
New-Item -ItemType SymbolicLink -Path (Join-Path $codexRoot 'AGENTS.md') -Target (Join-Path $sharedRoot 'AGENTS.md')
New-Item -ItemType SymbolicLink -Path (Join-Path $codexRoot 'agent-docs') -Target (Join-Path $sharedRoot 'agent-docs')
```

If Windows refuses symbolic-link creation, report it. The user may need Developer Mode or an administrator PowerShell session. Do not change those settings, elevate automatically, or substitute copied files. Report partial creation accurately.

## Verify

Repeat the link inspection and read through the links:

```powershell
Get-Content -LiteralPath (Join-Path $codexRoot 'AGENTS.md') -TotalCount 5
Get-Content -LiteralPath (Join-Path $codexRoot 'agent-docs\INDEX.md')
Get-Item -LiteralPath (Join-Path $codexRoot 'agent-docs\template\spec.md'), (Join-Path $codexRoot 'agent-docs\template\bug.md')
```

Report whether both targets are correct and readable, and whether an override shadows the guidelines. Do not claim that a running chat has loaded them.

Start a fresh chat in another project and ask: "Read my shared guidelines and locate my spec and bug templates."

On a new machine, open this SKILL.md by path and ask the agent to follow it, or run the commands yourself. The skill cannot discover itself before installation. Link only AGENTS.md and agent-docs, not the whole .codex folder. Links neither configure Drive sync nor grant write permissions.
