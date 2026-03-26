# Skill Downloader

> Discover and install OpenClaw skills from trusted sources with transparent review and directory-safe installation handling.

## Source

- Maintainer: `Yyang100`
- Canonical repository: `https://github.com/Yyang100/skill-downloader`
- ClawHub page: `https://clawhub.ai/yyang100/skill-downloader`

## Features

- 🔍 **Search skills**: Discover skills by name or description (triggers on "find X skill", "search for Y", etc.)
- 📥 **Install skills**: Install trusted skills with transparent review and safe installation steps (triggers on "install X skill", "download Y", etc.)
- 🛡️ **Security-aware workflow**: Prioritizes manual source review before installation; optional extra checks can be run when available
- 📁 **Flexible installation**: 
  - Global install to `~/.openclaw/skills` (when user mentions "global")
  - Local install to current workspace `./skills` (default)
- 📊 **Smart recommendations**: Shows popularity, descriptions, and recommends the best match
- 🚫 **No symlinks**: Copies full source files to target directory (no linking)

## Trusted Sources (by priority)

1. `https://clawhub.ai/` - Official ClawHub skill repository
2. `https://skills.sh/` - Open agent skills ecosystem registry (manual discovery or user-provided links)
3. `https://github.com/anthropics/skills` - Anthropic example skills repository

You can add more trusted sources by editing `SKILL.md`.

## Maintenance note

When updating this skill's behavior or source priority, keep `SKILL.md` and `README.md` in sync.

## Security and runtime model

- This skill may access trusted registries and repositories including `https://clawhub.ai/`, `https://skills.sh/`, and relevant GitHub repositories.
- It requires a network connection, `git`, `curl`, and access to trusted registries/repositories; if `clawhub` CLI is available, prefer it for ClawHub-hosted skills.
- It does not rely on dynamic package execution as part of the default search or install workflow.
- The skill must not download or install anything without explicit user confirmation.
- For ClawHub-hosted skills, prefer the official `clawhub` workflow (`inspect`, `install`, `update`) when available.
- If the official ClawHub workflow is unavailable, use an agent-managed transparent file installation workflow with unique per-run temporary directories.
- Downloaded skill contents should be reviewed before installation, and source files should be copied rather than symlinked into the target directory unless the official ClawHub workflow is used.
- The skill is intended to help users inspect and install third-party skills; it is not intended to execute arbitrary unrelated packages or bypass user review.

## When to use this skill

Use this skill whenever the user:
- Asks to "find", "search", or "look for" a skill (search mode)
- Requests to "download", "install", or "add" a skill (install mode)
- Asks "is there a skill that can..." or "有没有...技能"

## Core Rules

1. **Always confirm before downloading** - Never download without explicit user instruction
2. **Security-aware review** - Always review downloaded source contents before installation; if issues are found, abort
3. **Search in priority order** - ClawHub first, then skills.sh, then GitHub
4. **Prefer official ClawHub installation** - For ClawHub-hosted skills, use `clawhub inspect/install/update` when the CLI is available
5. **Transparent fallback workflow** - If official ClawHub installation is unavailable, use an agent-managed transparent file installation workflow
6. **Temporary working directory** - Use a unique per-run temporary working directory for transparent file installation
7. **Cleanup** - Remove temporary files after installation

## Workflow

### Mode A: Search Only
1. **Detect search intent** - User asks to find/search a skill
2. **Search trusted sources in priority order** - Search ClawHub first, then inspect trusted repository pages or user-provided links for skills.sh and other sources
3. **Present results** - Show table with name, popularity, URL
4. **Wait for user** - Ask if they want to install any result

### Mode B: Installation
1. **Capture request** - Get skill name and check if global installation is requested
2. **Find the skill** - Search ClawHub first, then other trusted sources in priority order; show results with recommendations
3. **Choose installation workflow** - Prefer the official `clawhub` workflow for ClawHub-hosted skills; otherwise use the transparent file installation workflow
4. **Security check** - Review downloaded source contents and evaluate utility when using the transparent file installation workflow
5. **Install** - Install via `clawhub` when available for ClawHub-hosted skills, otherwise copy full source to target directory and verify structure
6. **Cleanup** - Remove the unique per-run temporary working directory used by the transparent file installation workflow

## Installation

Recommended installation target for a global install:

```text
~/.openclaw/skills/skill-downloader
```

For ClawHub-hosted skills, prefer the official CLI workflow when available:

```bash
clawhub inspect <slug>
clawhub install <slug> --dir ~/.openclaw/skills
```

If `clawhub` is unavailable, fall back to the transparent file installation workflow and place the reviewed full source in the appropriate skills directory.

## Requirements

- git
- curl
- network access to trusted registries/repositories
- ability to review downloaded text/source files locally
- `skill-scanner` (optional additional check, when available)
- `skill-vetting` (optional additional check, when available)

## License

MIT
