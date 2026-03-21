# skill-downloader

> Search and install OpenClaw skills from trusted sources with security checks and proper directory handling.

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

1. `https://skills.sh/` - Open agent skills ecosystem registry (via `npx skills find`)
2. `https://clawhub.ai/` - Official ClawHub skill repository
3. `https://github.com/anthropics/skills` - Anthropic example skills repository

You can add more trusted sources by editing `SKILL.md`.

## When to use this skill

Use this skill whenever the user:
- Asks to "find", "search", or "look for" a skill (search mode)
- Requests to "download", "install", or "add" a skill (install mode)
- Asks "is there a skill that can..." or "有没有...技能"

## Core Rules

1. **Always confirm before downloading** - Never download without explicit user instruction
2. **Security first** - Always run security checks; abort if issues are found
3. **Search in priority order** - skills.sh first, then ClawHub, then GitHub
4. **Full source copy** - Copy all source files, do not use symlinks
5. **Temporary directory** - Clone to `/tmp/skill-downloader/` before installation
6. **Cleanup** - Remove temporary files after installation

## Workflow

### Mode A: Search Only
1. **Detect search intent** - User asks to find/search a skill
2. **Run npx skills find** - Search skills.sh
3. **Present results** - Show table with name, popularity, URL
4. **Wait for user** - Ask if they want to install any result

### Mode B: Installation
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
