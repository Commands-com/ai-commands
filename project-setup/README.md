# Project Scaffolder

This AI command helps you set up complete development environments with industry best practices. It creates project structure, initializes version control, configures CI/CD pipelines, and generates comprehensive documentation.

## How to Use:
1. **Project Name**: Enter the name for your new project (used for directory and package naming)
2. **Technology Stack**: Choose your primary tech stack (React, Vue, Next.js, Express, FastAPI, Django, Go API, or Flutter)
3. **Deployment Target**: Select deployment platform (Vercel, Netlify, AWS, Docker, or none)
4. **Features**: Specify comma-separated features to include (auth, database, testing, API)
5. **License**: Choose the open source license type (MIT, Apache, GPL)

## What It Creates:
- Complete project directory structure
- Package.json/requirements.txt with dependencies
- Configuration files (tsconfig, eslint, prettier)
- Git initialization with proper .gitignore
- CI/CD workflows for your chosen platform
- README and documentation templates
- Environment configuration examples

## Prerequisites:
- Filesystem MCP server for creating project structure and files
- GitHub MCP server for repository initialization and workflows
- Memory MCP server for tracking project templates and best practices
- SQLite MCP server for storing project configurations and templates