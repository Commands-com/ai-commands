# AI Commands Collection

A curated collection of AI commands for development workflows and automation. These commands are designed to work with Claude Code and other AI assistants that support MCP (Model Context Protocol).

## 🚀 Quick Start

### Prerequisites

- Claude Code or compatible AI assistant with MCP support
- Required MCP servers (see individual command requirements)

### Usage

1. Clone or download this repository
2. Import commands into your AI assistant via GitHub integration
3. Ensure required MCP servers are installed and configured
4. Run commands using the specified syntax

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

## 🔧 MCP Server Installation

### GitHub Server
```bash
npm install @modelcontextprotocol/server-github
```

### Filesystem Server  
```bash
npm install @modelcontextprotocol/server-filesystem
```

### Memory Server
```bash
npm install @modelcontextprotocol/server-memory
```

### SQLite Server
```bash
npm install @mokei/mcp-sqlite
```

### Puppeteer Server
```bash
npm install @modelcontextprotocol/server-puppeteer
```

## 📖 Usage Guidelines

1. **Read Command Documentation**: Each command has detailed instructions in its `command.md` file
2. **Check Prerequisites**: Ensure all required MCP servers are installed
3. **Follow Argument Syntax**: Use the exact argument format specified
4. **Review Output**: Commands generate files and reports - check the specified output directories

## 🤝 Contributing

Feel free to contribute new commands or improvements:

1. Create a new directory for your command
2. Add `command.md` with proper YAML frontmatter
3. Include preview image
4. Update `commands.yaml` with command metadata
5. Test thoroughly with required MCP servers

## 📄 License

MIT License - see individual command files for specific licensing information.

## 🔗 Links

- [Claude Code Documentation](https://docs.anthropic.com/claude/docs/claude-code)
- [MCP Documentation](https://modelcontextprotocol.io/)
- [Example Prompts Repository](https://github.com/user/ai-image-prompts)