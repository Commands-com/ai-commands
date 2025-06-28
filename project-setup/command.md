# Project Scaffolder
---
description: >
  Intelligent project setup assistant that creates complete development environments
  with best practices. Analyzes project requirements, sets up proper directory structure,
  initializes version control, configures CI/CD pipelines, and creates comprehensive
  documentation. Supports multiple tech stacks and deployment targets.
allowed_tools:
  - filesystem      # Create project structure and files
  - github          # Initialize repository and setup workflows
  - memory          # Track project templates and best practices
  - sqlite          # Store project configurations and templates
---

## Arguments

```
/project-setup 
name="<project-name>"
stack="<react|vue|nextjs|express|fastapi|django|go-api|flutter>"
[deployment=<vercel|netlify|aws|docker|none>]
[features=<auth,database,testing,api>]
[license=<mit|apache|gpl>]
```
*Defaults → `deployment=none  features=testing  license=mit`*

### Example

```
/project-setup 
name="my-saas-app"
stack=nextjs
deployment=vercel
features=auth,database,testing,api
license=mit
```

---

## Context – what the AI should do

1. **Parse and Validate Arguments**
   * `name` - project name (validate naming conventions)
   * `stack` - technology stack to use
   * `deployment` - target deployment platform
   * `features` - comma-separated list of features to include
   * `license` - open source license type

2. **Project Structure Creation**
   * Create root directory: `/filesystem__create_directory path=./${name}`
   * Generate tech stack specific folder structure
   * Set up source, test, config, and documentation directories
   * Create environment-specific configuration files

3. **Stack-Specific Setup**
   * **React/Next.js**: package.json, tsconfig.json, tailwind config, API routes
   * **Vue**: vue.config.js, router setup, store configuration
   * **Express**: server structure, middleware setup, route organization
   * **FastAPI/Django**: app structure, models, serializers, requirements.txt
   * **Go**: go.mod, main.go, handler structure, middleware
   * **Flutter**: pubspec.yaml, lib structure, platform configurations

4. **Feature Integration**
   * **Authentication**: Setup auth providers, user models, protected routes
   * **Database**: ORM configuration, migration files, seed data
   * **Testing**: Test framework setup, example tests, CI configuration
   * **API**: OpenAPI specs, client generation, error handling

5. **Development Tools Configuration**
   * Version control: `.gitignore`, `.gitattributes`, README.md
   * Code quality: ESLint, Prettier, pre-commit hooks
   * Environment: `.env.example`, Docker files, development scripts
   * CI/CD: GitHub Actions, deployment workflows

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

8. **Knowledge Base and Templates**
   * Store successful project configurations in memory
   * Track which combinations work well together
   * Learn from deployment patterns and common issues
   * Update SQLite with project metadata for future reference

9. **Post-Setup Verification**
   * Validate all configurations can build successfully
   * Check dependency compatibility
   * Verify deployment configuration syntax
   * Generate setup verification checklist

> Create production-ready projects with industry best practices. Include comprehensive documentation and clear next steps for development.