# AI Commands Collection

A curated collection of AI commands for development workflows and automation. These commands are designed to work with Claude Code and other AI assistants that support MCP (Model Context Protocol).

## 🚀 Quick Start

The fastest way to create and publish AI commands is through Commands.com. Visit the quick-start guide to get started in under 2 minutes:

👉 **[https://commands.com/creator/commands-prompts/quick-start/](https://commands.com/creator/commands-prompts/quick-start/)**

## 📋 Available Commands

### 🧹 Code Reviewer
**File:** `code-reviewer/command.md`  
**Category:** Development  
**Difficulty:** Intermediate  

Intelligent code review assistant that analyzes code quality, security vulnerabilities, performance issues, and best practices.

**Required MCP Servers:** github, filesystem, memory, sqlite

```bash
/code-review 
source="https://github.com/user/repo/pull/123"
focus=security
languages=javascript,typescript
```

### 🏗️ Project Scaffolder  
**File:** `project-setup/command.md`  
**Category:** Development  
**Difficulty:** Beginner  

Intelligent project setup assistant that creates complete development environments with best practices.

**Required MCP Servers:** filesystem, github, memory, sqlite

```bash
/project-setup 
name="my-saas-app"
stack=nextjs
deployment=vercel
features=auth,database,testing,api
```

### 🔌 API Explorer & Documentation Generator
**File:** `api-explorer/command.md`  
**Category:** API Tools  
**Difficulty:** Intermediate  

Comprehensive API analysis tool that discovers, tests, and documents APIs automatically.

**Required MCP Servers:** puppeteer, filesystem, memory, sqlite

```bash
/api-explore 
base_url="https://api.example.com"
output_format=openapi
test_endpoints=yes
```

## 🛠️ Required MCP Servers

| Server | Description | Commands |
|--------|-------------|----------|
| `github` | GitHub repository and PR management | code-reviewer, project-setup |
| `filesystem` | File system operations | All commands |
| `memory` | Persistent memory and knowledge graph | All commands |
| `sqlite` | Local database operations | All commands |
| `puppeteer` | Browser automation and web scraping | api-explorer |

## 📁 Repository Structure

```
ai-commands/
├── commands.yaml          # Repository metadata and command definitions
├── README.md              # This file
├── code-reviewer/
│   ├── command.md         # Code review command
│   └── preview.png        # Command preview image
├── project-setup/
│   ├── command.md         # Project scaffolding command  
│   └── preview.png        # Command preview image
└── api-explorer/
    ├── command.md         # API exploration command
    └── preview.png        # Command preview image
```

## 🚀 Creating Your Own Commands

### Option 1: AI-Powered Generation (Recommended)

Use the Commands.com MCP to automatically generate your `commands.yaml` file:

1. **Connect the Commands.com MCP** to Claude
2. **Use AI prompts** to scan your repository and generate commands
3. **Commit to GitHub** and import to Commands.com

Full guide: [https://commands.com/creator/commands-prompts/quick-start/](https://commands.com/creator/commands-prompts/quick-start/)

### Option 2: Manual Creation

1. Create a new directory for your command
2. Add `command.md` with proper YAML frontmatter
3. Include a preview image (optional but recommended)
4. Update `commands.yaml` with command metadata
5. Test thoroughly with required MCP servers

## 📖 Usage Guidelines

1. **Install Required MCP Servers**: Ensure all required MCP servers are installed and configured
2. **Check Command Documentation**: Each command has detailed instructions in its `command.md` file
3. **Follow Argument Syntax**: Use the exact argument format specified in the command
4. **Review Output**: Commands generate files and reports - check the specified output directories

## 🤝 Contributing

We welcome contributions of new commands or improvements:

1. Fork this repository
2. Create your command following the structure above
3. Test with all required MCP servers
4. Submit a pull request

For command ideas and best practices, visit the [Commands.com documentation](https://commands.com/docs/).

## 📄 License

MIT License - see individual command files for specific licensing information.

## 🔗 Resources

- [Commands.com Platform](https://commands.com) - Discover and share AI commands
- [Claude Code Documentation](https://docs.anthropic.com/claude/docs/claude-code) - Official Claude Code docs
- [MCP Documentation](https://modelcontextprotocol.io/) - Model Context Protocol specification
- [Commands.com Quick Start](https://commands.com/creator/commands-prompts/quick-start/) - Create commands with AI