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

Production-ready code review assistant with security analysis, maintainability scoring, and CI/CD integration via SARIF output.

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

Production-grade project bootstrapper with security-first practices, validated configurations, and comprehensive testing.

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

Ethical API reconnaissance tool that discovers and documents APIs while respecting rate limits and terms of service.

**Required MCP Servers:** puppeteer, filesystem, memory, sqlite

```bash
/api-explore 
base_url="https://api.example.com"
output_format=openapi
test_endpoints=yes
```

### 🤖 Smart Command Builder
**File:** `smart-command-builder/command.md`  
**Category:** Development  
**Difficulty:** Beginner  

Intelligent command generator that creates complete Commands.com-compatible commands from task descriptions.

**Required MCP Servers:** commands-com/mcp-server, filesystem

```bash
/build-command 
task_description="analyze git commits and generate release notes"
category=development
complexity=medium
```

### 🎨 Eleventy Asset Pipeline
**File:** `eleventy-asset-pipeline/command.md`  
**Category:** Development  
**Difficulty:** Intermediate  

Validates and scaffolds a production-grade asset pipeline for Eleventy sites with JavaScript bundling, CSS processing, and cache-busting.

**Required MCP Servers:** filesystem

```bash
/eleventy-asset-pipeline 
bundle_mode=true
css_framework=tailwind
source_maps=true
```

### 📢 Discord Release Generator
**File:** `discord-release/command.md`  
**Category:** Development  
**Difficulty:** Intermediate  

Automatically generate release notes for Discord by analyzing Git commits since the last published release.

**Required MCP Servers:** docker-mcp-discord, docker-filesystem

```bash
/discord-release 
guild_id="YOUR_GUILD_ID"
channel_name="release-notes"
format=release
days_back=30
```

### 🛡️ Docs Guardrails
**File:** `docs-guardrails/command.md`  
**Category:** Testing  
**Difficulty:** Beginner  

Ship content without surprises—no 404s, no empty titles, no brand hits. One-shot setup for docs quality that catches broken links, missing alt text, and frontmatter errors.

**Required MCP Servers:** filesystem

```bash
/docs-guardrails 
mode=report-only
scope=all
frontmatter_required=title,date
```

### ⚡ Performance Agent Builder
**File:** `performance-agent-builder/command.md`  
**Category:** Testing  
**Difficulty:** Advanced  

Install a specialized Claude Code subagent that automatically monitors, diagnoses, and fixes performance issues in your codebase.

**Required MCP Servers:** filesystem

```bash
/performance-agent-builder 
languages=auto
profile_mode=quick
min_improvement=10
```

### 📝 Docs Validator Agent Builder
**File:** `docs-validator-agent-builder/command.md`  
**Category:** Testing  
**Difficulty:** Advanced  

Install a specialized Claude Code subagent that automatically monitors, validates, and fixes documentation issues in your codebase.

**Required MCP Servers:** filesystem

```bash
/docs-validator-agent-builder 
frameworks=auto
mode=report-only
validation_scope=markdown-only
```

### 🔧 Subagent Builder
**File:** `subagent-builder/command.md`  
**Category:** Development  
**Difficulty:** Advanced  

Build perfect Claude Code subagents using the latest documentation and best practices.

**Required MCP Servers:** filesystem

```bash
/subagent-builder 
name="code-reviewer"
purpose="Review code for security and quality"
type=specialized
proactive=true
```

### 👥 Review Team Manager
**File:** `review-team/command.md`  
**Category:** Development  
**Difficulty:** Advanced  

Creates and manages a project-aware code review team of specialized Claude Code agents tailored to your specific project.

**Required MCP Servers:** filesystem

```bash
/review-team 
agent=code-quality-reviewer
check=false
create=true
```

## 🛠️ Required MCP Servers

| Server | Description | Commands |
|--------|-------------|----------|
| `github` | GitHub repository and PR management | code-reviewer, project-setup |
| `filesystem` | File system operations | All commands |
| `memory` | Persistent memory and knowledge graph | code-reviewer, project-setup, api-explorer |
| `sqlite` | Local database operations | code-reviewer, project-setup, api-explorer |
| `puppeteer` | Browser automation and web scraping | api-explorer |
| `commands-com/mcp-server` | Commands.com MCP integration | smart-command-builder |
| `docker-mcp-discord` | Discord integration via Docker | discord-release |
| `docker-filesystem` | Docker filesystem access | discord-release |

## 📁 Repository Structure

```
ai-commands/
├── commands.yaml                    # Repository metadata and command definitions
├── README.md                        # This file
├── LICENSE                          # MIT License
├── code-reviewer/                   # AI Code Reviewer
│   ├── command.md
│   ├── README.md
│   └── preview.png
├── project-setup/                   # Project Scaffolder
│   ├── command.md
│   ├── README.md
│   └── preview.png
├── api-explorer/                    # API Explorer & Documentation Generator
│   ├── command.md
│   ├── README.md
│   └── preview.png
├── smart-command-builder/           # Smart Command Builder
│   ├── command.md
│   ├── README.md
│   └── preview.png
├── eleventy-asset-pipeline/         # Eleventy Asset Pipeline
│   ├── command.md
│   ├── README.md
│   └── preview.png
├── discord-release/                 # Discord Release Generator
│   ├── command.md
│   ├── README.md
│   └── preview.png
├── docs-guardrails/                 # Docs Guardrails
│   ├── command.md
│   └── README.md
├── performance-agent-builder/       # Performance Agent Builder
│   ├── command.md
│   ├── README.md
│   └── FIXES_APPLIED.md
├── docs-validator-agent-builder/    # Docs Validator Agent Builder
│   ├── command.md
│   └── README.md
├── subagent-builder/                # Subagent Builder
│   ├── command.md
│   ├── README.md
│   └── preview.png
└── review-team/                     # Review Team Manager
    ├── command.md
    ├── README.md
    └── preview.png
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