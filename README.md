# skill-downloader

> Download and install OpenClaw skills from trusted sources with security checks and proper directory handling.

## Features

- 🔍 **Multi-source search**: Search [skills.sh](https://skills.sh/), ClawHub, and GitHub repositories in priority order
- 🛡️ **Security first**: Runs `skill-scanner` and `skill-vetting` before installation
- 📁 **Flexible installation**: 
  - Global install to `~/.openclaw/skills` (when user mentions "global")
  - Local install to current workspace `./skills` (default)
- 📊 **Smart recommendations**: Shows popularity, descriptions, and recommends the best match
- 🚫 **No symlinks**: Copies full source files to target directory (no linking)
- 👍 **Fallback handling**: Asks for user confirmation when security tools are missing

## Trusted Sources (by priority)

1. `https://skills.sh/` - Open agent skills ecosystem registry (via `npx skills find`)
2. `https://clawhub.ai/` - Official ClawHub skill repository
3. `https://github.com/anthropics/skills` - Anthropic example skills repository

You can add more trusted sources by editing `SKILL.md`.

## When to use this skill

Use this skill whenever the user:
- Requests to download, install, or add third-party skills
- Asks where to find skills
- Wants to install a skill from a known source

## Core Rules

1. **Always confirm before downloading** - Never download without explicit user instruction
2. **Security first** - Always run security checks; abort if issues are found
3. **Search in priority order** - skills.sh first, then ClawHub, then GitHub
4. **Full source copy** - Copy all source files, do not use symlinks
5. **Temporary directory** - Clone to `/tmp/skill-downloader/` before installation
6. **Cleanup** - Remove temporary files after installation

## Workflow

1. **Capture request** - Get skill name and check if global installation is requested
2. **Find the skill** - Search in trusted sources, show results with recommendations
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
- `skill-scanner` (optional but recommended)
- `skill-vetting` (optional but recommended)

## License

MIT
