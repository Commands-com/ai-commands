# Docs Validator Agent Builder

Install a specialized Claude Code subagent that automatically monitors, validates, and fixes documentation issues in your codebase.

## What It Does

This command transforms Claude Code into a documentation quality powerhouse by:

1. **Installing documentation validators** for your static site (lychee, markdownlint, frontmatter checks)
2. **Creating a specialized subagent** that knows how to validate and fix documentation
3. **Setting up automated workflows** for detect → fix → verify cycles

## Quick Start

```bash
# Auto-detect static site generator and install
/docs-validator-agent-builder

# Install for specific frameworks
/docs-validator-agent-builder frameworks=eleventy,docusaurus

# Install globally (all projects)
/docs-validator-agent-builder install_global=true

# Start in enforce mode (fail on issues)
/docs-validator-agent-builder mode=enforce
```

## How The Subagent Works

Once installed, just ask Claude Code about documentation quality:
- "Check for broken links"
- "Fix markdown formatting issues"
- "Validate all documentation"
- "Find missing metadata"

The docs validator subagent will:
1. **Scan** your documentation for issues
2. **Apply** automatic fixes where possible
3. **Generate** AI-powered improvements for complex issues
4. **Verify** all changes maintain quality
5. **Report** detailed results

## Example Output

```
Docs Validator:
[1/4] Scanning documentation...
  - Found 127 markdown files
  - Detected Eleventy static site generator
  
[2/4] Running quality checks...
  ✗ 12 broken links found
  ✗ 45 markdown formatting issues
  ✓ All frontmatter valid
  
[3/4] Applying fixes...
  ✓ Fixed 38/45 markdown issues automatically
  ✓ Updated 3 broken internal links
  ! 9 external links need manual review
  
[4/4] Results saved to docs_report.md
```

## Supported Frameworks

- **Eleventy**: 11ty sites with Nunjucks/Liquid
- **Docusaurus**: React-based documentation
- **Hugo**: Go-based static sites
- **Jekyll**: Ruby-based GitHub Pages
- **Astro**: Modern static site builder
- **Generic**: Any markdown-based site

## Requirements

- Claude Code (claude.ai/code)
- Node.js and npm/yarn/pnpm
- Git repository
- Static site with markdown files

## Learn More

See the full [command documentation](command.md) for detailed usage, configuration options, and troubleshooting.