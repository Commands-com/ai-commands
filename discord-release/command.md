---
description: >
  Automatically generate release notes for Discord by analyzing Git commits since the last published release.
  Searches Discord for previous release notes, summarizes all commits since then, and creates formatted
  release notes ready for Discord posting. Works with any Git repository in your current directory.
allowed_tools:
  - Bash             # Git operations via command line
  - mcp-discord      # Discord integration for searching messages
  - filesystem       # File operations for saving release notes
---

## Arguments

```
/discord-release
guild_id=<discord-guild-id>
[channel_id=<discord-channel-id>]
[channel_name=<channel-name-for-releases>]
[days_back=<number-of-days>]
[format=<changelog|release|simple>]
[save_file=<path-to-save-release-notes>]
[exclude_patterns=<comma-separated-patterns>]
[create_channel=<true|false>]
```
*Defaults → `channel_name=release-notes  days_back=30  format=release  save_file=release-notes.md  exclude_patterns=chore,docs,style  create_channel=true`*

### Example

```
/discord-release 
guild_id="9876543210987654321"
channel_id="1234567890123456789"
format=changelog
save_file=CHANGELOG-v1.2.0.md
```

---

## Context – what the AI should do

1. **Initialize Git and Discord**
   * Use `/Bash` to verify Git repository exists: `git rev-parse --git-dir`
   * Check for valid Discord MCP server connection
   * Get repository information using:
     - `git config --get remote.origin.url` for repo URL
     - `basename -s .git $(git config --get remote.origin.url)` for project name
     - Check for package.json or README.md for project description

2. **Setup Release Channel**
   * If `channel_id` provided, use that channel
   * Otherwise, check if `channel_name` exists:
     - If exists and `create_channel=false`, use existing channel
     - If not exists and `create_channel=true`, create new channel:
       - Name: `channel_name` (default: "release-notes")
       - Permissions: Bot can write, everyone else read-only
       - Topic: "Automated release notes for {project_name}"
   * Store channel ID for future use

3. **Find Last Release Notes in Discord**
   * Use `/mcp-discord__search_messages` in the release channel
   * Search patterns to look for:
     - "Release Notes"
     - "Version" or "v" followed by semantic version
     - "What's New"
     - "Changelog"
   * Extract the date and version from the last release
   * If no previous release found:
     - Generate **Initial Project Summary** instead
     - Include project description from README/package.json
     - Analyze full commit history for major features
     - Create "Welcome to {project_name}" announcement

4. **Analyze Git History**
   * For regular releases:
     - Get the date of last release or use `days_back` parameter
     - Use `/Bash` to run: `git log --since="YYYY-MM-DD" --pretty=format:"%H|%an|%ae|%ad|%s" --date=short`
   * For initial project summary:
     - Get total commit count: `git rev-list --count HEAD`
     - Get contributor count: `git shortlog -sn | wc -l`
     - Get first commit date: `git log --reverse --format=%ad --date=short | head -1`
     - Analyze recent significant commits: `git log --oneline -50`
   * Parse commit messages for:
     - Conventional commit types (feat, fix, chore, docs, etc.)
     - Issue/PR references (#123)
     - Breaking changes (BREAKING CHANGE in commit body)

5. **Categorize and Filter Commits**
   * Group commits by type:
     - **Features**: New functionality (feat)
     - **Bug Fixes**: Issue resolutions (fix)
     - **Performance**: Optimization improvements (perf)
     - **Breaking Changes**: API/behavior changes
     - **Other Changes**: Remaining significant commits
   * Apply `exclude_patterns` to filter out unwanted commit types
   * Remove duplicate or similar commits

6. **Generate Release Notes or Project Summary**
   * For regular releases:
     - Determine version number:
       - Check for latest tag: `git describe --tags --abbrev=0 2>/dev/null`
       - Or read from package.json: `grep '"version"' package.json | cut -d'"' -f4`
       - Auto-increment based on commit types (major/minor/patch)
     - Create formatted release notes based on `format` parameter:
       - **changelog**: Detailed format with all commits
       - **release**: User-friendly format with summaries
       - **simple**: Bullet-point list of changes
   * For initial project summary:
     - Project name (from git remote or directory name)
     - Description (from README.md first paragraph or package.json)
     - Key features (analyze commit history for major additions)
     - Technology stack (detect from file extensions and config files)
     - Total commits and contributors
     - Project age and activity
     - Installation instructions (if found in README)

7. **Format for Discord**
   * Apply Discord markdown formatting:
     - Bold headers with **text**
     - Code blocks with triple backticks
     - Emoji for different change types:
       - ✨ Features
       - 🐛 Bug Fixes
       - ⚡ Performance
       - 💥 Breaking Changes
       - 🎉 Initial Release (for project summary)
   * Keep within Discord message limits (2000 characters)
   * If too long, create summary with link to full notes

8. **Save and Preview**
   * Write release notes to `save_file` using `/filesystem__write_file`
   * Generate a preview showing:
     - Version number (or "Initial Release")
     - Number of commits included
     - Major highlights
     - Full formatted release notes
   * Provide instructions for posting to Discord

9. **Channel Permissions Verification**
   * If new channel was created, verify permissions:
     - Bot has SEND_MESSAGES, EMBED_LINKS, READ_MESSAGE_HISTORY
     - @everyone has VIEW_CHANNEL but not SEND_MESSAGES
     - Provide command to fix permissions if needed

10. **Quality Checks**
    * Verify all significant changes are included
    * Check for any security-related commits that need highlighting
    * Ensure proper credit to contributors (if commit has Co-authored-by)
    * Validate markdown formatting

11. **Generate Posting Instructions**
    * Provide the Discord channel name/ID
    * Show the formatted message ready to copy
    * If channel was created, note it's read-only for others
    * Include any additional context (e.g., @mentions for major features)
    * Suggest timing for posting if applicable

> The command should make it effortless to maintain consistent, professional release notes
> in Discord by automating the tedious process of reviewing commits and formatting announcements.