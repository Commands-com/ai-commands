# Docs Guardrails

> Ship content without surprises—no 404s, no empty titles, no brand hits.

## The Problem

Teams want content quality checks on every PR, but today's solutions are **piecemeal**:

- **DIY GitHub Actions**: Link checkers exist, but require manual YAML, config sprawl, and ongoing maintenance
- **Point Plugins**: One-click tools are host-specific and rarely provide unified reporting
- **Internal Platform Work**: Costs sprints, not hours—then must be maintained across repos
- **Periodic Audits**: Helpful snapshots, not guardrails. Findings go stale immediately

## The Solution

**Docs Guardrails** is a one-shot command that:

1. **Detects your static site framework** (11ty, Astro, Hugo, Docusaurus, Jekyll, etc.)
2. **Installs three quality checks**:
   - 🔗 **Broken links** via `lychee` - internal, external, anchors
   - 📝 **Markdown standards** via `markdownlint` - consistent formatting
   - 🏷️ **Frontmatter validation** - required fields, date formats, SEO metadata
3. **Creates a single PR** with everything configured and ready to merge
4. **Starts in report-only mode** - see issues without breaking builds

## Why It Wins

- **Zero maintenance**: Once merged, it just works
- **Framework agnostic**: Works with any static site generator
- **Gradual adoption**: Start with reporting, enforce when ready
- **Single source of truth**: One PR comment shows all issues
- **Smart defaults**: Tuned thresholds prevent false positives

## Usage

```bash
# Basic usage - starts in report-only mode
/docs-guardrails

# Enforce mode - fail builds on issues
/docs-guardrails mode=enforce

# Custom thresholds
/docs-guardrails thresholds_broken_links=0 thresholds_markdown=10

# Required frontmatter fields
/docs-guardrails frontmatter_required=title,date,author,description

# Check built HTML too (slower but thorough)
/docs-guardrails scope=all
```

## What You Get

### GitHub Actions Workflow
- Runs on every PR
- Smart caching for fast checks
- Parallel execution
- Detailed PR comments

### Configuration Files
- `lychee.toml` - Link checker config with smart excludes
- `.markdownlint.jsonc` - Friendly markdown rules
- `tools/frontmatter-check.mjs` - Custom validation script

### Documentation
- How to fix common issues
- Tuning thresholds
- Team onboarding guide

## Example PR Comment

```
## 📊 Docs Guardrails Results

| Check | Count | Threshold | Status |
|-------|--------|-----------|---------|
| 🔗 Broken Links | 2 | 5 | ✅ |
| 📝 Markdown Issues | 8 | 20 | ✅ |
| 🏷️ Frontmatter Issues | 0 | 10 | ✅ |

> 📋 **Report-only mode** - Issues shown but not enforced
```

## Framework Support

✅ **Tested with**:
- Eleventy (11ty)
- Astro
- Hugo
- Docusaurus
- Jekyll
- Gatsby
- Next.js (static export)
- Generic static sites

## Requirements

- Git repository
- Node.js project (package.json)
- GitHub Actions enabled
- Static site generator

## See Also

- [Vitals Guardrails](../vitals-guardrails) - Performance & accessibility checks
- [Command Documentation](command.md) - Full implementation details