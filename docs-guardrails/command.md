---
name: Docs Guardrails
description: Ship content without surprises—no 404s, no empty titles, no brand hits. One-shot command that installs link & content guardrails via PR. Works with 11ty, Astro, Hugo, Docusaurus, Next static, and more.
allowed_tools:
  - Read          # Read existing configs
  - Write         # Create new files
  - MultiEdit     # Update existing files
  - Bash          # Run commands
  - Grep          # Find patterns
  - LS            # List directories
  - TodoWrite     # Track progress
tags:
  - testing
  - quality
  - static-sites
  - ci-cd
category: testing
---

## Arguments

```
/docs-guardrails
[mode=<report-only|enforce>]
[scope=<markdown-only|all>]
[frontmatter_required=<comma-separated-keys>]
[ignore_domains=<comma-separated-domains>]
[thresholds_broken_links=<number>]
[thresholds_markdown=<number>]
[thresholds_frontmatter=<number>]
[build_command=<command|auto>]
[output_dir=<path|auto>]
```

## What This Command Does

1. **Detects your static site framework** (11ty, Jekyll, Hugo, Gatsby, Docusaurus, Astro)
2. **Adds three quality checks**:
   - 🔗 Broken links via `lychee`
   - 📝 Markdown standards via `markdownlint-cli2`
   - 🏷️ Frontmatter validation via custom script
3. **Starts in report-only mode** - see issues without breaking builds
4. **Creates a PR** with all changes ready to merge

## Why Teams Love It

- **No YAML spelunking**: The command writes the workflow for you
- **Fast adoption**: Starts report-only; flip to enforce when green
- **Framework-agnostic**: Works with any static site generator
- **Zero-config feel**: PRs are annotated automatically; teams rarely touch settings again

## Implementation

### LLM Intelligence Layer

The LLM should analyze the codebase to make intelligent decisions about:

1. **Path Exclusions**: Auto-detect generated directories, build outputs, vendor files
2. **Frontmatter Patterns**: Analyze existing markdown to find commonly used fields
3. **Domain Exclusions**: Find staging/dev URLs that should be ignored

Example detection logic (pseudocode for LLM guidance):
- Exclude: `node_modules/`, `dist/`, `build/`, `_site/`, `.cache/`, `vendor/`, `*.min.js`
- Detect framework-specific paths: `.next/`, `.nuxt/`, `.docusaurus/`, etc.
- Sample 10-20 markdown files to find >80% usage patterns for frontmatter
- Look for staging/dev domains in config files to auto-exclude

### Step 1: Framework Detection & Setup

```javascript
// Detect static site generator
const detectFramework = async () => {
  // First, check for monorepo markers
  const isMonorepo = await fileExists('lerna.json') || 
                     await fileExists('pnpm-workspace.yaml') ||
                     (await fileExists('package.json') && 
                      JSON.parse(await readFile('package.json')).workspaces);
  
  if (isMonorepo) {
    console.log('Monorepo detected - will configure per-package guardrails');
  }
  
  const checks = [
    { files: ['.eleventy.js', 'eleventy.config.js'], name: '11ty', output: '_site', build: 'npm run build' },
    { files: ['_config.yml'], name: 'jekyll', output: '_site', build: 'bundle exec jekyll build' },
    { files: ['hugo.toml', 'hugo.yaml', 'hugo.json', 'config.toml'], name: 'hugo', output: 'public', build: 'hugo' },
    { files: ['gatsby-config.js'], name: 'gatsby', output: 'public', build: 'npm run build' },
    { files: ['astro.config.mjs', 'astro.config.js'], name: 'astro', output: 'dist', build: 'npm run build' },
    { files: ['next.config.js'], name: 'nextjs', output: 'out', build: 'npm run build && npm run export' },
    { files: ['docusaurus.config.js'], name: 'docusaurus', output: 'build', build: 'npm run build' }
  ];
  
  for (const check of checks) {
    for (const file of check.files) {
      if (await fileExists(file)) {
        return check;
      }
    }
  }
  
  // Default fallback
  return { name: 'generic', output: '_site', build: 'npm run build' };
};

// Detect package manager
const detectPackageManager = async () => {
  if (await fileExists('pnpm-lock.yaml')) return 'pnpm';
  if (await fileExists('yarn.lock')) return 'yarn';
  return 'npm';
};

// Generate lychee skip blocks from ignore_domains
const generateDomainConfigs = (ignoreDomains) => {
  if (!ignoreDomains) return '';
  
  const domains = ignoreDomains.split(',').map(d => d.trim()).filter(Boolean);
  return domains.map(domain => `[[skip]]\npattern = "${domain}"`).join('\n\n');
};
```

### Step 2: Create Configuration Files

#### `.github/workflows/docs-guardrails.yml`
```yaml
name: Docs Guardrails

on:
  pull_request:
  workflow_dispatch:
  schedule:
    - cron: '0 3 * * *'  # Nightly full scan

permissions:
  contents: read
  pull-requests: write

concurrency:
  group: docs-guardrails-${{ github.ref }}
  cancel-in-progress: true

env:
  MODE: ${MODE}
  # Note: continue-on-error is set directly in each job
  SCOPE: ${SCOPE}
  BUILD_COMMAND: ${BUILD_COMMAND}
  OUTPUT_DIR: ${OUTPUT_DIR}
  THRESHOLDS_BROKEN_LINKS: ${THRESHOLDS_BROKEN_LINKS}
  THRESHOLDS_MARKDOWN: ${THRESHOLDS_MARKDOWN}
  THRESHOLDS_FRONTMATTER: ${THRESHOLDS_FRONTMATTER}
  FRONTMATTER_REQUIRED: ${FRONTMATTER_REQUIRED}
  PM: ${PM}
  IGNORE_DOMAINS: ${IGNORE_DOMAINS}

jobs:
  detect-changes:
    runs-on: ubuntu-latest
    outputs:
      markdown-files: ${{ steps.changes.outputs.markdown }}
      all-files: ${{ steps.changes.outputs.all }}
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0
      
      - id: changes
        run: |
          if [ "${{ github.event_name }}" = "pull_request" ]; then
            git fetch --no-tags --prune --depth=1 origin "${{ github.base_ref }}"
            MARKDOWN_FILES=$((git diff --name-only origin/${{ github.base_ref }}...HEAD | grep -E '\\.mdx?$' || true) | tr '\n' ' ')
            echo "markdown=${MARKDOWN_FILES}" >> $GITHUB_OUTPUT
            echo "all=false" >> $GITHUB_OUTPUT
          else
            echo "markdown=**/*.md **/*.mdx" >> $GITHUB_OUTPUT
            echo "all=true" >> $GITHUB_OUTPUT
          fi

  broken-links:
    needs: detect-changes
    runs-on: ubuntu-latest
    continue-on-error: ${{ env.MODE == 'report-only' }}
    steps:
      - uses: actions/checkout@v4
      
      - name: Restore link cache
        uses: actions/cache@v4
        with:
          path: .lycheecache
          key: lychee-${{ runner.os }}-${{ hashFiles('**/*.md', '**/*.mdx') }}
          restore-keys: lychee-${{ runner.os }}-
      
      - name: Check markdown links
        uses: lycheeverse/lychee-action@v1
        with:
          args: >
            --config lychee.toml
            --format json
            --output .lychee-md.json
            ${{ needs.detect-changes.outputs.markdown-files || '**/*.md **/*.mdx' }}
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
      
      - name: Build site (if scope=all)
        if: ${{ env.SCOPE == 'all' && (github.event_name != 'pull_request' || needs.detect-changes.outputs.all == 'true') }}
        run: ${BUILD_COMMAND}
      
      - name: Check HTML links (if built)
        if: ${{ env.SCOPE == 'all' && (github.event_name != 'pull_request' || needs.detect-changes.outputs.all == 'true') }}
        uses: lycheeverse/lychee-action@v1
        with:
          args: >
            --config lychee.toml
            --format json
            --output .lychee-html.json
            ${OUTPUT_DIR}/**/*.html
            ${OUTPUT_DIR}/sitemap.xml
            ${OUTPUT_DIR}/feed.xml
            ${OUTPUT_DIR}/rss.xml
      
      - uses: actions/upload-artifact@v4
        with:
          name: lychee-results
          path: .lychee-*.json

  markdown-lint:
    needs: detect-changes
    runs-on: ubuntu-latest
    continue-on-error: ${{ env.MODE == 'report-only' }}
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
      
      - name: Setup pnpm
        if: env.PM == 'pnpm'
        uses: pnpm/action-setup@v2
        with:
          version: 8
      
      - name: Setup yarn
        if: env.PM == 'yarn'
        run: npm install -g yarn
      
      - name: Install dependencies
        run: |
          if [ "${PM}" = "yarn" ]; then
            yarn add -D markdownlint-cli2
          else
            ${PM} install -D markdownlint-cli2
          fi
      
      - name: Lint markdown
        run: |
          if [ "${{ github.event_name }}" = "pull_request" ] && [ -n "${{ needs.detect-changes.outputs.markdown-files }}" ]; then
            npx markdownlint-cli2 ${{ needs.detect-changes.outputs.markdown-files }} --config .markdownlint.jsonc --output .markdownlint.json
          else
            npx markdownlint-cli2 "**/*.md" "**/*.mdx" "!**/node_modules/**" "!**/_site/**" "!**/dist/**" "!**/build/**" "!.aws-sam/**" "!**/.cache/**" "!**/coverage/**" "!**/vendor/**" "!.claude/**" --config .markdownlint.jsonc --output .markdownlint.json || true
          fi
      
      - uses: actions/upload-artifact@v4
        with:
          name: markdown-results
          path: .markdownlint.json

  frontmatter-check:
    needs: detect-changes
    runs-on: ubuntu-latest
    continue-on-error: ${{ env.MODE == 'report-only' }}
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
      
      - name: Setup pnpm
        if: env.PM == 'pnpm'
        uses: pnpm/action-setup@v2
        with:
          version: 8
      
      - name: Setup yarn
        if: env.PM == 'yarn'
        run: npm install -g yarn
      
      - name: Install dependencies
        run: |
          if [ "${PM}" = "yarn" ]; then
            yarn add -D gray-matter fast-glob toml
          else
            ${PM} install -D gray-matter fast-glob toml
          fi
      
      - name: Check frontmatter
        run: node tools/frontmatter-check.mjs > .frontmatter.json
        env:
          REQUIRED_KEYS: "${FRONTMATTER_REQUIRED}"
          FILE_GLOBS: "${{ needs.detect-changes.outputs.markdown-files || '**/*.md **/*.mdx' }}"
          OUTPUT_JSON: "true"
      
      - uses: actions/upload-artifact@v4
        with:
          name: frontmatter-results
          path: .frontmatter.json

  docs-guardrails:
    needs: [broken-links, markdown-lint, frontmatter-check]
    runs-on: ubuntu-latest
    if: always()
    steps:
      - uses: actions/checkout@v4
      
      - name: Download all results
        uses: actions/download-artifact@v4
        with:
          pattern: "*-results"
      
      - name: Evaluate guardrails
        uses: actions/github-script@v7
        with:
          script: |
            const fs = require('fs');
            const thresholds = {
              broken_links: ${THRESHOLDS_BROKEN_LINKS},
              markdown_issues: ${THRESHOLDS_MARKDOWN},
              frontmatter_issues: ${THRESHOLDS_FRONTMATTER}
            };
            
            let results = {
              links: { failed: 0, passed: 0 },
              markdown: { issues: 0 },
              frontmatter: { issues: 0 }
            };
            
            // Parse link results
            try {
              if (fs.existsSync('lychee-results/.lychee-md.json')) {
                const linkData = JSON.parse(fs.readFileSync('lychee-results/.lychee-md.json'));
                results.links.failed = linkData.fail_map ? Object.keys(linkData.fail_map).length : 0;
              }
              if (fs.existsSync('lychee-results/.lychee-html.json')) {
                const htmlData = JSON.parse(fs.readFileSync('lychee-results/.lychee-html.json'));
                results.links.failed += htmlData.fail_map ? Object.keys(htmlData.fail_map).length : 0;
              }
            } catch (e) {
              console.log('Could not parse link results:', e);
            }
            
            // Parse markdown results
            try {
              if (fs.existsSync('markdown-results/.markdownlint.json')) {
                const mdData = JSON.parse(fs.readFileSync('markdown-results/.markdownlint.json'));
                // Handle both array and object formats
                if (Array.isArray(mdData)) {
                  results.markdown.issues = mdData.length;
                } else if (typeof mdData === 'object') {
                  results.markdown.issues = Object.keys(mdData).reduce((sum, file) => {
                    const fileData = mdData[file];
                    return sum + (Array.isArray(fileData) ? fileData.length : 0);
                  }, 0);
                }
              }
            } catch (e) {
              console.log('Could not parse markdown results:', e);
            }
            
            // Parse frontmatter results
            try {
              if (fs.existsSync('frontmatter-results/.frontmatter.json')) {
                const fmData = JSON.parse(fs.readFileSync('frontmatter-results/.frontmatter.json'));
                results.frontmatter.issues = (fmData.stats && fmData.stats.total_issues) || 0;
              }
            } catch (e) {
              console.log('Could not parse frontmatter results:', e);
            }
            
            // Create summary
            const summary = [];
            summary.push('## 📊 Docs Guardrails Results\n');
            summary.push('| Check | Count | Threshold | Status |');
            summary.push('|-------|--------|-----------|---------|');
            
            const linkStatus = results.links.failed <= thresholds.broken_links ? '✅' : '❌';
            summary.push(`| 🔗 Broken Links | ${results.links.failed} | ${thresholds.broken_links} | ${linkStatus} |`);
            
            const mdStatus = results.markdown.issues <= thresholds.markdown_issues ? '✅' : '❌';
            summary.push(`| 📝 Markdown Issues | ${results.markdown.issues} | ${thresholds.markdown_issues} | ${mdStatus} |`);
            
            const fmStatus = results.frontmatter.issues <= thresholds.frontmatter_issues ? '✅' : '❌';
            summary.push(`| 🏷️ Frontmatter Issues | ${results.frontmatter.issues} | ${thresholds.frontmatter_issues} | ${fmStatus} |`);
            
            const allPassed = linkStatus === '✅' && mdStatus === '✅' && fmStatus === '✅';
            
            if ('${MODE}' === 'report-only') {
              summary.push('\n> 📋 **Report-only mode** - Issues shown but not enforced');
            } else if (!allPassed) {
              summary.push('\n> ❌ **Docs guardrails failed** - Thresholds exceeded');
              core.setFailed('Docs guardrails failed - see summary above');
            } else {
              summary.push('\n> ✅ **Docs guardrails passed** - All checks within thresholds');
            }
            
            // Post comment on PR
            if (context.eventName === 'pull_request') {
              await github.rest.issues.createComment({
                ...context.repo,
                issue_number: context.issue.number,
                body: summary.join('\n')
              });
            }
            
            // Write summary
            await core.summary
              .addRaw(summary.join('\n'))
              .write();
```

#### `lychee.toml`
```toml
# Lychee configuration for link checking
max_redirects = 10
max_retries = 2
max_concurrency = 16
timeout = 30
method = "get"
insecure = false
accept = [200, 203, 206]
glob_ignore_case = true
exclude_all_private = false

# Headers
headers = []

# Cache
cache = true
max_cache_age = 86400

# Exclude patterns
exclude = [
  "^mailto:",
  "^tel:",
  "^javascript:",
  "^#",
  "^/$"
]

exclude_path = [
  "node_modules",
  "_site", 
  "dist",
  "build",
  ".git",
  ".cache",
  "coverage",
  "vendor",
  # AWS SAM build artifacts
  ".aws-sam",
  # Monorepo workspace excludes
  "site/_site",
  "site/node_modules",
  "discord/node_modules",
  "scripts/node_modules",
  "src/node_modules",
  # Test files
  "test",
  "tests",
  "__tests__",
  # Claude-specific
  ".claude",
  # Framework-specific excludes
  ${FRAMEWORK_EXCLUDES}
  # Auto-detected excludes
  ${DETECTED_EXCLUDES}
]

# User agent
user_agent = "lychee/0.14 (compatible; +https://github.com/lycheeverse/lychee)"

# Retry settings
retry_wait_time = 5
max_retry_attempts = 2

# Include/exclude by file type
include = []
exclude_file = []

# Cookies
cookies = []

# Basic auth
basic_auth = []

# GitHub token is set via env var GITHUB_TOKEN

# Custom headers per domain
[[headers]]
name = "Accept"
value = "text/html"

# Remap URLs (useful for local dev)
[[remap]]
from = "http://localhost:8080"
to = "https://example.com"

# Per-domain settings
# Generated from IGNORE_DOMAINS environment variable
# Example output when IGNORE_DOMAINS="staging.example.com,dev.example.com":
# [[skip]]
# pattern = "staging.example.com"
# 
# [[skip]]
# pattern = "dev.example.com"
${DOMAIN_CONFIGS}
```

#### `.markdownlint.jsonc`
```jsonc
{
  // Friendly defaults that avoid noise
  "default": true,
  "MD001": true,     // Heading levels increment
  "MD003": {         // Heading style
    "style": "atx"
  },
  "MD004": {         // Unordered list style
    "style": "dash"
  },
  "MD005": true,     // List indentation
  "MD007": {         // List indentation
    "indent": 2
  },
  "MD009": true,     // Trailing spaces
  "MD010": true,     // Hard tabs
  "MD011": true,     // Reversed link syntax
  "MD012": true,     // Multiple blank lines
  "MD013": false,    // Line length (off - too noisy)
  "MD014": true,     // Dollar signs before commands
  "MD018": true,     // No space after hash
  "MD019": true,     // Multiple spaces after hash
  "MD022": true,     // Headings surrounded by blank lines
  "MD023": true,     // Headings start at beginning of line
  "MD025": true,     // Single H1
  "MD026": true,     // Trailing punctuation in heading
  "MD027": true,     // Multiple spaces after blockquote
  "MD028": true,     // Blank line inside blockquote
  "MD029": {         // Ordered list prefix
    "style": "ordered"
  },
  "MD030": true,     // Spaces after list markers
  "MD031": true,     // Fenced code blocks surrounded by blank lines
  "MD032": true,     // Lists surrounded by blank lines
  "MD033": false,    // Inline HTML (allowed)
  "MD034": true,     // Bare URLs
  "MD035": {         // Horizontal rule style
    "style": "---"
  },
  "MD036": true,     // Emphasis used instead of heading
  "MD037": true,     // Spaces inside emphasis
  "MD038": true,     // Spaces inside code spans
  "MD039": true,     // Spaces inside links
  "MD040": true,     // Fenced code language
  "MD041": true,     // First line is top level heading
  "MD042": true,     // No empty links
  "MD043": false,    // Required heading structure (off)
  "MD044": false,    // Proper names (off - too specific)
  "MD045": false,    // No alt text (handled by custom frontmatter check)
  "MD046": {         // Code block style
    "style": "fenced"
  },
  "MD047": true,     // File ends with newline
  "MD048": {         // Code fence style
    "style": "backtick"
  },
  "MD049": false,    // Emphasis style (allow both * and _)
  "MD050": false,    // Strong style (allow both ** and __)
  "MD051": true,     // Link fragments
  "MD052": true,     // Reference links and images
  "MD053": true      // Link/image reference definitions
}
```

#### `tools/frontmatter-check.mjs`
```javascript
#!/usr/bin/env node
/**
 * Frontmatter validation with enhanced checks
 * - Required fields validation
 * - Date format validation
 * - Image alt text checking
 * - Outputs JSON for CI parsing
 */

import fg from 'fast-glob';
import fs from 'node:fs';
import matter from 'gray-matter';
import toml from 'toml';

// Configuration from environment with intelligent defaults
let detectedPatterns = { requiredFields: [] };
try {
  detectedPatterns = await analyzeContentPatterns(); // optionally injected at generation time
} catch (_) {
  detectedPatterns = { requiredFields: [] }; // default safely
}
const REQUIRED_KEYS = process.env.REQUIRED_KEYS !== undefined
  ? process.env.REQUIRED_KEYS.split(',').map(s => s.trim()).filter(Boolean)
  : (detectedPatterns.requiredFields.length > 0 
     ? detectedPatterns.requiredFields 
     : ['title', 'date']);

// If REQUIRED_KEYS is explicitly empty string, don't require any fields
if (process.env.REQUIRED_KEYS === '') {
  REQUIRED_KEYS.length = 0;
}

const FILE_GLOBS = (process.env.FILE_GLOBS || '**/*.md **/*.mdx')
  .split(/\s+/)
  .filter(Boolean);

const OUTPUT_JSON = process.env.OUTPUT_JSON === 'true';

// Find all files
const files = fg.sync(FILE_GLOBS, { 
  ignore: ['**/node_modules/**', '**/_site/**', '**/dist/**', '**/build/**'],
  dot: false 
});

const issues = [];
const stats = {
  total_files: files.length,
  files_with_issues: 0,
  total_issues: 0,
  by_type: {}
};

// Validation helpers
const isValidDate = (value) => {
  if (!value) return false;
  const date = new Date(value);
  return !isNaN(date.getTime()) && date.getFullYear() > 1900 && date.getFullYear() < 2100;
};

const checkImageAltText = (content, file) => {
  const imageRegex = /!\[([^\]]*)\]\([^)]+\)/g;
  let match;
  const missingAlt = [];
  
  while ((match = imageRegex.exec(content)) !== null) {
    if (!match[1] || match[1].trim() === '') {
      const line = content.substring(0, match.index).split('\n').length;
      missingAlt.push({ line, text: match[0] });
    }
  }
  
  return missingAlt;
};

// Process each file
for (const file of files) {
  const content = fs.readFileSync(file, 'utf8');
  const fileIssues = [];
  
  // Parse frontmatter (support YAML and TOML)
  let parsed;
  try {
    parsed = matter(content, {
      engines: {
        toml: toml.parse.bind(toml)
      }
    });
  } catch (e) {
    fileIssues.push({
      type: 'parse-error',
      message: `Failed to parse frontmatter: ${e.message}`
    });
    issues.push({ file, issues: fileIssues });
    continue;
  }
  
  const { data: frontmatter, content: body } = parsed;
  
  // Check required fields
  for (const key of REQUIRED_KEYS) {
    const value = frontmatter[key];
    
    if (value === undefined || value === null || 
        (typeof value === 'string' && value.trim() === '')) {
      fileIssues.push({
        type: 'missing-field',
        field: key,
        message: `Required field '${key}' is missing or empty`
      });
    }
    
    // Special validation for date fields
    if (key.toLowerCase().includes('date') && value !== undefined) {
      if (!isValidDate(value)) {
        fileIssues.push({
          type: 'invalid-date',
          field: key,
          value: value,
          message: `Invalid date format for '${key}': ${value}`
        });
      }
    }
  }
  
  // Check for common issues
  if (frontmatter.draft === 'true' || frontmatter.draft === true) {
    fileIssues.push({
      type: 'draft-content',
      message: 'File is marked as draft'
    });
  }
  
  // Check image alt text
  const missingAlt = checkImageAltText(body, file);
  if (missingAlt.length > 0) {
    fileIssues.push({
      type: 'missing-alt-text',
      count: missingAlt.length,
      locations: missingAlt,
      message: `${missingAlt.length} image(s) missing alt text`
    });
  }
  
  // Add to results if there are issues
  if (fileIssues.length > 0) {
    issues.push({ file, issues: fileIssues });
    stats.files_with_issues++;
    stats.total_issues += fileIssues.length;
    
    // Count by type
    for (const issue of fileIssues) {
      stats.by_type[issue.type] = (stats.by_type[issue.type] || 0) + 1;
    }
  }
}

// Output results
if (OUTPUT_JSON) {
  // JSON output for CI
  console.log(JSON.stringify({ issues, stats }, null, 2));
} else {
  // Human-readable output
  if (issues.length === 0) {
    console.log('✅ All frontmatter checks passed!');
    console.log(`   Checked ${files.length} files`);
  } else {
    console.error(`❌ Found ${stats.total_issues} issue(s) in ${stats.files_with_issues} file(s):\n`);
    
    for (const { file, issues: fileIssues } of issues) {
      console.error(`📄 ${file}`);
      for (const issue of fileIssues) {
        console.error(`   • [${issue.type}] ${issue.message}`);
      }
      console.error('');
    }
    
    console.error('Summary by type:');
    for (const [type, count] of Object.entries(stats.by_type)) {
      console.error(`  ${type}: ${count}`);
    }
  }
}

// Exit with error if issues found and not in JSON mode
if (!OUTPUT_JSON && issues.length > 0) {
  process.exit(1);
}
```

### Step 3: Update Package.json Scripts

```javascript
// Add these scripts to package.json
const scripts = {
  // Note: lychee requires separate installation (brew install lychee or cargo install lychee)
  // For CI-only usage, remove these local scripts and rely on the GitHub Action
  "check:links": "echo 'Note: lychee must be installed separately (brew install lychee)' && lychee --config lychee.toml '**/*.md' '**/*.mdx'",
  "check:links:all": "npm run build && lychee --config lychee.toml '_site/**/*.html' '_site/sitemap.xml'",
  "check:md": "markdownlint-cli2 '**/*.md' '!**/node_modules/**' '!**/_site/**' '!**/dist/**' '!**/build/**' '!.aws-sam/**' '!**/.cache/**' '!**/coverage/**' '!**/vendor/**' '!.claude/**'",
  "check:frontmatter": "node tools/frontmatter-check.mjs",
  "check:all": "npm run check:md && npm run check:frontmatter",
  "fix:md": "markdownlint-cli2 --fix '**/*.md' '!**/node_modules/**' '!**/_site/**' '!**/dist/**' '!**/build/**' '!.aws-sam/**' '!**/.cache/**' '!**/coverage/**' '!**/vendor/**' '!.claude/**'"
};

// Add devDependencies
const devDependencies = {
  "markdownlint-cli2": "^0.11.0",
  "gray-matter": "^4.0.3",
  "fast-glob": "^3.3.2",
  "toml": "^3.0.0"
};
```

### Step 4: Create Documentation

#### `docs/docs-guardrails.md`
```markdown
# Docs Guardrails

This repository uses automated quality checks to maintain content standards.

## Overview

Three types of checks run on every pull request:

1. **🔗 Broken Links** - Validates all internal and external links
2. **📝 Markdown Standards** - Enforces consistent markdown formatting
3. **🏷️ Frontmatter Validation** - Ensures required metadata is present

## Current Configuration

- **Mode**: ${MODE}
- **Thresholds**:
  - Broken links: ${THRESHOLDS_BROKEN_LINKS}
  - Markdown issues: ${THRESHOLDS_MARKDOWN}
  - Frontmatter issues: ${THRESHOLDS_FRONTMATTER}
- **Required frontmatter**: ${FRONTMATTER_REQUIRED}

## Running Checks Locally

```bash
# Check everything
${PM} run check:all

# Check specific areas
${PM} run check:links      # Links only
${PM} run check:md         # Markdown only
${PM} run check:frontmatter # Frontmatter only

# Auto-fix markdown issues
${PM} run fix:md
```

## Tuning the Guardrails

### Switching to Enforce Mode

Edit `.github/workflows/docs-guardrails.yml` and change:
```yaml
continue-on-error: true  # Change to false
```

### Adjusting Thresholds

Modify the workflow environment variables:
```yaml
env:
  THRESHOLDS_BROKEN_LINKS: 5
  THRESHOLDS_MARKDOWN: 20
  THRESHOLDS_FRONTMATTER: 10
```

### Customizing Markdown Rules

Edit `.markdownlint.jsonc` to enable/disable specific rules.

### Ignoring Specific Files

Add patterns to the respective config files:
- Links: Add to `exclude_path` in `lychee.toml`
- Markdown: Add to globs in scripts
- Frontmatter: Uses same globs as markdown

## Common Issues

### False Positive Broken Links

Add domains to ignore list in `lychee.toml`:
```toml
[[remap]]
from = "http://localhost:8080"
to = "https://example.com"
```

### Markdown Rule Too Strict

Disable specific rules in `.markdownlint.jsonc`:
```jsonc
{
  "MD013": false  // Line length
}
```

### Missing Frontmatter

Ensure all markdown files have required fields:
```yaml
---
title: Page Title
date: 2024-01-01
---
```
```

#### Update README.md

```markdown
<!-- DOCS-GUARDRAILS:START -->
## 🔍 Docs Guardrails

[![Docs Guardrails](https://github.com/${GITHUB_REPO}/actions/workflows/docs-guardrails.yml/badge.svg)](https://github.com/${GITHUB_REPO}/actions/workflows/docs-guardrails.yml)

This repository enforces quality standards for all content:
- **Broken links** are detected and reported
- **Markdown formatting** follows consistent standards
- **Required metadata** is validated in all files

Currently in **${MODE}** mode. See [docs guardrails documentation](docs/docs-guardrails.md) for details.
<!-- DOCS-GUARDRAILS:END -->
```

### Step 5: Implementation Flow

1. **Create feature branch**
   ```bash
   git checkout -b feat/docs-guardrails-$(date +%Y%m%d-%H%M)
   ```

2. **Install dependencies**
   ```bash
   ${PM} install -D markdownlint-cli2 gray-matter fast-glob toml
   ```

3. **Create all files with proper substitutions**

4. **Run local validation**
   ```bash
   ${PM} run check:all
   ```

5. **Commit in logical chunks**
   ```bash
   git add lychee.toml .markdownlint.jsonc
   git commit -m "chore: add docs guardrails configurations"
   
   git add tools/frontmatter-check.mjs package.json
   git commit -m "chore: add docs guardrails tools and scripts"
   
   git add .github/workflows/docs-guardrails.yml
   git commit -m "ci: add docs guardrails workflow"
   
   git add docs/docs-guardrails.md README.md
   git commit -m "docs: add docs guardrails documentation"
   ```

6. **Push and create PR**
   ```bash
   git push -u origin HEAD
   gh pr create --title "feat: add docs guardrails for content quality" \
                --body "$PR_BODY"
   ```

## Variable Substitutions

The command should replace these placeholders:
- `${MODE}` → input mode value
- `${PM}` → detected package manager
- `${BUILD_COMMAND}` → detected or input build command
- `${OUTPUT_DIR}` → detected or input output directory
- `${FRONTMATTER_REQUIRED}` → input frontmatter keys
- `${THRESHOLDS_*}` → input threshold values
- `${DOMAIN_CONFIGS}` → generated lychee TOML skip sections from IGNORE_DOMAINS using generateDomainConfigs()
- `${GITHUB_REPO}` → detected from git remote
- `${FRAMEWORK_EXCLUDES}` → framework-specific paths to exclude (e.g., ".next", ".nuxt")
- `${DETECTED_EXCLUDES}` → auto-detected exclude patterns from codebase analysis

## Lessons Learned & Best Practices

### 1. Start with Relaxed Rules
- Disable noisy markdown rules: MD022 (headings), MD031 (code blocks), MD032 (lists)
- These enforce style preferences, not actual errors
- Focus on real issues: broken links, missing code languages, invalid syntax

### 2. Frontmatter is Often Optional
- Many codebases don't use frontmatter
- Default to empty string (`FRONTMATTER_REQUIRED: ""`) 
- Only require fields if the project actively uses them

### 3. Comprehensive Exclusions are Critical
- Always exclude `.aws-sam/` for AWS projects
- Use `**/node_modules/**` pattern for monorepos
- Exclude `.claude/` and other AI-specific directories
- Test files often have intentionally broken examples

### 4. Lychee Configuration Gotchas
- Remove conflicting `headers = []` if using `[[headers]]` sections
- Use duration strings like `"1d"` instead of seconds for cache
- Keep config simple - complex features often cause parse errors
- Accept redirects: `accept = [200, 203, 206, 301, 302, 303, 307, 308]`

### 5. Realistic Thresholds
- Start with higher thresholds in report-only mode
- First run often shows 100s or 1000s of issues
- Markdown: 20-50 is reasonable after excluding noisy rules
- Frontmatter: 10 if required, 0 if not using frontmatter
- Broken links: 5-10 for external link flakiness

### 6. Auto-Fix Capabilities
- `markdownlint-cli2 --fix` can resolve ~70% of issues automatically
- Fixes: trailing spaces, newlines, list formatting
- Doesn't fix: code languages, duplicate headings, bare URLs
- Always commit before running auto-fix
```