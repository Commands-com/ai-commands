# Project Scaffolder

Production-grade project bootstrapper that creates complete development environments with security-first practices and validated configurations. Supports multiple tech stacks with stack-specific CI/CD pipelines and comprehensive smoke testing.

## Key Features:
- **Smart Validation**: Prevents invalid stack/deployment combinations (e.g., Flutter + Vercel)
- **Security Layer**: Optional security scanning, secret management, and compliance tools
- **Environment Abstraction**: Separate configs for development, test, and production
- **Pluggable Features**: Core features plus extensible community plugins
- **Stack-Specific CI/CD**: Tailored GitHub Actions workflows for each technology
- **Automated Testing**: Post-setup smoke tests ensure everything works
- **Learning System**: Builds knowledge base from successful project patterns

## How to Use:
1. **Project Name**: Enter the name for your new project (used for directory and package naming)
2. **Technology Stack**: Choose your primary tech stack:
   - **Frontend**: React, Vue, Next.js, Svelte, Astro, Remix, Vite
   - **Backend**: Express, FastAPI, Django, Go API
   - **Mobile**: Flutter
3. **Deployment Target**: Select deployment platform (Vercel, Netlify, AWS, Docker, Render, or none)
4. **Features**: Specify comma-separated features (auth, database, testing, api, graphql, i18n, analytics)
5. **License**: Choose the open source license (MIT, Apache 2.0, GPL, BSD-3, MPL-2.0)
6. **Security**: Enable security-first configuration with Dependabot, SAST, and secret scanning

## What It Creates:
- Complete project directory structure with best practices
- Stack-specific configuration files (tsconfig, go.mod, pubspec.yaml, etc.)
- Multiple environment files (.env.development, .env.test, .env.production)
- Git initialization with comprehensive .gitignore
- Stack-specific CI/CD workflows (node-test.yml, python-django.yml, go-ci.yml)
- Architecture documentation with Mermaid diagrams
- API documentation (Swagger/OpenAPI for REST, GraphQL schemas)
- Security configurations (if enabled): Dependabot, secret scanning, SAST
- Post-setup verification with automated smoke tests

## Stack Compatibility Matrix:
| Stack | Vercel | Netlify | AWS | Docker | Render |
|-------|--------|---------|-----|--------|--------|
| React | ✅ | ✅ | ✅ | ✅ | ✅ |
| Vue | ✅ | ✅ | ✅ | ✅ | ✅ |
| Next.js | ✅ | ✅ | ✅ | ✅ | ✅ |
| Svelte | ✅ | ✅ | ✅ | ✅ | ✅ |
| Astro | ✅ | ✅ | ✅ | ✅ | ✅ |
| Remix | ✅ | ✅ | ✅ | ✅ | ✅ |
| Vite | ✅ | ✅ | ✅ | ✅ | ✅ |
| Express | ❌ | ⚠️* | ✅ | ✅ | ✅ |
| FastAPI | ❌ | ❌ | ✅ | ✅ | ✅ |
| Django | ❌ | ❌ | ✅ | ✅ | ✅ |
| Go API | ❌ | ❌ | ✅ | ✅ | ✅ |
| Flutter | ❌ | ❌ | ✅ | ✅ | ❌ |

*Express on Netlify requires Functions configuration

## Prerequisites:
- Filesystem MCP server for creating project structure and files
- GitHub MCP server for repository initialization and workflows
- Memory MCP server for tracking project templates and best practices
- SQLite MCP server for storing project configurations and templates

## Example Commands:
```bash
# Basic React app with testing
/project-setup name="my-app" stack=react

# Production Next.js with all features and security
/project-setup name="saas-platform" stack=nextjs deployment=vercel features=auth,database,testing,api,analytics security=true

# Modern Svelte app with Vercel deployment
/project-setup name="svelte-app" stack=svelte deployment=vercel features=auth,testing

# Static site with Astro
/project-setup name="blog" stack=astro deployment=netlify features=i18n,analytics

# Full-stack Remix app
/project-setup name="remix-app" stack=remix deployment=render features=auth,database,api,testing

# Vanilla TypeScript with Vite
/project-setup name="ts-app" stack=vite features=testing

# Django API with Docker deployment
/project-setup name="api-service" stack=django deployment=docker features=database,api,testing

# Flutter app with basic features
/project-setup name="mobile-app" stack=flutter deployment=none features=testing
```