# Discord Release Generator

Automatically generate professional release notes for Discord by analyzing Git commits since your last published release. This command searches your Discord channel for previous release notes, summarizes all commits since then, and creates beautifully formatted release notes ready for posting.

## Features

- 🔍 **Automatic Detection**: Finds your last release notes in Discord automatically
- 📊 **Smart Categorization**: Groups commits by type (features, fixes, performance, etc.)
- 🎨 **Discord-Optimized**: Formats with Discord markdown and appropriate emojis
- 🔧 **Customizable**: Multiple format options and filtering capabilities
- 📝 **Version Management**: Auto-detects or suggests version numbers
- 💾 **Export Options**: Save release notes for documentation
- 🎉 **Initial Release Support**: Generates project summary when no previous releases exist
- 🔒 **Auto-Channel Creation**: Creates read-only release channel with proper permissions

## Prerequisites

This command requires the following MCP servers:

1. **Discord** - For searching previous release notes and posting
   ```bash
   claude mcp add mcp-discord -- docker run -i --rm mcp/mcp-discord:latest
   ```

2. **Filesystem** - For saving release notes
   ```bash
   claude mcp add filesystem -- docker run -i --rm mcp/filesystem:latest
   ```

**Note**: Git operations are handled through Claude Code's built-in Bash tool, so no separate Git MCP server is needed. The command works with whatever Git repository you're currently in.

## Installation

1. Import this command to your Commands.com account through GitHub
2. Ensure required MCP servers are installed (see Prerequisites)
3. Configure Discord MCP server with your bot token
4. Navigate to any Git repository in Claude Code

## Usage

### Basic Usage

Generate release notes by searching the last 30 days:

```bash
/discord-release guild_id="YOUR_DISCORD_GUILD_ID"
```

### Specify Discord Channel

Target a specific Discord channel:

```bash
/discord-release guild_id="YOUR_DISCORD_GUILD_ID" channel_id="1234567890123456789"
```

### Auto-Create Release Channel

Create a dedicated read-only channel for releases:

```bash
/discord-release guild_id="YOUR_DISCORD_GUILD_ID" channel_name="release-notes" create_channel=true
```

### Custom Time Range

Look back a specific number of days:

```bash
/discord-release guild_id="YOUR_DISCORD_GUILD_ID" days_back=14
```

### Format Options

Choose from different formatting styles:

```bash
# Detailed changelog format
/discord-release guild_id="YOUR_DISCORD_GUILD_ID" format=changelog

# User-friendly release format (default)
/discord-release guild_id="YOUR_DISCORD_GUILD_ID" format=release

# Simple bullet-point list
/discord-release guild_id="YOUR_DISCORD_GUILD_ID" format=simple
```

### Save to File

Export release notes to a file:

```bash
/discord-release guild_id="YOUR_DISCORD_GUILD_ID" save_file=releases/v1.2.0.md
```

### Exclude Commit Types

Filter out certain commit types:

```bash
/discord-release guild_id="YOUR_DISCORD_GUILD_ID" exclude_patterns=chore,docs,style,test
```

## Example Output

### Regular Release
```markdown
**📢 Release Notes - v1.2.0**

*Released on July 22, 2025*

**✨ New Features**
• Added Discord release generator for automated changelog creation
• Implemented smart commit categorization with conventional commit support
• Enhanced MCP server discovery with 100+ available integrations

**🐛 Bug Fixes**
• Fixed issue with Git commit parsing for merge commits
• Resolved Discord message search pagination bug
• Corrected version number auto-increment logic

**⚡ Performance Improvements**
• Optimized commit analysis for large repositories (2x faster)
• Reduced memory usage during Discord message search

**💥 Breaking Changes**
• Changed default format from 'simple' to 'release' for better readability

---
*Full changelog: 47 commits from 8 contributors*
```

### Initial Project Summary
```markdown
**🎉 Welcome to MyAwesomeProject!**

*A powerful tool for automating development workflows*

**📋 Project Overview**
MyAwesomeProject is a comprehensive solution for managing and automating your development pipeline with AI-powered intelligence.

**🚀 Key Features**
• Intelligent code analysis and review
• Automated documentation generation
• CI/CD pipeline integration
• Multi-language support
• Real-time collaboration tools

**💻 Technology Stack**
• Node.js + TypeScript
• React for UI
• PostgreSQL database
• Docker containerization

**📊 Project Statistics**
• 1,247 total commits
• 12 contributors
• 6 months of active development
• 89% test coverage

**🏁 Getting Started**
```bash
npm install myawesomeproject
npx myawesomeproject init
```

---
*Visit our [documentation](https://docs.example.com) for more information*
```

## Configuration

### Discord Setup

1. Create a Discord bot and get its token
2. Configure the Discord MCP server with your bot token
3. Get your Discord Guild ID:
   - Enable Developer Mode in Discord Settings > Advanced
   - Right-click your server name
   - Select "Copy Server ID"
4. Ensure your bot has the following permissions:
   - View Channels
   - Send Messages
   - Read Message History
   - Manage Channels (if using auto-create feature)
   - Manage Permissions (for read-only channel setup)

### Git Configuration

The command automatically works with the Git repository in your current directory:
- No additional Git setup required
- Uses Claude Code's built-in Git access
- Reads commit history directly from your local repository
- Parses conventional commit messages for better categorization
- Extracts contributor information from commit authors

## Tips

1. **Consistent Commit Messages**: Use conventional commits (feat:, fix:, etc.) for better categorization
2. **Version Tags**: Tag your releases in Git for automatic version detection
3. **Release Schedule**: Run this command as part of your regular release cycle
4. **Channel Organization**: Use a dedicated channel for release notes to make searching easier
5. **First Time Setup**: The command will generate a comprehensive project summary if no previous releases are found
6. **Read-Only Channels**: The auto-created channel ensures only the bot can post, keeping release history clean

## Troubleshooting

### "No previous release found"
- The command will generate an initial project summary instead
- This is normal for first-time use
- Subsequent runs will find this initial release and generate proper changelogs

### "Discord channel not found"
- Verify the channel ID is correct
- Ensure your Discord bot has access to the channel
- Try omitting channel_id to let the command create one

### "Cannot create channel"
- Ensure your bot has "Manage Channels" permission
- Check that the guild allows bot-created channels
- Try using an existing channel with `create_channel=false`

### "Commits not categorized correctly"
- Use conventional commit format: `type(scope): description`
- Common types: feat, fix, docs, style, refactor, perf, test, chore

## Support

For issues or feature requests, please visit the [Commands.com support](https://commands.com/support) page.  Thank you.
