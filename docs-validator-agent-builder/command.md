# Docs Validator Agent Builder
---
description: >
  Install a specialized Claude Code subagent that automatically monitors, validates, and fixes documentation issues in your codebase. The agent checks broken links, validates markdown standards, ensures frontmatter completeness, and maintains documentation quality autonomously.
allowed_tools:
  - Read          # Read existing configs
  - Write         # Create new files
  - MultiEdit     # Update existing files
  - Bash          # Run commands
  - Grep          # Find patterns
  - LS            # List directories
  - TodoWrite     # Track progress
tags:
  - documentation
  - quality
  - validation
  - subagent
category: testing
---

## Key Features

This command installs a documentation validation subagent that:

1. **Detects Documentation Issues** - Broken links, formatting, missing metadata
2. **Auto-fixes Common Problems** - Applies markdown formatting fixes
3. **AI-powered Improvements** - Uses Claude to fix complex documentation issues
4. **Verifies Quality** - Ensures documentation meets standards
5. **Reports Progress** - Generates detailed quality reports

## Arguments

```
/docs-validator-agent-builder
[frameworks=<auto|eleventy,hugo,jekyll,docusaurus,astro>]
[install_global=<true|false>]
[mode=<report-only|enforce>]
[validation_scope=<markdown-only|all>]
[thresholds_broken_links=<number>]
[thresholds_markdown=<number>]
[thresholds_frontmatter=<number>]
[frontmatter_required=<comma-separated-fields>]
```

## What This Command Does

1. **Installs Documentation Tools**:
   - Link checker (lychee) for broken link detection
   - Markdown linter (markdownlint-cli2) for formatting
   - Frontmatter validator (custom script)
   - Auto-fix utilities
   
2. **Creates Docs Validator Subagent**:
   - Sets up `.claude/agents/docs-validator.md`
   - Configures detection patterns and fix strategies
   - Integrates with your documentation workflow

3. **Updates Project Configuration**:
   - Adds validation scripts to package.json
   - Updates CLAUDE.md with usage instructions
   - Creates quality baseline

## Quick Start

```bash
# Install with auto-detection of frameworks
/docs-validator-agent-builder

# Install for specific frameworks
/docs-validator-agent-builder frameworks=eleventy,docusaurus

# Install globally for all projects
/docs-validator-agent-builder install_global=true

# Start in enforce mode
/docs-validator-agent-builder mode=enforce
```

## How It Works

### 1. Detection Phase
The subagent monitors for documentation-related requests and triggers when:
- User mentions "documentation", "broken links", "markdown", "validate"
- Documentation files are changed
- Quality checks show regressions

### 2. Scanning Phase
```bash
# Check broken links
lychee --config .claude/docs-tools/lychee.toml **/*.md

# Validate markdown
markdownlint-cli2 **/*.md --config .markdownlint.jsonc

# Check frontmatter
node .claude/docs-tools/frontmatter-check.mjs
```

### 3. Fix Phase
The subagent applies fixes in order:
1. **Auto-fixes** - Markdown formatting via markdownlint --fix
2. **Link updates** - Fix internal broken links
3. **AI improvements** - Claude analyzes and fixes complex issues

### 4. Verification Phase
```bash
# Re-run all checks
npm run docs:validate

# Generate report
npm run docs:report
```

## Subagent Capabilities

### Framework Support
- **Eleventy**: Nunjucks/Liquid templates, collections
- **Docusaurus**: MDX support, versioned docs
- **Hugo**: Shortcodes, multilingual
- **Jekyll**: Liquid templates, GitHub Pages
- **Astro**: MDX, content collections
- **Generic**: Any markdown-based site

### Validation Checks
- Broken internal/external links
- Markdown formatting standards
- Required frontmatter fields
- Image alt text validation
- Code block language specification
- Heading hierarchy
- File naming conventions

### Integration with Claude Code
Once installed, simply ask Claude Code to validate documentation:
- "Check for broken links in the docs"
- "Fix markdown formatting issues"
- "Validate all documentation"
- "Ensure frontmatter is complete"

The subagent will automatically engage and handle the validation workflow.

## Configuration

### Validation Thresholds
```yaml
# .claude/agents/docs-validator.yaml
thresholds:
  broken_links: 5      # Max allowed broken links
  markdown_issues: 20  # Max markdown problems
  frontmatter: 10      # Max metadata issues
```

### Custom Rules
Add your own validation patterns:
```yaml
patterns:
  - name: "api-docs-format"
    detect: "^api/"
    require: ["endpoint", "method", "parameters"]
    frameworks: ["docusaurus"]
```

## Example Session

```
User: "Check our documentation for issues"

Claude Code: I'll use the docs-validator subagent to analyze your documentation.

Docs Validator: 
[1/4] Scanning documentation...
  - Found 234 markdown files
  - Detected Docusaurus framework
  - Using custom frontmatter requirements
  
[2/4] Running quality checks...
  ✗ 8 broken links found:
    - docs/api/auth.md → /api/v1/login (404)
    - guides/setup.md → https://oldsite.com/install (moved)
  ✗ 32 markdown issues:
    - 12 missing code block languages
    - 20 inconsistent list formatting
  ✓ All frontmatter valid
  
[3/4] Applying fixes...
  ✓ Fixed 20 list formatting issues
  ✓ Added languages to 10 code blocks
  ✓ Updated 2 internal broken links
  ! 6 external links need manual review
  ! 2 code blocks have ambiguous language
  
[4/4] Documentation report saved to .claude/docs-report.md

Fixed 32/40 issues automatically. 8 issues need manual review.
```

## Troubleshooting

### Tool Installation Issues
```bash
# macOS: Install with Homebrew
brew install lychee

# Linux: Install via cargo
cargo install lychee

# Node.js tools
npm install -g markdownlint-cli2
```

### Framework Detection
The command auto-detects frameworks from:
- Configuration files (eleventy.js, docusaurus.config.js, etc.)
- Package.json dependencies
- Directory structure

### Common Issues
- **False positive links**: Add to `lychee.toml` exclusions
- **Strict markdown rules**: Adjust `.markdownlint.jsonc`
- **Custom frontmatter**: Update validation script

## Best Practices

1. **Start in Report Mode** - See issues before enforcing
2. **Set Realistic Thresholds** - Some external links may fail
3. **Review AI Fixes** - Complex changes need verification
4. **Maintain Baselines** - Track documentation quality over time
5. **Customize for Your Site** - Adjust rules to match style guide

## Security Notes

- All tools run locally only
- No documentation sent to external services
- AI improvements generated by Claude with full context
- All changes shown before applying

## Implementation

### Step 1: Detect Frameworks

```javascript
// Auto-detect static site generators
const detectFrameworks = async () => {
  const frameworks = new Set();
  
  // Check for framework-specific files
  if (await fileExists('.eleventy.js') || await fileExists('eleventy.config.js')) {
    frameworks.add('eleventy');
  }
  
  if (await fileExists('docusaurus.config.js')) {
    frameworks.add('docusaurus');
  }
  
  if (await fileExists('hugo.toml') || await fileExists('config.toml')) {
    frameworks.add('hugo');
  }
  
  if (await fileExists('_config.yml')) {
    frameworks.add('jekyll');
  }
  
  if (await fileExists('astro.config.mjs')) {
    frameworks.add('astro');
  }
  
  // Check package.json for framework dependencies
  if (await fileExists('package.json')) {
    const pkg = JSON.parse(await readFile('package.json'));
    const deps = { ...pkg.dependencies, ...pkg.devDependencies };
    
    if (deps['@11ty/eleventy']) frameworks.add('eleventy');
    if (deps['@docusaurus/core']) frameworks.add('docusaurus');
    if (deps['astro']) frameworks.add('astro');
  }
  
  return frameworks.size > 0 ? Array.from(frameworks) : ['generic'];
};
```

### Step 2: Install Documentation Tools

```javascript
// Install validation tools
const installTools = async () => {
  console.log('Installing documentation validators...');
  
  // Install Node.js tools
  const pm = await detectPackageManager();
  await bash(`${pm} install --save-dev markdownlint-cli2 gray-matter fast-glob toml 2>/dev/null || true`);
  
  // Install lychee (link checker)
  console.log('Installing lychee link checker...');
  try {
    // Try different installation methods
    await bash('command -v lychee || brew install lychee 2>/dev/null || cargo install lychee --locked 2>/dev/null || true');
  } catch (e) {
    console.log('Note: lychee installation may require manual setup');
  }
  
  // Create tools directory
  await bash('mkdir -p .claude/docs-tools');
};
```

### Step 3: Create Validation Wrapper

```javascript
// Create universal validation wrapper
const createValidationWrapper = async () => {
  const wrapperContent = `#!/usr/bin/env bash
# Universal documentation validator wrapper

set -euo pipefail

# Default values
ACTION="validate"
OUTPUT="docs-report.json"
FIX="false"

# Parse arguments
while [[ $# -gt 0 ]]; do
  case $1 in
    --action) ACTION="$2"; shift 2 ;;
    --output) OUTPUT="$2"; shift 2 ;;
    --fix) FIX="true"; shift ;;
    *) echo "Unknown option: $1"; exit 1 ;;
  esac
done

# Create temporary directory for results
TEMP_DIR=$(mktemp -d)
trap "rm -rf $TEMP_DIR" EXIT

# Initialize results
echo '{"timestamp": "'$(date -u +"%Y-%m-%dT%H:%M:%SZ")'", "results": {}}' > "$TEMP_DIR/results.json"

# Run validation based on action
case "$ACTION" in
  validate|all)
    echo "Running all documentation validators..."
    
    # Check broken links
    if command -v lychee &> /dev/null; then
      echo "Checking for broken links..."
      lychee --config .claude/docs-tools/lychee.toml \\
        --format json \\
        --output "$TEMP_DIR/links.json" \\
        '**/*.md' '**/*.mdx' || true
    fi
    
    # Validate markdown
    if command -v markdownlint-cli2 &> /dev/null; then
      echo "Validating markdown formatting..."
      if [[ "$FIX" == "true" ]]; then
        markdownlint-cli2 --fix '**/*.md' '**/*.mdx' \\
          --config .markdownlint.jsonc || true
      else
        markdownlint-cli2 '**/*.md' '**/*.mdx' \\
          --config .markdownlint.jsonc \\
          --output "$TEMP_DIR/markdown.json" || true
      fi
    fi
    
    # Check frontmatter
    if [[ -f ".claude/docs-tools/frontmatter-check.mjs" ]]; then
      echo "Checking frontmatter..."
      node .claude/docs-tools/frontmatter-check.mjs > "$TEMP_DIR/frontmatter.json" || true
    fi
    ;;
    
  links)
    echo "Checking links only..."
    lychee --config .claude/docs-tools/lychee.toml \\
      --format json \\
      --output "$TEMP_DIR/links.json" \\
      '**/*.md' '**/*.mdx'
    ;;
    
  markdown)
    echo "Checking markdown only..."
    if [[ "$FIX" == "true" ]]; then
      markdownlint-cli2 --fix '**/*.md' '**/*.mdx' \\
        --config .markdownlint.jsonc
    else
      markdownlint-cli2 '**/*.md' '**/*.mdx' \\
        --config .markdownlint.jsonc \\
        --output "$TEMP_DIR/markdown.json"
    fi
    ;;
    
  frontmatter)
    echo "Checking frontmatter only..."
    node .claude/docs-tools/frontmatter-check.mjs > "$TEMP_DIR/frontmatter.json"
    ;;
    
  *)
    echo "Unknown action: $ACTION"
    exit 1
    ;;
esac

# Merge results
node -e "
const fs = require('fs');
const results = JSON.parse(fs.readFileSync('$TEMP_DIR/results.json'));

// Add link results
if (fs.existsSync('$TEMP_DIR/links.json')) {
  try {
    results.results.links = JSON.parse(fs.readFileSync('$TEMP_DIR/links.json'));
  } catch (e) {
    results.results.links = { error: e.message };
  }
}

// Add markdown results
if (fs.existsSync('$TEMP_DIR/markdown.json')) {
  try {
    results.results.markdown = JSON.parse(fs.readFileSync('$TEMP_DIR/markdown.json'));
  } catch (e) {
    results.results.markdown = { error: e.message };
  }
}

// Add frontmatter results
if (fs.existsSync('$TEMP_DIR/frontmatter.json')) {
  try {
    results.results.frontmatter = JSON.parse(fs.readFileSync('$TEMP_DIR/frontmatter.json'));
  } catch (e) {
    results.results.frontmatter = { error: e.message };
  }
}

fs.writeFileSync('$OUTPUT', JSON.stringify(results, null, 2));
"

echo "Validation complete. Results saved to $OUTPUT"
`;

  await write('.claude/docs-tools/docvalidate', wrapperContent);
  await bash('chmod +x .claude/docs-tools/docvalidate');
};
```

### Step 4: Create Docs Validator Subagent

```javascript
// Create the subagent with configuration
const createSubagent = async (config) => {
  const { frameworks, installGlobal, mode, validationScope, thresholds, frontmatterRequired } = config;
  const agentDir = installGlobal === 'true' ? `${process.env.HOME}/.claude/agents` : '.claude/agents';
  
  const subagentContent = `---
name: docs-validator
description: Analyzes documentation quality, identifies issues, applies fixes, and maintains documentation standards across multiple static site frameworks
tools: all
---

You are a documentation quality specialist with expertise in validating, analyzing, and improving documentation across multiple static site generators. Your mission is to ensure documentation is accurate, well-formatted, and maintainable.

## Core Capabilities

1. **Documentation Validation**
   - Broken link detection (internal/external)
   - Markdown formatting standards
   - Frontmatter completeness
   - Image alt text validation
   - Code block language specification

2. **Fix Techniques**
   - Automatic markdown formatting fixes
   - Internal link resolution
   - Frontmatter field addition
   - Image optimization
   - Cross-reference validation

3. **Framework-Specific Knowledge**
   - Eleventy: Collections, layouts, Nunjucks/Liquid
   - Docusaurus: Versioning, MDX, sidebars
   - Hugo: Shortcodes, taxonomies, multilingual
   - Jekyll: Liquid, GitHub Pages compatibility
   - Astro: Content collections, MDX components

## Workflow

When asked to validate documentation, follow this systematic approach:

### 1. Initial Assessment
- Understand validation scope (all docs, specific sections, etc.)
- Identify the static site framework
- Determine quality metrics to check

### 2. Scanning Phase
Use the docvalidate tool:

\`\`\`bash
# Validate everything
.claude/docs-tools/docvalidate --action all

# Check specific aspects
.claude/docs-tools/docvalidate --action links
.claude/docs-tools/docvalidate --action markdown
.claude/docs-tools/docvalidate --action frontmatter

# Auto-fix markdown issues
.claude/docs-tools/docvalidate --action markdown --fix
\`\`\`

### 3. Analysis Phase
- Parse validation results from JSON output
- Identify top issues by severity
- Determine which can be auto-fixed

### 4. Fix Phase
Apply fixes in order of safety:

1. **Automatic Fixes** (safe)
   - Markdown formatting via \`markdownlint-cli2 --fix\`
   - Trailing whitespace removal
   - Consistent list formatting
   - Code block language addition

2. **Semi-automatic Fixes** (review needed)
   - Internal broken link updates
   - Frontmatter field additions
   - Image alt text suggestions

3. **Manual Fixes** (require human input)
   - External broken links
   - Content restructuring
   - Complex cross-references

### 5. Verification Phase
Always re-validate after fixes:

\`\`\`bash
# Re-run validation
.claude/docs-tools/docvalidate --action all --output post-fix-report.json

# Compare before/after
diff docs-report.json post-fix-report.json
\`\`\`

## Configuration

Current settings:
- Mode: ${mode}
- Validation scope: ${validationScope}
- Frameworks: ${frameworks.join(', ')}
- Thresholds:
  - Broken links: ${thresholds.broken_links}
  - Markdown issues: ${thresholds.markdown}
  - Frontmatter issues: ${thresholds.frontmatter}
- Required frontmatter: ${frontmatterRequired.join(', ')}

## Best Practices

1. **Prioritize User Impact** - Fix broken links before formatting
2. **Preserve Intent** - Don't change meaning when fixing
3. **Framework Awareness** - Respect framework conventions
4. **Incremental Fixes** - Fix in small batches
5. **Document Changes** - Explain why fixes were made

## Common Patterns to Look For

### Broken Links
- Relative path issues (../ vs /)
- Changed file names
- Anchor links to removed sections
- External sites that moved

### Markdown Issues
- Inconsistent heading levels
- Missing code block languages
- Improper list indentation
- Bare URLs without link syntax

### Frontmatter Problems
- Missing required fields
- Invalid date formats
- Incorrect field types
- Duplicate keys

## Framework-Specific Considerations

### Eleventy
- Check collection references
- Validate layout names
- Verify filter functions
- Test pagination links

### Docusaurus
- Validate sidebar.js references
- Check versioned docs links
- Verify MDX imports
- Test category indexes

### Hugo
- Validate shortcode usage
- Check taxonomy terms
- Verify multilingual links
- Test menu references

## Reporting

After validation, provide:
1. Summary statistics (total files, issues found)
2. Issues by category with examples
3. Fixes applied automatically
4. Issues requiring manual intervention
5. Recommendations for prevention

Remember: The goal is not just to fix issues, but to help maintain long-term documentation quality.
`;

  await bash(`mkdir -p "${agentDir}"`);
  await write(`${agentDir}/docs-validator.md`, subagentContent);
};
```

### Step 5: Create Configuration Files

```javascript
// Create lychee configuration
const createLycheeConfig = async (ignoreDomains) => {
  const domainSkips = ignoreDomains
    ? ignoreDomains.split(',').map(d => `[[skip]]\npattern = "${d.trim()}"`).join('\n\n')
    : '';
    
  const lycheeConfig = `# Lychee configuration for documentation link checking
cache = true
max_cache_age = "1d"
max_retries = 2
max_concurrency = 16
timeout = 30
accept = [200, 203, 206, 301, 302, 303, 307, 308]

# Exclude patterns
exclude = [
  "^mailto:",
  "^tel:",
  "^javascript:",
  "^#$",
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
  ".aws-sam",
  ".claude",
  "test",
  "tests",
  "__tests__"
]

# User agent
user_agent = "Mozilla/5.0 (compatible; lychee/0.14; +https://github.com/lycheeverse/lychee)"

${domainSkips}
`;

  await write('.claude/docs-tools/lychee.toml', lycheeConfig);
};

// Create markdownlint configuration  
const createMarkdownlintConfig = async () => {
  const config = {
    "default": true,
    "MD001": true,     // Heading levels increment
    "MD003": { "style": "atx" },
    "MD004": { "style": "dash" },
    "MD007": { "indent": 2 },
    "MD009": true,     // Trailing spaces
    "MD010": true,     // Hard tabs
    "MD013": false,    // Line length (too noisy)
    "MD022": false,    // Headings surrounded by blank lines (noisy)
    "MD025": true,     // Single H1
    "MD031": false,    // Fenced code blocks surrounded (noisy)
    "MD032": false,    // Lists surrounded (noisy)
    "MD033": false,    // Inline HTML allowed
    "MD040": true,     // Code block language
    "MD041": false,    // First line heading (not always needed)
    "MD046": { "style": "fenced" },
    "MD048": { "style": "backtick" }
  };
  
  await write('.markdownlint.jsonc', JSON.stringify(config, null, 2));
};

// Create frontmatter validation script
const createFrontmatterValidator = async (requiredFields) => {
  const script = `#!/usr/bin/env node
import fg from 'fast-glob';
import fs from 'node:fs';
import matter from 'gray-matter';
import toml from 'toml';

const REQUIRED_FIELDS = ${JSON.stringify(requiredFields)};
const FILE_GLOBS = ['**/*.md', '**/*.mdx'];

const files = fg.sync(FILE_GLOBS, {
  ignore: ['**/node_modules/**', '**/_site/**', '**/dist/**', '**/build/**', '.claude/**'],
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
  return !isNaN(date.getTime());
};

const checkImageAltText = (content) => {
  const imageRegex = /!\\[([^\\]]*)\\]\\([^)]+\\)/g;
  const missingAlt = [];
  let match;
  
  while ((match = imageRegex.exec(content)) !== null) {
    if (!match[1] || match[1].trim() === '') {
      const line = content.substring(0, match.index).split('\\n').length;
      missingAlt.push({ line, text: match[0] });
    }
  }
  
  return missingAlt;
};

// Process files
for (const file of files) {
  const content = fs.readFileSync(file, 'utf8');
  const fileIssues = [];
  
  try {
    const parsed = matter(content, {
      engines: { toml: toml.parse.bind(toml) }
    });
    
    const { data: frontmatter, content: body } = parsed;
    
    // Check required fields
    for (const field of REQUIRED_FIELDS) {
      if (!frontmatter[field] || (typeof frontmatter[field] === 'string' && !frontmatter[field].trim())) {
        fileIssues.push({
          type: 'missing-field',
          field,
          message: \`Required field '\${field}' is missing or empty\`
        });
      }
      
      // Date validation
      if (field.toLowerCase().includes('date') && frontmatter[field] && !isValidDate(frontmatter[field])) {
        fileIssues.push({
          type: 'invalid-date',
          field,
          value: frontmatter[field],
          message: \`Invalid date format for '\${field}'\`
        });
      }
    }
    
    // Check draft status
    if (frontmatter.draft === true || frontmatter.draft === 'true') {
      fileIssues.push({
        type: 'draft-content',
        message: 'Document is marked as draft'
      });
    }
    
    // Check image alt text
    const missingAlt = checkImageAltText(body);
    if (missingAlt.length > 0) {
      fileIssues.push({
        type: 'missing-alt-text',
        count: missingAlt.length,
        locations: missingAlt,
        message: \`\${missingAlt.length} image(s) missing alt text\`
      });
    }
    
  } catch (e) {
    fileIssues.push({
      type: 'parse-error',
      message: \`Failed to parse frontmatter: \${e.message}\`
    });
  }
  
  if (fileIssues.length > 0) {
    issues.push({ file, issues: fileIssues });
    stats.files_with_issues++;
    stats.total_issues += fileIssues.length;
    
    for (const issue of fileIssues) {
      stats.by_type[issue.type] = (stats.by_type[issue.type] || 0) + 1;
    }
  }
}

// Output JSON
console.log(JSON.stringify({ issues, stats }, null, 2));
`;

  await write('.claude/docs-tools/frontmatter-check.mjs', script);
  await bash('chmod +x .claude/docs-tools/frontmatter-check.mjs');
};
```

### Step 6: Update Project Configuration

```javascript
// Update CLAUDE.md
const updateClaudeMd = async () => {
  const claudeMdAddition = `
## Documentation Validator Subagent

This project has a specialized documentation validation subagent installed. To use it:

1. **Ask about documentation quality**: Just mention broken links, formatting, or validation
2. **The subagent will automatically**:
   - Scan all documentation for issues
   - Apply automatic fixes where possible
   - Generate AI-powered improvements for complex issues
   - Verify all changes maintain quality

### Example Requests
- "Check for broken links in the documentation"
- "Fix markdown formatting issues"
- "Validate all documentation files"
- "Ensure all docs have proper metadata"

### Available Tools
- Link checker: lychee
- Markdown linter: markdownlint-cli2
- Frontmatter validator: Custom script
- Validation wrapper: docvalidate

The subagent will handle the complete validation workflow automatically.
`;

  if (await fileExists('CLAUDE.md')) {
    const content = await read('CLAUDE.md');
    if (!content.includes('Documentation Validator Subagent')) {
      await write('CLAUDE.md', content + claudeMdAddition);
    }
  } else {
    await write('CLAUDE.md', `# CLAUDE.md\n\nThis file provides guidance to Claude Code when working with this repository.\n${claudeMdAddition}`);
  }
};

// Add npm scripts
const addNpmScripts = async () => {
  if (await fileExists('package.json')) {
    const packageJson = JSON.parse(await read('package.json'));
    packageJson.scripts = packageJson.scripts || {};
    
    packageJson.scripts['docs:validate'] = '.claude/docs-tools/docvalidate --action all';
    packageJson.scripts['docs:validate:links'] = '.claude/docs-tools/docvalidate --action links';
    packageJson.scripts['docs:validate:markdown'] = '.claude/docs-tools/docvalidate --action markdown';
    packageJson.scripts['docs:validate:frontmatter'] = '.claude/docs-tools/docvalidate --action frontmatter';
    packageJson.scripts['docs:fix'] = '.claude/docs-tools/docvalidate --action markdown --fix';
    packageJson.scripts['docs:report'] = '.claude/docs-tools/docvalidate --action all --output docs-report.json';
    
    await write('package.json', JSON.stringify(packageJson, null, 2));
  }
};
```

### Step 7: Main Execution Flow

```javascript
// Parse arguments
const parseArgs = (args) => {
  return {
    frameworks: args.frameworks || 'auto',
    installGlobal: args.install_global || 'false',
    mode: args.mode || 'report-only',
    validationScope: args.validation_scope || 'markdown-only',
    thresholds: {
      broken_links: parseInt(args.thresholds_broken_links || '5'),
      markdown: parseInt(args.thresholds_markdown || '20'),
      frontmatter: parseInt(args.thresholds_frontmatter || '10')
    },
    frontmatterRequired: args.frontmatter_required 
      ? args.frontmatter_required.split(',').map(s => s.trim())
      : ['title', 'date'],
    ignoreDomains: args.ignore_domains || ''
  };
};

// Main execution
const main = async () => {
  const config = parseArgs(args);
  
  console.log('📚 Docs Validator Builder');
  console.log('Installing documentation validation subagent for Claude Code\n');
  
  // Detect frameworks if auto
  if (config.frameworks === 'auto') {
    config.frameworks = await detectFrameworks();
  } else {
    config.frameworks = config.frameworks.split(',').map(f => f.trim());
  }
  
  console.log('✓ Detected frameworks:', config.frameworks.join(', '));
  
  // Create directories
  console.log('\nSetting up directories...');
  await bash('mkdir -p .claude/docs-tools');
  
  // Install tools
  console.log('\nInstalling documentation tools...');
  await installTools();
  
  // Create configurations
  console.log('\nCreating validation configurations...');
  await createLycheeConfig(config.ignoreDomains);
  await createMarkdownlintConfig();
  await createFrontmatterValidator(config.frontmatterRequired);
  
  // Create wrapper
  console.log('\nCreating validation wrapper...');
  await createValidationWrapper();
  
  // Create subagent
  console.log('\nCreating docs validator subagent...');
  await createSubagent(config);
  
  // Update project files
  console.log('\nUpdating project configuration...');
  await updateClaudeMd();
  await addNpmScripts();
  
  // Test installation
  console.log('\nTesting subagent...');
  const testFile = '.claude/docs-tools/test-doc.md';
  const testContent = `---
title: Test Document
date: 2024-01-01
---

# Test Document

This is a test document with [a broken link](/nonexistent) and some formatting issues.

- item1
-  item2
  - nested

\`\`\`
code without language
\`\`\`

![](image-without-alt.png)
`;
  
  await write(testFile, testContent);
  
  try {
    await bash('.claude/docs-tools/docvalidate --action all --output test-report.json');
    const report = JSON.parse(await read('test-report.json'));
    console.log('  ✓ Validation test completed');
    
    // Clean up
    await bash(`rm -f ${testFile} test-report.json`);
  } catch (error) {
    console.log('  ⚠ Validation test failed (tools may need configuration)');
  }
  
  // Summary
  const agentLocation = config.installGlobal === 'true' ? '~/.claude/agents' : '.claude/agents';
  console.log('\n✅ Docs Validator Builder Complete!');
  console.log(`\nInstalled:
  • Subagent at: ${agentLocation}/docs-validator.md
  • Tools in: .claude/docs-tools/
  • Frameworks: ${config.frameworks.join(', ')}
  • Mode: ${config.mode}`);
  
  console.log('\nNext Steps:
1. Ask Claude Code to validate documentation
2. Example: "Check for broken links in the docs"
3. The docs validator will automatically engage');
  
  // Tool status check
  console.log('\nTool Status:');
  try {
    await bash('command -v lychee');
    console.log('  ✓ lychee installed');
  } catch {
    console.log('  ⚠ lychee not found - install with: brew install lychee');
  }
  
  try {
    await bash('command -v markdownlint-cli2');
    console.log('  ✓ markdownlint-cli2 installed');
  } catch {
    console.log('  ⚠ markdownlint-cli2 not found - will be installed via npm');
  }
  
  console.log('\nDocumentation validation scripts added to package.json:
  • npm run docs:validate - Run all checks
  • npm run docs:fix - Auto-fix markdown issues
  • npm run docs:report - Generate detailed report');
};

// Execute
main().catch(console.error);
```