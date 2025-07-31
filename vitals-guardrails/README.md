# Vitals Guardrails

> Keep the site fast and compliant automatically so marketing dollars convert.

## The Problem

Teams want performance and accessibility checks on every PR, but today's solutions are **fragmented**:

- **DIY GitHub Actions**: Lighthouse CI and axe-core exist, but each requires manual setup, no unified budgets, and little executive visibility
- **Point Plugins**: Host-specific tools lock you to a provider and don't cover accessibility
- **Internal Platform Work**: Costs sprints to build, then ongoing maintenance across repos
- **Periodic Audits**: Expensive snapshots that go stale immediately—not continuous guardrails

## The Solution

**Vitals Guardrails** is a one-shot command that:

1. **Detects your static site framework** (11ty, Astro, Hugo, Jekyll, Gatsby, Next.js)
2. **Installs comprehensive checks**:
   - 🚦 **Lighthouse scores** - Performance, Accessibility, SEO, Best Practices
   - 📊 **Core Web Vitals** - LCP, TBT, CLS (the metrics Google uses)
   - ♿ **WCAG compliance** - via axe-core to avoid legal issues
   - 📈 **Trend analysis** - Compare against main branch baseline
3. **Creates a single PR** with CI/CD workflows ready to merge
4. **Starts in report-only mode** - tune thresholds before enforcing

## Why It Wins

- **Marketing ROI**: Better Core Web Vitals = better SEO = more conversions
- **Legal protection**: Automated WCAG checking reduces lawsuit risk
- **Zero false positives**: Smart defaults from real-world sites
- **Progressive rollout**: Start with homepage → critical pages → full site
- **Executive visibility**: Single metrics dashboard in PR comments

## Usage

```bash
# Basic usage - mobile performance focus
/vitals-guardrails

# Desktop testing
/vitals-guardrails device=desktop

# Enforce mode - fail builds below thresholds
/vitals-guardrails mode=enforce

# Test PR preview deployments
/vitals-guardrails preview_url=https://pr-123.vercel.app

# Custom thresholds
/vitals-guardrails performance_threshold=95 lcp_threshold=2000

# Test more pages (slower but thorough)
/vitals-guardrails max_pages=50
```

## What You Get

### GitHub Actions Workflow
- Runs on every PR
- Smart URL discovery (sitemap → crawl → list)
- Parallel Lighthouse + axe-core execution
- Nightly baseline collection
- Detailed PR comments with trends

### Testing Tools
- `tools/collect-urls.mjs` - Smart page discovery
- `tools/axe-scan.mjs` - WCAG compliance scanner
- `tools/summarize-lighthouse.mjs` - Metrics aggregation
- `tools/evaluate-gate.mjs` - Pass/fail logic with trends

### Documentation
- Performance optimization guide
- Common accessibility fixes
- Threshold tuning playbook

## Example PR Comment

```
## 🚦 Vitals & Compliance Report

### Performance Metrics
| Metric | Budget | Current | vs Main | Status |
|--------|---------|----------|----------|---------|
| Score | ≥90 | 92 | ↑ +3 | ✅ |
| LCP | ≤2500ms | 2100ms | ↓ -200ms | ✅ |
| TBT | ≤300ms | 250ms | → +0ms | ✅ |
| CLS | ≤0.1 | 0.05 | ↓ -0.02 | ✅ |

### Accessibility Issues
| Severity | Count | Status |
|----------|--------|--------|
| Critical | 0 | ✅ |
| Serious | 2 | ✅ |
| Moderate | 5 | ℹ️ |

### Status: ✅ Passed
> 📋 Running in **report-only** mode
```

## Business Impact

- **SEO Rankings**: Google uses Core Web Vitals as a ranking factor
- **Conversion Rates**: 100ms faster = 1% more conversions (Google/Deloitte)
- **Legal Compliance**: WCAG AA helps meet ADA requirements
- **Brand Protection**: No embarrassing performance regressions

## Framework Support

✅ **Optimized for**:
- Eleventy (11ty)
- Astro
- Hugo
- Jekyll
- Gatsby
- Next.js (static export)
- Docusaurus
- Any static site with build command

## Requirements

- Git repository
- Node.js project (package.json)
- GitHub Actions enabled
- Static site generator or build process

## See Also

- [Docs Guardrails](../docs-guardrails) - Content quality checks
- [Command Documentation](command.md) - Full implementation details