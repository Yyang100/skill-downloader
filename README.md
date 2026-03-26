# skill-downloader

> Search and install OpenClaw skills from trusted sources with security checks and proper directory handling.

## Source

- Maintainer: `Yyang100`
- Canonical repository: `https://github.com/Yyang100/skill-downloader`
- ClawHub page: `https://clawhub.ai/yyang100/skill-downloader`

## Features

- 🔍 **Search skills**: Find skills by name or description (triggers on "find X skill", "search for Y", etc.)
- 📥 **Install skills**: Download and install with security checks (triggers on "install X skill", "download Y", etc.)
- 🛡️ **Security first**: Runs `skill-scanner` and `skill-vetting` before installation
- 📁 **Flexible installation**: 
  - Global install to `~/.openclaw/skills` (when user mentions "global")
  - Local install to current workspace `./skills` (default)
- 📊 **Smart recommendations**: Shows popularity, descriptions, and recommends the best match
- 🚫 **No symlinks**: Copies full source files to target directory (no linking)

## Trusted Sources (by priority)

1. `https://clawhub.ai/` - Official ClawHub skill repository
2. `https://skills.sh/` - Open agent skills ecosystem registry (via `npx skills find`)
3. `https://github.com/anthropics/skills` - Anthropic example skills repository

You can add more trusted sources by editing `SKILL.md`.

## Maintenance note

When updating this skill's behavior or source priority, keep `SKILL.md` and `README.md` in sync.

## Security and runtime model

- This skill may access trusted registries and repositories including `https://clawhub.ai/`, `https://skills.sh/`, and relevant GitHub repositories.
- It requires a network connection, `git`, `curl`, Node.js/npm, and `npx` availability when using the `skills.sh` fallback path.
- `npx skills find <query>` is a fallback mechanism for `skills.sh` discovery, not the default search path.
- The skill must not download or install anything without explicit user confirmation.
- Downloaded skill contents should be reviewed before installation, and source files should be copied rather than symlinked into the target directory.
- The skill is intended to help users inspect and install third-party skills; it is not intended to execute arbitrary unrelated packages or bypass user review.

## When to use this skill

Use this skill whenever the user:
- Asks to "find", "search", or "look for" a skill (search mode)
- Requests to "download", "install", or "add" a skill (install mode)
- Asks "is there a skill that can..." or "有没有...技能"

## Core Rules

1. **Always confirm before downloading** - Never download without explicit user instruction
2. **Security first** - Always run security checks; abort if issues are found
3. **Search in priority order** - ClawHub first, then skills.sh, then GitHub
4. **Full source copy** - Copy all source files, do not use symlinks
5. **Temporary directory** - Clone to `/tmp/skill-downloader/` before installation
6. **Cleanup** - Remove temporary files after installation

## Workflow

### Mode A: Search Only
1. **Detect search intent** - User asks to find/search a skill
2. **Search trusted sources in priority order** - Search ClawHub first, then fall back to `npx skills find` for skills.sh, then other trusted sources
3. **Present results** - Show table with name, popularity, URL
4. **Wait for user** - Ask if they want to install any result

### Mode B: Installation
1. **Capture request** - Get skill name and check if global installation is requested
2. **Find the skill** - Search ClawHub first, then other trusted sources in priority order; show results with recommendations
3. **Security check** - Scan for malware and evaluate utility
4. **Install** - Copy full source to target directory, verify structure
5. **Cleanup** - Remove temporary files

## Installation

Install globally in your `~/.openclaw/skills/`:

```bash
npx skills add Yyang100/skill-downloader@skill-downloader -g
```

## Requirements

- git
- curl
- Node.js/npm
- `npx` (used only as a fallback for `skills.sh` discovery)
- network access to trusted registries/repositories
- `skill-scanner` (optional but recommended)
- `skill-vetting` (optional but recommended)

## License

MIT
