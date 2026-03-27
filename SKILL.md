---
name: skill-downloader
version: 0.1.12
description: Discover, review, and install OpenClaw skills from trusted sources such as ClawHub, skills.sh, and GitHub. Defaults to search-and-review mode; any download or installation requires explicit user confirmation and source inspection.
author: Yyang100
triggers:
  - "search"
  - "find"
  - "look for"
  - "查找"
  - "搜索"
  - "is there a skill"
  - "有没有.*技能"
  - "download skill"
  - "install skill"
  - "添加技能"
  - "下载技能"
compatibility: requires git, curl, network access, and the ability to review downloaded source files locally
permissions:
  fileRead: true - Read candidate skill files for review before installation
  fileWrite: true - Write reviewed skill files to the chosen installation directory
  network: true - Access trusted skill registries such as ClawHub, skills.sh, and GitHub for discovery and review
  shell: true - Use trusted local tools such as git and curl to inspect or retrieve skill source when explicitly approved
trustScore: 90
category: Utility
---

# Skill Downloader

Discover, review, and install OpenClaw skills from trusted sources with transparent review and careful installation handling.

By default this skill operates in search-and-review mode. It does not automatically execute third-party code, and any download or installation step requires explicit user approval plus local source inspection.

## Source

- Maintainer: `Yyang100`
- Canonical repository: `https://github.com/Yyang100/skill-downloader`
- ClawHub page: `https://clawhub.ai/yyang100/skill-downloader`

## Trusted download sources

Default trusted sources (ordered by priority):
1. `https://clawhub.ai/` - Official ClawHub skill repository
2. `https://skills.sh/` - Open agent skills ecosystem registry (manual discovery or user-provided links)
3. `https://github.com/anthropics/skills` - Anthropic example skills repository

Users can add additional sources later by updating this list in SKILL.md.

## Security and runtime model

- This skill may access trusted registries and repositories including `https://clawhub.ai/`, `https://skills.sh/`, and relevant GitHub repositories.
- It requires network access plus `git`, `curl`, and the ability to inspect downloaded source files locally; if `clawhub` CLI is available, prefer it for ClawHub-hosted skills.
- Default behavior is discovery and review, not automatic installation.
- Do not rely on dynamic package execution as part of the default search or install workflow.
- Do not download or install anything without explicit user confirmation.
- Do not automatically execute third-party repositories or package scripts.
- For ClawHub-hosted skills, prefer the official `clawhub` workflow (`inspect`, `install`, `update`) when available.
- If the official ClawHub workflow is unavailable, use an agent-managed transparent file installation workflow with a unique per-run temporary working directory.
- Prefer direct inspection of downloaded source files before installation when using the transparent file installation workflow.
- Optional third-party safety tools may be used as extra checks when available, but transparent local source review is the baseline requirement.

## Core rules

1. **Always confirm before downloading**: Do not download any skill without explicit instruction from the user. If the user just asks about skills or where to find them, recommend sources but wait for a clear download command.
2. **Security review first**: Before installation, read the candidate skill content and check for suspicious behavior such as unauthorized data collection, secret exfiltration, destructive commands, hidden execution paths, or malicious obfuscation.
3. **Stop on risk**: If suspicious or malicious content is detected, abort installation and explain the specific concern.
4. **Search order**: Search for the skill in the default trusted sources in priority order, with `https://clawhub.ai/` first by default. If not found, ask the user if they can provide an alternative download URL.
5. **Recommend when uncertain**: If unsure where to find the skill, list the available trusted sources and ask the user which one to use.
6. **Multiple results handling**: If multiple matching skills are found:
   - Collect skill name, description, popularity/install count (if available), source repository, and skills.sh page URL
   - Organize into a clear list/table for the user
   - For each skill, add a short recommendation note explaining its best use case
   - After listing all skills, give a summary recommendation of which one to choose overall, prioritizing skills that are secure, actively maintained, and have high functional matching

## Installation directory rules

Follow these rules strictly for where to install the skill:

### Global installation
- When the user explicitly mentions "global", "system-wide", or similar words indicating global installation:
  - **macOS/Linux**: `~/.openclaw/skills/`
  - **Windows**: `%USERPROFILE%\.openclaw\skills\`
  - **Other platforms**: Follow the XDG base directory spec if defined, otherwise use `~/.openclaw/skills/`

### Local installation (default)
- When the user does NOT mention "global" or similar words:
  - Install to: `{current_workspace}/skills/`
  - If no workspace is detected, default to `~/.openclaw/workspace/skills/`

## Workflow

### Mode A: Search Only (no installation)

When user only asks to "search", "find", or "look for" a skill (no download/install intent):

1. **Detect search intent** — User asks "find X skill", "search for Y", "is there a skill that can..."
2. **Search trusted sources in priority order** — Prefer searching `https://clawhub.ai/` first; if needed, inspect trusted repository pages or user-provided links for skills.sh and then other trusted sources
3. **Present results** — Show results in table format with:
   - Skill name and owner/repo
   - Install count (popularity)
   - Skills.sh URL
   - Brief recommendation
4. **Wait for user** — Ask if they want to install any of the results
5. **If yes** → Continue to Mode B (Installation)
6. **Done** — End here if user just wants to browse

### Mode B: Installation (download + install)

Use the official ClawHub installation workflow for ClawHub-hosted skills whenever the `clawhub` CLI is available. Use the transparent file installation workflow only when the official ClawHub workflow is unavailable or when the source is outside ClawHub.

1. **Capture request**
   - Extract the skill name to download
   - Check if user requested global installation
   - Confirm this is an explicit download request (if not, ask for confirmation)
   - **Check existing installation**:
     - For global installation: check if skill already exists at `~/.openclaw/skills/<skill-name>/`
     - For local installation: check if skill already exists at `{current_workspace}/skills/<skill-name>/`
     - If skill already exists: inform the user the skill is already installed, ask whether they want to **update** it to the latest version, then proceed accordingly

2. **Find the skill**
   - For `clawhub.ai` source: search ClawHub first
   - For `skills.sh` source: inspect the relevant registry/repository pages manually or use user-provided links
   - For other sources: search manually in the configured trusted repositories
   - Search in priority order (ClawHub first)
   - If multiple matching skills found: organize information (name, description, popularity, source) and recommend → wait for user selection
   - If the selected skill is hosted on ClawHub and `clawhub` CLI is available: inspect it with the official CLI before installation
   - If the transparent file installation workflow is needed: obtain the repository URL and download or clone it into a unique per-run temporary working directory under the system temp location
   - If not found: inform user, suggest alternative sources, wait for user-provided URL

3. **Internal sensitive content check**
   - For the transparent file installation workflow: download to a unique per-run temporary location and do not use symlink-based installation
   - After downloading to a temporary location, automatically read all text files to check for sensitive or malicious content
   - Look for patterns such as unauthorized private data collection, secret or credential exfiltration, destructive system commands, hidden execution paths, malicious obfuscation, or crypto-mining behavior
   - If suspicious or malicious content is detected: delete the download, inform user of the specific issues, stop
   - If no suspicious content is found: proceed to installation
   - If the official `clawhub` workflow is available for a ClawHub-hosted skill, prefer `clawhub inspect` before `clawhub install`
   - If additional third-party safety tools are available and the user wants extra checks, run them as an extra verification layer after the baseline manual review

4. **Install / Update**
   - If the selected skill is hosted on ClawHub and `clawhub` CLI is available:
     - For inspection: use `clawhub inspect <slug>`
     - For global install: use `clawhub install <slug> --dir ~/.openclaw/skills`
     - For local install: use `clawhub install <slug> --dir {current_workspace}/skills`
     - For updates, prefer the official ClawHub update/install workflow instead of a custom copy-based update flow
   - Otherwise, use the transparent file installation workflow:
     - Create the target directory if it doesn't exist
     - If updating existing skill:
       - Backup old version to target directory with `.bak` suffix (e.g. `skill-name.bak.<timestamp>`)
       - **Preserve skill-generated data**:
         - Keep all non-source files that are not part of the original download (e.g. cached data, user configuration, generated outputs)
         - Only delete or overwrite files that exist in the new downloaded version
         - Do not delete empty directories or directories that contain user data
     - Copy the full source files from temp to target directory (**do NOT use symlinks**)
     - Overwrite only existing source files, leave untouched any additional user/data files
     - Verify the skill structure (ensures SKILL.md exists)
   - If updated: inform user that the update is complete, mention the backup location and that existing user data has been preserved
   - If installed: inform user of successful new installation:
     - Where it was installed
     - How to use it
     - Any additional setup required

5. **Cleanup**
   - Remove the unique per-run temporary working directory and any temporary download files created for that run

6. **Post-installation prompt**
   - After successful installation/update and cleanup, ask the user: "Would you like me to explain the functions and usage of this new skill for you?"
   - If user agrees, read the skill's SKILL.md and summarize the core functions, usage flow and key notes to the user

7. **Done**

## Adding new sources

Users can add new trusted sources by appending to the *Trusted download sources* list above. Maintain the numbered order.

## Examples

**Example 1: Global install**
> User: Download the weather skill globally
- Skill name: `weather`
- Install path: `~/.openclaw/skills/weather`
- Run security checks → install

**Example 2: Local install**
> User: Can you install skill-creator in this workspace
- Skill name: `skill-creator`
- Install path: `./skills/skill-creator`
- Run security checks → install

**Example 3: Not found**
> User: Download my-custom-skill
- Not found in default sources → "I couldn't find 'my-custom-skill' in the default trusted sources. Can you provide a download URL?"

**Example 4: Uncertain request**
> User: Where can I find a good markdown processing skill?
- "You can find skills on https://clawhub.ai/, https://skills.sh/, or https://github.com/anthropics/skills. Would you like me to search for and download a markdown processing skill?"
