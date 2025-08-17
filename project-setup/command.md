---
name: Project Scaffolder
description: Production-grade project bootstrapper that creates complete development environments with security-first best practices. Validates stack/deployment compatibility, sets up CI/CD pipelines with stack-specific workflows, implements comprehensive testing, and creates architecture documentation. Features pluggable extensions and environment abstractions.
allowed_tools:
  - filesystem      # Create project structure and files
  - github          # Initialize repository and setup workflows
  - memory          # Track project templates and best practices
  - sqlite          # Store project configurations and templates
tags:
  - scaffolding
  - dev-ops
  - ci-cd
  - project-setup
  - automation
category: development
---

## Arguments

```
/project-setup 
name="<project-name>"
stack="<react|vue|nextjs|svelte|astro|remix|vite|express|fastapi|django|go-api|flutter>"
[deployment=<vercel|netlify|aws|docker|render|none>]
[features=<auth,database,testing,api,graphql,i18n,analytics>]
[license=<mit|apache|gpl|bsd3|mpl2>]
[security=<true|false>]
```
*Defaults → `deployment=none  features=testing  license=mit  security=false`*

### Examples

```bash
# Basic setup with security features
/project-setup 
name="my-saas-app"
stack=nextjs
deployment=vercel
features=auth,database,testing,api
license=mit
security=true

# API service with Docker deployment
/project-setup 
name="api-service"
stack=fastapi
deployment=docker
features=database,testing,api

# Mobile app with minimal features
/project-setup 
name="mobile-app"
stack=flutter
deployment=none
features=testing
```

---

## Context – what the AI should do

1. **Parse and Validate Arguments**
   * `name` - project name (validate naming conventions)
   * `stack` - technology stack to use
   * `deployment` - target deployment platform (validate compatibility)
   * `features` - comma-separated list of features (extensible)
   * `license` - open source license type (MIT, Apache 2.0, GPL, BSD-3, MPL-2.0)
   * `security` - enable security-first configuration
   
   **Validation Rules:**
   * Flutter cannot deploy to Vercel/Netlify
   * Express + Netlify requires Functions configuration
   * Static sites (React/Vue/Svelte/Astro) + AWS need S3/CloudFront
   * Remix/Next.js work on all platforms (full-stack frameworks)
   * Vite projects deploy like static sites
   * API stacks require Docker or cloud deployment

2. **Project Structure Creation**
   * Create root directory: `/filesystem__create_directory path=./${name}`
   * Generate tech stack specific folder structure
   * Set up source, test, config, and documentation directories
   * Create environment-specific configuration files

3. **Stack-Specific Setup**
   * **React/Next.js**: package.json, tsconfig.json, tailwind config, API routes
   * **Vue**: vue.config.js, router setup, store configuration
   * **Svelte**: svelte.config.js, SvelteKit structure, adapter configuration
   * **Astro**: astro.config.mjs, content collections, island architecture
   * **Remix**: remix.config.js, routes structure, loader/action patterns
   * **Vite**: vite.config.js, vanilla TS/JS structure, HMR setup
   * **Express**: server structure, middleware setup, route organization
   * **FastAPI/Django**: app structure, models, serializers, requirements.txt
   * **Go**: go.mod, main.go, handler structure, middleware
   * **Flutter**: pubspec.yaml, lib structure, platform configurations

4. **Security & Compliance Layer** (if security=true)
   * **Dependency Scanning**: Dependabot config, Snyk integration
   * **Secret Management**: Git-secrets hooks, .gitleaks.toml
   * **Supply Chain**: SLSA attestation, SBOM generation
   * **Security Workflows**: SAST scanning, container scanning
   * **Compliance Templates**: SOC2, HIPAA, GDPR documentation stubs

5. **Feature Integration** (Pluggable System)
   * **Core Features**:
     - **Authentication**: Auth providers, JWT/OAuth, protected routes
     - **Database**: ORM setup, migrations, connection pooling
     - **Testing**: Unit/integration/e2e frameworks, coverage
     - **API**: REST/GraphQL endpoints, versioning, rate limiting
   * **Extended Features** (via features+=):
     - **GraphQL**: Schema definition, resolvers, subscriptions
     - **i18n**: Locale files, translation keys, RTL support
     - **Analytics**: GA4, Mixpanel, custom event tracking
     - **Monitoring**: OpenTelemetry, error tracking, APM
   * **Community Plugins**: Register via SQLite, auto-discover

6. **Development Tools Configuration**
   * Version control: `.gitignore`, `.gitattributes`, README.md
   * Code quality: ESLint, Prettier, pre-commit hooks
   * Environment: `.env.development`, `.env.test`, `.env.production`, `.env.example`
   * Docker: Multi-stage Dockerfile, docker-compose variants
   * CI/CD: Stack-specific GitHub Actions workflows
   * Security: Dependabot, secret scanning, SAST tools (if security=true)

6. **Documentation Generation**
   * **README.md**: Project overview, setup instructions, usage examples
   * **CONTRIBUTING.md**: Development guidelines, code style, PR process
   * **API Documentation**: Auto-generated API docs based on stack
   * **Architecture Diagram**: High-level system design explanation

7. **Deployment Configuration**
   * **Vercel**: vercel.json, environment variables setup
   * **Netlify**: netlify.toml, build configuration
   * **AWS**: CloudFormation templates, deployment scripts
   * **Docker**: Multi-stage Dockerfile, docker-compose for development
   * **Render**: render.yaml, environment groups, preview environments

8. **Stack-Specific CI/CD Templates**
   * **Node.js stacks**: `.github/workflows/node-test.yml`, `node-lint.yml`
   * **Svelte/Astro/Remix/Vite**: `.github/workflows/node-test.yml` with framework-specific builds
   * **Python stacks**: `.github/workflows/python-django.yml`, `python-fastapi.yml`
   * **Go**: `.github/workflows/go-ci.yml` with `go vet`, `go test`
   * **Flutter**: `.github/workflows/flutter-ci.yml` with analyze/test
   * **Deployment**: `deploy-vercel.yml`, `deploy-aws.yml`, `docker-build.yml`

9. **Knowledge Base and Templates**
   * Store successful configurations in memory/SQLite
   * Track stack/deployment compatibility matrix
   * Learn from common patterns and successful combinations
   * Update SQLite with project metadata and success metrics

10. **Post-Setup Verification & Smoke Tests**
    * **Node.js (React/Vue/Next.js)**: Run `npm install && npm run build && npm test`
    * **Svelte**: Run `npm install && npm run check && npm run build`
    * **Astro**: Run `npm install && npm run build`
    * **Remix**: Run `npm install && npm run build && npm run typecheck`
    * **Vite**: Run `npm install && npm run build`
    * **Python**: Run `pip install -r requirements.txt && pytest`
    * **Go**: Run `go mod download && go vet ./... && go test ./...`
    * **Flutter**: Run `flutter pub get && flutter analyze`
    * Fail fast with actionable error logs
    * Generate setup verification report

11. **Documentation Generation**
    * **Architecture Diagrams**: Mermaid-based system diagrams
    * **API Docs**: Swagger/OpenAPI for REST, GraphQL schemas
    * **Stack-specific**: TSDoc for TypeScript, GoDoc for Go
    * **README**: Include quickstart, architecture, deployment guides

> Create production-grade projects with security-first practices, validated configurations, and comprehensive testing. Supports extensible features and learns from successful project patterns for consistent, reliable project bootstrapping.