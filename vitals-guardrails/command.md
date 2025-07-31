# Performance & Accessibility Guardrails
---
description: >
  Add automated Lighthouse performance and axe-core accessibility checks to any
  static site. Includes Web Vitals (LCP, TBT, CLS), WCAG compliance, and trend
  analysis. Starts in report-only mode for safe adoption.
allowed_tools:
  - Read          # Read existing configs
  - Write         # Create new files
  - MultiEdit     # Update existing files
  - Bash          # Run commands
  - Grep          # Find patterns
  - LS            # List directories
  - TodoWrite     # Track progress
tags:
  - performance
  - accessibility
  - testing
  - web-vitals
category: testing
---

## Key Improvements from Real-World Implementation

This command has been battle-tested on production sites. Key improvements include:

1. **CORS Configuration** - Proper setup for local testing with separate ports
2. **Lighthouse Preset Fix** - Uses `perf` preset with `formFactor` for accurate results
3. **Specific Package Versions** - Pinned versions that work reliably together
4. **Graceful Degradation** - Handles missing accessibility scores and other edge cases
5. **Real Performance Insights** - Identifies common issues like 4+ second wait times

## Arguments

```
/perf-a11y-guardrails
[mode=<report-only|enforce>]
[preview_url=<url>]
[url_source=<auto|sitemap|list>]
[max_pages=<number>]
[device=<mobile|desktop>]
[performance_threshold=<0-100>]
[accessibility_threshold=<0-100>]
[lcp_threshold=<milliseconds>]
[tbt_threshold=<milliseconds>]
[cls_threshold=<decimal>]
[enable_trending=<true|false>]
[build_command=<command|auto>]
[output_dir=<path|auto>]
```

## What This Command Does

1. **Detects your static site setup** (11ty, Jekyll, Hugo, Gatsby, Next.js, Astro)
2. **Adds comprehensive performance testing**:
   - 🚦 Lighthouse scores (Performance, Accessibility, Best Practices, SEO)
   - 📊 Core Web Vitals (LCP, TBT, CLS, FCP, TTI)
   - ♿ WCAG compliance via axe-core
   - 📈 Trend analysis vs main branch
3. **Smart URL discovery** - finds the right pages to test
4. **Creates a PR** with everything configured

## Quick Start

```bash
# 1. Update CORS to allow test port (if testing against local backend)
# Add to your backend CORS config:
# 'http://localhost:4173', 'http://127.0.0.1:4173'

# 2. Run the command
/vitals-guardrails mode=report-only
```

## Implementation

### Step 1: Framework Detection & Validation

```javascript
// Detect static site generator and build setup
const detectFramework = async () => {
  const checks = [
    { 
      files: ['.eleventy.js', 'eleventy.config.js'], 
      name: '11ty', 
      output: '_site', 
      build: 'npm run build',
      serve: 'npx @11ty/eleventy-dev-server --port=4173'
    },
    { 
      files: ['_config.yml'], 
      name: 'jekyll', 
      output: '_site', 
      build: 'bundle exec jekyll build',
      serve: 'bundle exec jekyll serve --port 4173'
    },
    { 
      files: ['hugo.toml', 'hugo.yaml', 'hugo.json', 'config.toml'], 
      name: 'hugo', 
      output: 'public', 
      build: 'hugo',
      serve: 'hugo server --port 4173'
    },
    { 
      files: ['gatsby-config.js'], 
      name: 'gatsby', 
      output: 'public', 
      build: 'npm run build',
      serve: 'npx gatsby serve --port 4173'
    },
    { 
      files: ['astro.config.mjs', 'astro.config.js'], 
      name: 'astro', 
      output: 'dist', 
      build: 'npm run build',
      serve: 'npm run preview -- --port 4173'
    },
    { 
      files: ['next.config.js'], 
      name: 'nextjs', 
      output: 'out', 
      build: 'npm run build && npm run export',
      serve: 'npx serve out -p 4173'
    }
  ];
  
  for (const check of checks) {
    for (const file of check.files) {
      if (await fileExists(file)) {
        return check;
      }
    }
  }
  
  // Fallback: check package.json for build script
  try {
    const pkg = JSON.parse(await readFile('package.json'));
    if (pkg.scripts?.build) {
      return { 
        name: 'generic', 
        output: '_site', 
        build: 'npm run build',
        serve: 'npx http-server _site -p 4173'
      };
    }
  } catch {}
  
  return { 
    name: 'unknown', 
    output: '_site', 
    build: 'echo "No build command found"',
    serve: 'npx http-server . -p 4173'
  };
};

// Validate setup before proceeding
const validateSetup = async () => {
  const issues = [];
  
  // Check for package.json
  if (!await fileExists('package.json')) {
    issues.push('No package.json found - is this a Node.js project?');
  }
  
  // Check for common build tools
  const framework = await detectFramework();
  if (framework.name === 'unknown') {
    issues.push('Could not detect static site generator - build may fail');
  }
  
  // Check for git
  try {
    await bash('git status');
  } catch {
    issues.push('Git not initialized - cannot create branch');
  }
  
  return { framework, issues };
};
```

### Step 2: Create Configuration Files

#### `.github/workflows/perf-a11y.yml`
```yaml
name: Performance & Accessibility Checks

on:
  pull_request:
  workflow_dispatch:
    inputs:
      preview_url:
        description: 'Preview URL to test (optional)'
        required: false
        type: string
  schedule:
    - cron: '0 3 * * *'  # Nightly baseline collection

permissions:
  contents: read
  actions: read
  pull-requests: write

concurrency:
  group: perf-a11y-${{ github.ref }}
  cancel-in-progress: true

env:
  MODE: ${MODE}
  PREVIEW_URL: ${{ github.event.inputs.preview_url || '${PREVIEW_URL}' }}
  BUILD_CMD: ${BUILD_COMMAND}
  OUTPUT_DIR: ${OUTPUT_DIR}
  URL_SOURCE: ${URL_SOURCE}
  MAX_PAGES: ${MAX_PAGES}
  DEVICE: ${DEVICE}  # Auto-detected: ${AUTO_DETECTED_DEVICE}
  ENABLE_TRENDING: ${ENABLE_TRENDING}
  # Thresholds
  PERFORMANCE_THRESHOLD: ${PERFORMANCE_THRESHOLD}
  ACCESSIBILITY_THRESHOLD: ${ACCESSIBILITY_THRESHOLD}
  LCP_THRESHOLD: ${LCP_THRESHOLD}
  TBT_THRESHOLD: ${TBT_THRESHOLD}
  CLS_THRESHOLD: ${CLS_THRESHOLD}

jobs:
  setup:
    runs-on: ubuntu-latest
    outputs:
      base_url: ${{ steps.detect.outputs.base_url }}
      output_dir: ${{ steps.detect.outputs.output_dir }}
      serve_cmd: ${{ steps.detect.outputs.serve_cmd }}
      cache_key: ${{ steps.detect.outputs.cache_key }}
    steps:
      - uses: actions/checkout@v4
      
      - name: Detect environment
        id: detect
        run: |
          # Output directory detection
          OUTPUT="${OUTPUT_DIR}"
          if [ "$OUTPUT" = "auto" ] || [ -z "$OUTPUT" ]; then
            if [ -f ".eleventy.js" ] || [ -f "eleventy.config.js" ]; then OUTPUT="_site"
            elif [ -f "_config.yml" ]; then OUTPUT="_site"
            elif [ -f "hugo.toml" ] || [ -f "config.toml" ]; then OUTPUT="public"
            elif [ -f "astro.config.mjs" ]; then OUTPUT="dist"
            elif [ -f "gatsby-config.js" ]; then OUTPUT="public"
            elif [ -f "next.config.js" ]; then OUTPUT="out"
            else OUTPUT="_site"; fi
          fi
          echo "output_dir=$OUTPUT" >> $GITHUB_OUTPUT
          
          # Serve command detection
          if [ -f ".eleventy.js" ]; then SERVE="npx @11ty/eleventy-dev-server --port=4173"
          elif [ -f "hugo.toml" ]; then SERVE="hugo server --port 4173"
          elif [ -f "astro.config.mjs" ]; then SERVE="npm run preview -- --port 4173"
          else SERVE="npx http-server $OUTPUT -p 4173 --silent"; fi
          echo "serve_cmd=$SERVE" >> $GITHUB_OUTPUT
          
          # Base URL
          if [ -n "$PREVIEW_URL" ]; then
            echo "base_url=$PREVIEW_URL" >> $GITHUB_OUTPUT
          else
            echo "base_url=http://localhost:4173" >> $GITHUB_OUTPUT
          fi
          
          # Cache key for dependencies
          echo "cache_key=${{ runner.os }}-perf-a11y-${{ hashFiles('**/package-lock.json', '**/yarn.lock', '**/pnpm-lock.yaml') }}" >> $GITHUB_OUTPUT

  install-deps:
    needs: setup
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: npm
      
      - name: Cache dependencies
        uses: actions/cache@v4
        with:
          path: |
            ~/.npm
            ~/.cache/puppeteer
            ~/.cache/ms-playwright
          key: ${{ needs.setup.outputs.cache_key }}
          restore-keys: |
            ${{ runner.os }}-perf-a11y-
      
      - name: Install dependencies
        run: |
          npm i -D @lhci/cli@0.13.0 \
                   puppeteer@22.0.0 \
                   playwright@1.41.0 \
                   @axe-core/playwright@4.8.0 \
                   wait-on@7.2.0 \
                   http-server@14.1.0 \
                   fast-glob@3.3.0 \
                   xml2js@0.6.0 \
                   minimist@1.2.0
      
      - name: Install Playwright browsers
        run: |
          npx playwright install chromium || \
          npx playwright install-deps chromium || \
          echo "::warning::Playwright install failed - axe scans may not work"

  collect-urls:
    needs: [setup, install-deps]
    runs-on: ubuntu-latest
    outputs:
      url_count: ${{ steps.collect.outputs.count }}
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
      
      - name: Restore dependencies
        run: npm ci || npm install
      
      - name: Build site (if no preview URL)
        if: needs.setup.outputs.base_url == 'http://localhost:4173'
        run: |
          if [ "$BUILD_CMD" = "auto" ]; then
            # Auto-detect build command based on framework
            if [ -f "next.config.js" ]; then DEFAULT_BUILD_CMD="npm run build && npm run export"
            elif [ -f "gatsby-config.js" ]; then DEFAULT_BUILD_CMD="npm run build"
            elif [ -f "astro.config.mjs" ]; then DEFAULT_BUILD_CMD="npm run build"
            elif [ -f "hugo.toml" ] || [ -f "config.toml" ]; then DEFAULT_BUILD_CMD="hugo"
            elif [ -f ".eleventy.js" ] || [ -f "eleventy.config.js" ]; then DEFAULT_BUILD_CMD="npm run build"
            elif [ -f "_config.yml" ]; then DEFAULT_BUILD_CMD="bundle exec jekyll build"
            else DEFAULT_BUILD_CMD="npm run build"; fi
            $DEFAULT_BUILD_CMD
          else
            $BUILD_CMD
          fi
        timeout-minutes: 10
      
      - name: Find available port
        id: port
        if: needs.setup.outputs.base_url == 'http://localhost:4173'
        run: |
          echo "port=4173" >> $GITHUB_OUTPUT
      
      - name: Start server
        if: needs.setup.outputs.base_url == 'http://localhost:4173'
        run: |
          ${{ needs.setup.outputs.serve_cmd }} &
          echo $! > server.pid
          npx wait-on http://localhost:${{ steps.port.outputs.port }} --timeout 30000
      
      - name: Collect URLs
        id: collect
        run: |
          node tools/collect-urls.mjs \
            --base "${{ needs.setup.outputs.base_url }}" \
            --output-dir "${{ needs.setup.outputs.output_dir }}" \
            --source "$URL_SOURCE" \
            --max "$MAX_PAGES" > urls.txt
          
          echo "Found $(wc -l < urls.txt) URLs:"
          head -20 urls.txt
          if [ $(wc -l < urls.txt) -gt 20 ]; then
            echo "... and $(($(wc -l < urls.txt) - 20)) more"
          fi
          
          echo "count=$(wc -l < urls.txt)" >> $GITHUB_OUTPUT
      
      - name: Stop server
        if: always() && needs.setup.outputs.base_url == 'http://localhost:4173'
        run: |
          if [ -f server.pid ]; then
            kill $(cat server.pid) || true
          fi
      
      - uses: actions/upload-artifact@v4
        with:
          name: urls
          path: urls.txt
          retention-days: 14

  lighthouse:
    needs: collect-urls
    runs-on: ubuntu-latest
    continue-on-error: ${{ env.MODE == 'report-only' }}
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
      
      - name: Restore dependencies
        run: npm ci || npm install
      
      - name: Setup Chrome
        uses: browser-actions/setup-chrome@v1
      
      - name: Download URLs
        uses: actions/download-artifact@v4
        with:
          name: urls
      
      - name: Run Lighthouse
        run: |
          mkdir -p .lighthouseci
          FAILED=0
          
          while IFS= read -r url; do
            echo "Testing: $url"
            npx lhci collect \
              --config=lighthouserc.json \
              --url="$url" \
              --settings.chromeFlags="--no-sandbox" \
              --settings.preset="perf" \
              --settings.formFactor="$DEVICE" || FAILED=$((FAILED + 1))
          done < urls.txt
          
          if [ $FAILED -gt 0 ]; then
            echo "::warning::$FAILED Lighthouse runs failed"
          fi
          
          # Create summary
          node tools/summarize-lighthouse.mjs
      
      - uses: actions/upload-artifact@v4
        with:
          name: lighthouse-results
          path: |
            .lighthouseci/*.json
            lighthouse-summary.json
          retention-days: 14

  axe-scan:
    needs: collect-urls
    runs-on: ubuntu-latest
    continue-on-error: ${{ env.MODE == 'report-only' }}
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
      
      - name: Restore dependencies
        run: npm ci || npm install
      
      - name: Restore caches
        uses: actions/cache@v4
        with:
          path: |
            ~/.npm
            ~/.cache/puppeteer
            ~/.cache/ms-playwright
          key: ${{ needs.setup.outputs.cache_key }}
          restore-keys: |
            ${{ runner.os }}-perf-a11y-
      
      - name: Ensure Playwright browsers
        run: npx playwright install chromium || npx playwright install-deps chromium || echo "Playwright setup warning"
      
      - name: Download URLs
        uses: actions/download-artifact@v4
        with:
          name: urls
      
      - name: Run axe-core
        run: |
          node tools/axe-scan.mjs \
            --urls urls.txt \
            --output axe-results.json \
            --device "$DEVICE"
        timeout-minutes: 15
      
      - uses: actions/upload-artifact@v4
        with:
          name: axe-results
          path: axe-results.json
          retention-days: 14

  evaluate-gate:
    needs: [lighthouse, axe-scan]
    runs-on: ubuntu-latest
    if: always()
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
      
      - name: Download all artifacts
        uses: actions/download-artifact@v4
      
      - name: Find baseline artifact
        if: env.ENABLE_TRENDING == 'true' && github.event_name == 'pull_request'
        id: find-baseline
        continue-on-error: true
        uses: actions/github-script@v7
        with:
          script: |
            // Find most recent successful run on main
            const runs = await github.rest.actions.listWorkflowRuns({
              owner: context.repo.owner,
              repo: context.repo.repo,
              workflow_id: 'perf-a11y.yml',
              branch: 'main',
              status: 'success',
              per_page: 10
            });
            
            for (const run of runs.data.workflow_runs) {
              try {
                const artifacts = await github.rest.actions.listWorkflowRunArtifacts({
                  owner: context.repo.owner,
                  repo: context.repo.repo,
                  run_id: run.id
                });
                
                const baseline = artifacts.data.artifacts.find(a => a.name === 'baseline');
                if (baseline) {
                  core.setOutput('artifact_id', baseline.id);
                  core.setOutput('run_id', run.id);
                  console.log('Found baseline from run:', run.id);
                  return;
                }
              } catch (e) {
                console.warn('Failed to check run', run.id, e);
              }
            }
            console.log('No baseline found');
      
      - name: Download baseline
        if: steps.find-baseline.outputs.artifact_id
        continue-on-error: true
        uses: actions/download-artifact@v4
        with:
          name: baseline
          github-token: ${{ secrets.GITHUB_TOKEN }}
          run-id: ${{ steps.find-baseline.outputs.run_id }}
      
      - name: Evaluate results
        id: gate
        run: |
          node tools/evaluate-gate.mjs \
            --lighthouse lighthouse-results/lighthouse-summary.json \
            --axe axe-results/axe-results.json \
            --baseline baseline/gate-summary.json \
            --thresholds '{
              "performance": ${PERFORMANCE_THRESHOLD},
              "accessibility": ${ACCESSIBILITY_THRESHOLD},
              "lcp_ms": ${LCP_THRESHOLD},
              "tbt_ms": ${TBT_THRESHOLD},
              "cls": ${CLS_THRESHOLD},
              "axe_critical": 0,
              "axe_serious": 3
            }' \
            --mode "$MODE" \
            --output gate-summary.json
      
      - name: Save baseline (main branch only)
        if: github.event_name == 'schedule'
        uses: actions/upload-artifact@v4
        with:
          name: baseline
          path: gate-summary.json
          retention-days: 30
      
      - name: Comment on PR
        if: github.event_name == 'pull_request'
        uses: actions/github-script@v7
        with:
          script: |
            const fs = require('fs');
            const summary = JSON.parse(fs.readFileSync('gate-summary.json', 'utf8'));
            
            let comment = '## 🚦 Performance & Accessibility Report\n\n';
            
            // Performance metrics table
            comment += '### Performance Metrics\n';
            comment += '| Metric | Budget | Current | ';
            if (summary.baseline) comment += 'vs Main | ';
            comment += 'Status |\n';
            comment += '|--------|---------|----------|';
            if (summary.baseline) comment += '----------|';
            comment += '---------|\n';
            
            const metrics = [
              { key: 'performance', label: 'Perf', format: v => v, suffix: '' },
              { key: 'accessibility', label: 'Accessibility', format: v => v, suffix: '' },
              { key: 'lcp', label: 'LCP', format: v => v.toFixed(0), suffix: 'ms' },
              { key: 'tbt', label: 'TBT', format: v => v.toFixed(0), suffix: 'ms' },
              { key: 'cls', label: 'CLS', format: v => v.toFixed(3), suffix: '' }
            ];
            
            for (const metric of metrics) {
              const current = summary.metrics[metric.key];
              const threshold = summary.thresholds[metric.key + (metric.suffix === 'ms' ? '_ms' : '')];
              const baseline = summary.baseline?.metrics[metric.key];
              
              comment += `| ${metric.label} | `;
              comment += `${metric.key === 'performance' || metric.key === 'accessibility' ? '≥' : '≤'}${threshold}${metric.suffix} | `;
              comment += `${metric.format(current.avg)}${metric.suffix} | `;
              
              if (baseline) {
                const diff = current.avg - baseline.avg;
                const arrow = diff > 0 ? '↑' : diff < 0 ? '↓' : '→';
                const sign = diff > 0 ? '+' : '';
                comment += `${arrow} ${sign}${metric.format(diff)}${metric.suffix} | `;
              }
              
              const passed = metric.key === 'performance' || metric.key === 'accessibility' 
                ? current.avg >= threshold 
                : current.avg <= threshold;
              comment += `${passed ? '✅' : '❌'} |\n`;
            }
            
            // Accessibility summary
            comment += '\n### Accessibility Issues\n';
            comment += '| Severity | Count | Status |\n';
            comment += '|----------|--------|--------|\n';
            comment += `| Critical | ${summary.axe.critical} | ${summary.axe.critical === 0 ? '✅' : '❌'} |\n`;
            comment += `| Serious | ${summary.axe.serious} | ${summary.axe.serious <= 3 ? '✅' : '⚠️'} |\n`;
            comment += `| Moderate | ${summary.axe.moderate} | ℹ️ |\n`;
            comment += `| Minor | ${summary.axe.minor} | ℹ️ |\n`;
            
            // Add details if there are issues
            if (summary.details?.length > 0) {
              comment += '\n<details>\n<summary>View detailed issues</summary>\n\n';
              for (const detail of summary.details.slice(0, 10)) {
                comment += `**${detail.url}**\n`;
                if (detail.performance < summary.thresholds.performance) {
                  comment += `- 🔴 Performance: ${detail.performance}\n`;
                }
                if (detail.violations?.length > 0) {
                  comment += `- 🔴 Accessibility violations:\n`;
                  for (const v of detail.violations.slice(0, 3)) {
                    comment += `  - ${v.help} (${v.impact})\n`;
                  }
                }
                comment += '\n';
              }
              comment += '</details>\n';
            }
            
            // Status
            comment += `\n### Status: ${summary.passed ? '✅ Passed' : '❌ Failed'}\n`;
            if (summary.mode === 'report-only') {
              comment += '> 📋 Running in **report-only** mode - issues shown but not blocking\n';
            }
            
            // Find and update or create comment
            const { data: comments } = await github.rest.issues.listComments({
              ...context.repo,
              issue_number: context.issue.number
            });
            
            const botComment = comments.find(c => 
              c.user.type === 'Bot' && c.body.includes('Performance & Accessibility Report')
            );
            
            if (botComment) {
              await github.rest.issues.updateComment({
                ...context.repo,
                comment_id: botComment.id,
                body: comment
              });
            } else {
              await github.rest.issues.createComment({
                ...context.repo,
                issue_number: context.issue.number,
                body: comment
              });
            }
      
      - name: Set check status
        if: always()
        run: |
          if [ -f gate-summary.json ]; then
            PASSED=$(jq -r .passed gate-summary.json)
            MODE=$(jq -r .mode gate-summary.json)
            
            if [ "$PASSED" = "false" ] && [ "$MODE" = "enforce" ]; then
              echo "❌ Quality gate failed"
              exit 1
            elif [ "$PASSED" = "false" ] && [ "$MODE" = "report-only" ]; then
              echo "⚠️ Quality gate failed (report-only mode)"
            else
              echo "✅ Quality gate passed"
            fi
          fi
```

#### `tools/collect-urls.mjs`
```javascript
#!/usr/bin/env node
/**
 * Smart URL collector for static sites
 * - Tries sitemap.xml first
 * - Falls back to crawling output directory
 * - Supports custom URL lists
 * - Handles various static site generators
 */

import fs from 'fs';
import path from 'path';
import fg from 'fast-glob';
import { parseStringPromise } from 'xml2js';
import minimist from 'minimist';

const argv = minimist(process.argv.slice(2), {
  default: {
    base: 'http://localhost:4173',
    'output-dir': '_site',
    source: 'auto',
    list: 'urls.txt',
    max: 25
  }
});

const baseUrl = argv.base.replace(/\/$/, '');
const outputDir = argv['output-dir'];
const source = argv.source;
const listPath = argv.list;
const maxPages = parseInt(argv.max, 10);

// Normalize URL to ensure consistency
function normalizeUrl(url) {
  try {
    const u = new URL(url, baseUrl);
    return u.toString();
  } catch {
    return null;
  }
}

// Try to load sitemap
async function loadSitemap() {
  const urls = [];
  
  // Check local sitemap first
  const localSitemap = path.join(outputDir, 'sitemap.xml');
  if (fs.existsSync(localSitemap)) {
    try {
      const xml = fs.readFileSync(localSitemap, 'utf8');
      const result = await parseStringPromise(xml);
      const urlset = result.urlset?.url || [];
      
      for (const entry of urlset) {
        const loc = entry.loc?.[0];
        if (loc) urls.push(normalizeUrl(loc));
      }
      
      console.error(`Found ${urls.length} URLs in sitemap.xml`);
      return urls;
    } catch (e) {
      console.error('Failed to parse sitemap:', e.message);
    }
  }
  
  // Try remote sitemap
  if (baseUrl.startsWith('http')) {
    try {
      const fetch = globalThis.fetch;
      const response = await fetch(`${baseUrl}/sitemap.xml`);
      
      if (response.ok) {
        const xml = await response.text();
        const result = await parseStringPromise(xml);
        const urlset = result.urlset?.url || [];
        
        for (const entry of urlset) {
          const loc = entry.loc?.[0];
          if (loc) urls.push(normalizeUrl(loc));
        }
        
        console.error(`Found ${urls.length} URLs in remote sitemap`);
        return urls;
      }
    } catch (e) {
      console.error('Failed to fetch remote sitemap:', e.message);
    }
  }
  
  return urls;
}

// Crawl output directory for HTML files
async function crawlOutputDir() {
  const urls = new Set();
  
  if (!fs.existsSync(outputDir)) {
    console.error(`Output directory ${outputDir} not found`);
    return Array.from(urls);
  }
  
  // Find all HTML files
  const htmlFiles = await fg('**/*.html', {
    cwd: outputDir,
    ignore: ['**/node_modules/**', '**/.*/**']
  });
  
  // Convert file paths to URLs
  for (const file of htmlFiles) {
    // Special handling for index.html
    let urlPath = file.replace(/\\/g, '/');
    
    if (urlPath === 'index.html') {
      urls.add(normalizeUrl('/'));
    } else if (urlPath.endsWith('/index.html')) {
      // Convert folder/index.html to /folder/
      urlPath = urlPath.replace('/index.html', '/');
      urls.add(normalizeUrl(urlPath));
    } else if (urlPath.endsWith('.html')) {
      // Keep .html to avoid 404s on static hosts without rewrites
      urls.add(normalizeUrl('/' + urlPath));
    }
  }
  
  console.error(`Found ${urls.size} URLs by crawling ${outputDir}`);
  return Array.from(urls);
}

// Load custom URL list
function loadUrlList() {
  if (!fs.existsSync(listPath)) {
    console.error(`URL list ${listPath} not found`);
    return [];
  }
  
  const urls = fs.readFileSync(listPath, 'utf8')
    .split('\n')
    .map(line => line.trim())
    .filter(line => line && !line.startsWith('#'))
    .map(url => normalizeUrl(url))
    .filter(Boolean);
  
  console.error(`Found ${urls.length} URLs in ${listPath}`);
  return urls;
}

// Smart URL prioritization
function prioritizeUrls(urls) {
  const prioritized = [];
  const seen = new Set();
  
  // High priority pages - keep it simple for v1
  const highPriority = ['/', '/pricing', '/docs', '/features', '/about', '/contact'];
  
  // Add high priority pages first
  for (const pattern of highPriority) {
    for (const url of urls) {
      if (url.endsWith(pattern) || url.endsWith(pattern + '/')) {
        if (!seen.has(url)) {
          prioritized.push(url);
          seen.add(url);
        }
      }
    }
  }
  
  // Add remaining URLs
  for (const url of urls) {
    if (!seen.has(url)) {
      prioritized.push(url);
      seen.add(url);
    }
  }
  
  return prioritized;
}

// Main execution
async function main() {
  let urls = [];
  
  switch (source) {
    case 'sitemap':
      urls = await loadSitemap();
      break;
      
    case 'list':
      urls = loadUrlList();
      break;
      
    case 'crawl':
      urls = await crawlOutputDir();
      break;
      
    case 'auto':
    default:
      // Try sitemap first, then crawl
      urls = await loadSitemap();
      if (urls.length === 0) {
        urls = await crawlOutputDir();
      }
      if (urls.length === 0) {
        // Last resort: test base URL
        urls = [baseUrl];
      }
      break;
  }
  
  // Prioritize and limit URLs
  urls = prioritizeUrls(urls);
  urls = urls.slice(0, maxPages);
  
  // Output URLs (one per line)
  for (const url of urls) {
    console.log(url);
  }
  
  if (urls.length === 0) {
    console.error('Warning: No URLs found to test!');
    console.log(baseUrl); // Fallback to base URL
  }
}

main().catch(console.error);
```

#### `tools/axe-scan.mjs`
```javascript
#!/usr/bin/env node
/**
 * Accessibility scanner using axe-core via Playwright
 * - Tests pages for WCAG compliance
 * - Groups violations by severity
 * - Provides actionable remediation advice
 */

import fs from 'fs';
import minimist from 'minimist';
import { chromium } from 'playwright';
import AxeBuilder from '@axe-core/playwright';

const argv = minimist(process.argv.slice(2), {
  default: {
    urls: 'urls.txt',
    output: 'axe-results.json',
    device: 'desktop',
    timeout: 30000,
    'wait-for': 'networkidle'
  }
});

// Read URLs from file
const urls = fs.readFileSync(argv.urls, 'utf8')
  .split('\n')
  .map(line => line.trim())
  .filter(Boolean);

if (urls.length === 0) {
  console.error('No URLs provided');
  process.exit(1);
}

// Device configurations
const devices = {
  mobile: {
    viewport: { width: 375, height: 667 },
    userAgent: 'Mozilla/5.0 (iPhone; CPU iPhone OS 16_0 like Mac OS X) AppleWebKit/605.1.15'
  },
  desktop: {
    viewport: { width: 1280, height: 720 },
    userAgent: 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36'
  }
};

const results = [];
const summary = {
  totalPages: urls.length,
  totalViolations: 0,
  violationsBySeverity: {
    critical: 0,
    serious: 0,
    moderate: 0,
    minor: 0
  },
  violationsByType: {},
  commonIssues: []
};

// Run axe scan on each URL
async function scanUrl(browser, url) {
  const context = await browser.newContext(devices[argv.device]);
  const page = await context.newPage();
  
  try {
    console.error(`Scanning: ${url}`);
    
    // Navigate to page
    await page.goto(url, {
      waitUntil: argv['wait-for'],
      timeout: argv.timeout
    });
    
    // Wait a bit for dynamic content
    await page.waitForTimeout(1000);
    
    // Run axe
    const axe = new AxeBuilder({ page })
      .withTags(['wcag2a', 'wcag2aa', 'wcag21a', 'wcag21aa'])
      .disableRules(['color-contrast-enhanced']); // AA+ is too strict for most sites
    
    const result = await axe.analyze();
    
    // Process results
    const pageResult = {
      url,
      timestamp: new Date().toISOString(),
      violations: result.violations.map(v => ({
        id: v.id,
        impact: v.impact,
        help: v.help,
        helpUrl: v.helpUrl,
        elements: v.nodes.length,
        firstElement: v.nodes[0]?.html
      })),
      violationCount: result.violations.length,
      passes: result.passes.length,
      incomplete: result.incomplete.length
    };
    
    // Update summary
    summary.totalViolations += pageResult.violationCount;
    for (const violation of result.violations) {
      summary.violationsBySeverity[violation.impact]++;
      summary.violationsByType[violation.id] = 
        (summary.violationsByType[violation.id] || 0) + 1;
    }
    
    return pageResult;
    
  } catch (error) {
    console.error(`Error scanning ${url}:`, error.message);
    return {
      url,
      error: error.message,
      violations: [],
      violationCount: 0
    };
  } finally {
    await context.close();
  }
}

// Main execution
(async () => {
  const browser = await chromium.launch({
    headless: true,
    args: ['--no-sandbox', '--disable-setuid-sandbox']
  });
  
  try {
    // Process URLs in batches (adaptive sizing)
    const batchSize = Math.min(3, Math.ceil(urls.length / 5));
    for (let i = 0; i < urls.length; i += batchSize) {
      const batch = urls.slice(i, i + batchSize);
      const batchResults = await Promise.all(
        batch.map(url => scanUrl(browser, url))
      );
      results.push(...batchResults);
    }
    
    // Find most common issues
    const sortedIssues = Object.entries(summary.violationsByType)
      .sort(([, a], [, b]) => b - a)
      .slice(0, 5);
    
    summary.commonIssues = sortedIssues.map(([id, count]) => ({
      id,
      count,
      percentage: Math.round((count / summary.totalPages) * 100)
    }));
    
    // Write results
    const output = {
      summary,
      results,
      metadata: {
        scanDate: new Date().toISOString(),
        device: argv.device,
        axeVersion: '4.8.0'
      }
    };
    
    fs.writeFileSync(argv.output, JSON.stringify(output, null, 2));
    console.error(`\nScan complete. Results written to ${argv.output}`);
    console.error(`Total violations: ${summary.totalViolations}`);
    console.error(`Critical: ${summary.violationsBySeverity.critical}`);
    console.error(`Serious: ${summary.violationsBySeverity.serious}`);
    
  } finally {
    await browser.close();
  }
})();
```

#### `tools/summarize-lighthouse.mjs`
```javascript
#!/usr/bin/env node
/**
 * Summarizes Lighthouse results for easier processing
 */

import fs from 'fs';
import path from 'path';
import fg from 'fast-glob';

// Find all Lighthouse result files
const lhrFiles = fg.sync('.lighthouseci/lhr-*.json');

if (lhrFiles.length === 0) {
  console.error('No Lighthouse results found');
  process.exit(1);
}

const results = [];
const metrics = {
  performance: [],
  accessibility: [],
  'best-practices': [],
  seo: [],
  lcp: [],
  tbt: [],
  cls: [],
  fcp: [],
  si: [],
  tti: []
};

// Process each result
for (const file of lhrFiles) {
  try {
    const lhr = JSON.parse(fs.readFileSync(file, 'utf8'));
    
    const result = {
      url: lhr.finalUrl || lhr.requestedUrl,
      fetchTime: lhr.fetchTime,
      scores: {
        performance: Math.round((lhr.categories.performance?.score || 0) * 100),
        accessibility: Math.round((lhr.categories.accessibility?.score || 0) * 100),
        'best-practices': Math.round((lhr.categories['best-practices']?.score || 0) * 100),
        seo: Math.round((lhr.categories.seo?.score || 0) * 100)
      },
      metrics: {
        lcp: lhr.audits['largest-contentful-paint']?.numericValue || 0,
        tbt: lhr.audits['total-blocking-time']?.numericValue || 0,
        cls: lhr.audits['cumulative-layout-shift']?.numericValue || 0,
        fcp: lhr.audits['first-contentful-paint']?.numericValue || 0,
        si: lhr.audits['speed-index']?.numericValue || 0,
        tti: lhr.audits['interactive']?.numericValue || 0
      }
    };
    
    results.push(result);
    
    // Collect for averaging
    Object.keys(result.scores).forEach(key => {
      metrics[key].push(result.scores[key]);
    });
    Object.keys(result.metrics).forEach(key => {
      metrics[key].push(result.metrics[key]);
    });
    
  } catch (e) {
    console.error(`Failed to process ${file}:`, e.message);
  }
}

// Calculate summary statistics
const summary = {
  pageCount: results.length,
  scores: {},
  metrics: {}
};

// Calculate averages and percentiles
function calculateStats(values) {
  if (values.length === 0) return { avg: 0, min: 0, max: 0, p50: 0, p90: 0 };
  
  const sorted = [...values].sort((a, b) => a - b);
  const avg = values.reduce((sum, v) => sum + v, 0) / values.length;
  const p50 = sorted[Math.floor(sorted.length * 0.5)];
  const p90 = sorted[Math.floor(sorted.length * 0.9)];
  
  return {
    avg: Math.round(avg),
    min: Math.min(...values),
    max: Math.max(...values),
    p50: Math.round(p50),
    p90: Math.round(p90)
  };
}

// Process scores and metrics
Object.keys(metrics).forEach(key => {
  if (metrics[key].length > 0) {
    if (['performance', 'accessibility', 'best-practices', 'seo'].includes(key)) {
      summary.scores[key] = calculateStats(metrics[key]);
    } else {
      summary.metrics[key] = calculateStats(metrics[key]);
    }
  }
});

// Write summary
const output = {
  summary,
  results,
  timestamp: new Date().toISOString()
};

fs.writeFileSync('lighthouse-summary.json', JSON.stringify(output, null, 2));
console.error('Lighthouse summary created');
```

#### `tools/evaluate-gate.mjs`
```javascript
#!/usr/bin/env node
/**
 * Evaluates results against thresholds and generates gate summary
 */

import fs from 'fs';
import minimist from 'minimist';

const argv = minimist(process.argv.slice(2));

// Load results
const lighthouseSummary = JSON.parse(fs.readFileSync(argv.lighthouse, 'utf8'));
const axeResults = JSON.parse(fs.readFileSync(argv.axe, 'utf8'));
const thresholds = JSON.parse(argv.thresholds);
const mode = argv.mode || 'report-only';

// Load baseline if available
let baseline = null;
if (argv.baseline && fs.existsSync(argv.baseline)) {
  try {
    baseline = JSON.parse(fs.readFileSync(argv.baseline, 'utf8'));
  } catch (e) {
    console.error('Failed to load baseline:', e.message);
  }
}

// Evaluate Lighthouse results
const metrics = {
  performance: lighthouseSummary.summary.scores.performance || { avg: 0 },
  accessibility: lighthouseSummary.summary.scores.accessibility || { avg: 0 },
  lcp: lighthouseSummary.summary.metrics.lcp || { avg: 0 },
  tbt: lighthouseSummary.summary.metrics.tbt || { avg: 0 },
  cls: lighthouseSummary.summary.metrics.cls || { avg: 0 }
};

// Check thresholds
const failures = [];

if (metrics.performance.avg < thresholds.performance) {
  failures.push(`Performance score ${metrics.performance.avg} < ${thresholds.performance}`);
}

if (metrics.accessibility.avg < thresholds.accessibility) {
  failures.push(`Accessibility score ${metrics.accessibility.avg} < ${thresholds.accessibility}`);
}

if (metrics.lcp.avg > thresholds.lcp_ms) {
  failures.push(`LCP ${metrics.lcp.avg}ms > ${thresholds.lcp_ms}ms`);
}

if (metrics.tbt.avg > thresholds.tbt_ms) {
  failures.push(`TBT ${metrics.tbt.avg}ms > ${thresholds.tbt_ms}ms`);
}

if (metrics.cls.avg > thresholds.cls) {
  failures.push(`CLS ${metrics.cls.avg} > ${thresholds.cls}`);
}

// Evaluate axe results
const axeSummary = axeResults.summary.violationsBySeverity;

if (axeSummary.critical > thresholds.axe_critical) {
  failures.push(`Critical a11y violations ${axeSummary.critical} > ${thresholds.axe_critical}`);
}

if (axeSummary.serious > thresholds.axe_serious) {
  failures.push(`Serious a11y violations ${axeSummary.serious} > ${thresholds.axe_serious}`);
}

// Calculate trends if baseline available
let trends = null;
if (baseline) {
  trends = {
    performance: metrics.performance.avg - (baseline.metrics?.performance?.avg || 0),
    lcp: metrics.lcp.avg - (baseline.metrics?.lcp?.avg || 0),
    tbt: metrics.tbt.avg - (baseline.metrics?.tbt?.avg || 0),
    cls: metrics.cls.avg - (baseline.metrics?.cls?.avg || 0)
  };
}

// Generate gate summary
const summary = {
  timestamp: new Date().toISOString(),
  mode,
  passed: failures.length === 0,
  failures,
  metrics,
  thresholds,
  baseline: baseline ? {
    metrics: baseline.metrics,
    timestamp: baseline.timestamp
  } : null,
  trends,
  axe: {
    critical: axeSummary.critical,
    serious: axeSummary.serious,
    moderate: axeSummary.moderate,
    minor: axeSummary.minor,
    total: axeResults.summary.totalViolations
  },
  details: []
};

// Add page-level details for failed pages
const failedPages = lighthouseSummary.results
  .filter(r => r.scores.performance < thresholds.performance || 
               r.scores.accessibility < thresholds.accessibility)
  .slice(0, 10);

for (const page of failedPages) {
  const axePage = axeResults.results.find(r => r.url === page.url);
  summary.details.push({
    url: page.url,
    performance: page.scores.performance,
    accessibility: page.scores.accessibility,
    lcp: page.metrics.lcp,
    violations: axePage?.violations || []
  });
}

// Output summary
if (argv.output) {
  fs.writeFileSync(argv.output, JSON.stringify(summary, null, 2));
}

// Console output
console.log('\n📊 Performance & Accessibility Gate Results');
console.log('==========================================');
console.log(`Mode: ${mode}`);
console.log(`Pages tested: ${lighthouseSummary.summary.pageCount}`);
console.log('\nMetrics:');
console.log(`  Performance: ${metrics.performance.avg} (threshold: ≥${thresholds.performance})`);
console.log(`  Accessibility: ${metrics.accessibility.avg} (threshold: ≥${thresholds.accessibility})`);
console.log(`  LCP: ${metrics.lcp.avg}ms (threshold: ≤${thresholds.lcp_ms}ms)`);
console.log(`  TBT: ${metrics.tbt.avg}ms (threshold: ≤${thresholds.tbt_ms}ms)`);
console.log(`  CLS: ${metrics.cls.avg.toFixed(3)} (threshold: ≤${thresholds.cls})`);

if (trends) {
  console.log('\nTrends vs baseline:');
  Object.entries(trends).forEach(([key, value]) => {
    const arrow = value > 0 ? '↑' : value < 0 ? '↓' : '→';
    const sign = value > 0 ? '+' : '';
    const unit = ['lcp', 'tbt'].includes(key) ? 'ms' : '';
    console.log(`  ${key}: ${arrow} ${sign}${value.toFixed(0)}${unit}`);
  });
}

console.log('\nAccessibility:');
console.log(`  Critical violations: ${axeSummary.critical}`);
console.log(`  Serious violations: ${axeSummary.serious}`);
console.log(`  Total violations: ${axeResults.summary.totalViolations}`);

if (failures.length > 0) {
  console.log('\n❌ Gate FAILED:');
  failures.forEach(f => console.log(`  - ${f}`));
  
  if (mode === 'enforce') {
    process.exit(1);
  } else {
    console.log('\n📋 Running in report-only mode - not failing build');
  }
} else {
  console.log('\n✅ Gate PASSED - all thresholds met');
}
```

### Step 3: Create Supporting Files

#### `lighthouserc.json`
```json
{
  "ci": {
    "collect": {
      "numberOfRuns": 3,
      "settings": {
        "onlyCategories": ["performance", "accessibility", "best-practices", "seo"],
        "throttlingMethod": "simulate",
        "throttling": {
          "cpuSlowdownMultiplier": 4
        }
      }
    }
  }
}
```

#### `urls.txt` (example)
```
# Example URL list for testing specific pages
# One URL per line, comments start with #
/
/about
/features
/pricing
/docs
/blog
```

#### `docs/perf-a11y-guardrails.md`
```markdown
# Performance & Accessibility Guardrails

This repository uses automated testing to ensure performance and accessibility standards are maintained.

## Overview

Every pull request runs:

1. **🚦 Lighthouse** - Performance scores and Core Web Vitals
2. **♿ axe-core** - WCAG 2.1 AA compliance checking
3. **📈 Trend Analysis** - Comparison against main branch baseline

## Current Configuration

### Mode
- **${MODE}** - Issues are ${MODE === 'enforce' ? 'blocking' : 'reported but not blocking'}

### Performance Thresholds
- Performance Score: ≥${PERFORMANCE_THRESHOLD}
- Accessibility Score: ≥${ACCESSIBILITY_THRESHOLD}
- Largest Contentful Paint (LCP): ≤${LCP_THRESHOLD}ms
- Total Blocking Time (TBT): ≤${TBT_THRESHOLD}ms
- Cumulative Layout Shift (CLS): ≤${CLS_THRESHOLD}

### Accessibility Thresholds
- Critical violations: 0 allowed
- Serious violations: ≤3 allowed

## Running Tests Locally

```bash
# Install dependencies
npm install

# Run full test suite
npm run perf:test

# Test specific URLs
echo "https://localhost:3000/slow-page" > test-urls.txt
npm run perf:test -- --urls test-urls.txt

# Test with different device
npm run perf:test -- --device desktop
```

## Understanding Results

### Performance Metrics

- **Performance Score**: Overall Lighthouse performance score (0-100)
- **LCP**: Largest Contentful Paint - loading performance
- **TBT**: Total Blocking Time - interactivity
- **CLS**: Cumulative Layout Shift - visual stability
- **FCP**: First Contentful Paint - perceived load speed

### Accessibility Violations

- **Critical**: Must fix - seriously impacts users
- **Serious**: Should fix - significant barriers
- **Moderate**: Good to fix - some impact
- **Minor**: Nice to fix - minimal impact

## Fixing Common Issues

### Performance

**Slow LCP**
- Optimize images (use WebP, proper sizing)
- Improve server response time
- Use CDN for static assets

**High TBT**
- Split large JavaScript bundles
- Defer non-critical scripts
- Reduce main thread work

**Layout Shifts (CLS)**
- Set dimensions on images/videos
- Avoid inserting content above existing content
- Use CSS transforms instead of position changes

### Accessibility

**Missing alt text**
```html
<!-- Bad -->
<img src="hero.jpg">

<!-- Good -->
<img src="hero.jpg" alt="Team collaborating on project">
```

**Low contrast**
```css
/* Ensure 4.5:1 ratio for normal text */
color: #767676; /* On white: 4.54:1 ✓ */
```

**Missing labels**
```html
<!-- Bad -->
<input type="email" placeholder="Email">

<!-- Good -->
<label for="email">Email</label>
<input id="email" type="email">
```

## Adjusting Thresholds

Edit `.github/workflows/perf-a11y.yml`:

```yaml
env:
  # Make stricter
  PERFORMANCE_THRESHOLD: 95
  LCP_THRESHOLD: 2000
  
  # Make more lenient
  TBT_THRESHOLD: 500
```

## Switching to Enforce Mode

When ready to enforce standards:

1. Review recent trend data
2. Ensure team is trained on fixes
3. Update workflow:
   ```yaml
   env:
     MODE: enforce
   ```

## FAQ

**Why did my PR fail?**
Check the PR comment for specific metrics that failed. Focus on fixing the "❌" items first.

**Can I skip these checks?**
No, but you can run in report-only mode during initial adoption.

**How do I test before pushing?**
Run `npm run perf:test` locally to catch issues early.

**Why are mobile scores lower?**
Mobile testing simulates slower devices and networks. This reflects real user experience.
```

#### Update README.md
```markdown
<!-- PERF-A11Y-GUARDRAILS:START -->
## 🚦 Performance & Accessibility Standards

[![Performance & Accessibility](https://github.com/${GITHUB_REPO}/actions/workflows/perf-a11y.yml/badge.svg)](https://github.com/${GITHUB_REPO}/actions/workflows/perf-a11y.yml)

This repository enforces performance and accessibility standards:
- **Core Web Vitals** monitoring (LCP, TBT, CLS)
- **WCAG 2.1 AA** compliance checking
- **Trend analysis** to prevent regressions

Currently in **${MODE}** mode. See [performance documentation](docs/perf-a11y-guardrails.md) for details.
<!-- PERF-A11Y-GUARDRAILS:END -->
```

### Step 4: Package.json Scripts

```javascript
// Add these scripts
const scripts = {
  "perf:install": "npm i -D @lhci/cli@0.13.0 puppeteer@19.7.0 playwright@1.41.0 @axe-core/playwright@4.8.0 wait-on@7.2.0 http-server@14.1.0 fast-glob@3.3.0 xml2js@0.6.0 minimist@1.2.0",
  "perf:serve": "http-server ${OUTPUT_DIR} -p 4173",
  "perf:collect-urls": "node tools/collect-urls.mjs --base http://localhost:4173 --output-dir ${OUTPUT_DIR}",
  "perf:lighthouse": "lhci collect --config=lighthouserc.json",
  "perf:summarize": "node tools/summarize-lighthouse.mjs",
  "perf:axe": "node tools/axe-scan.mjs",
  "perf:test": "npm run build && npm run perf:serve & wait-on http://localhost:4173 && npm run perf:collect-urls > urls.txt && npm run perf:lighthouse && npm run perf:summarize && npm run perf:axe",
  "perf:evaluate": "node tools/evaluate-gate.mjs --lighthouse lighthouse-summary.json --axe axe-results.json --thresholds '{\"performance\":90,\"accessibility\":95,\"lcp_ms\":2500,\"tbt_ms\":300,\"cls\":0.1,\"axe_critical\":0,\"axe_serious\":3}'"
};
```

## Troubleshooting Common Issues

### CORS Issues
For local testing with separate frontend/backend ports:
1. Add test port (4173) to your backend CORS configuration
2. Ensure both `http://localhost:4173` and `http://127.0.0.1:4173` are allowed

### Missing Accessibility Scores
Lighthouse sometimes reports 0 for accessibility. This is a known issue. The axe-core results are more reliable for accessibility testing.

### Playwright Installation
If Playwright fails to install browsers:
```bash
npx playwright install-deps chromium
sudo npx playwright install chromium
```

### Performance Issues
Main causes of poor performance scores:
1. **Long LCP (4+ seconds)**: Usually waiting for JavaScript/API calls before rendering
   - Solution: Server-side rendering, loading states, or progressive enhancement
2. **High TBT**: Too much JavaScript execution blocking interactivity
   - Solution: Code splitting, defer non-critical scripts
3. **Layout shifts**: Content jumping around as it loads
   - Solution: Reserve space for dynamic content, set image dimensions

### Step 5: Implementation Flow

1. **Create feature branch**
   ```bash
   git checkout -b feat/vitals-guardrails-$(date +%Y%m%d-%H%M)
   ```

2. **Install dependencies**
   ```bash
   ${PM} install -D @lhci/cli@0.13.0 puppeteer@19.7.0 playwright@1.41.0 @axe-core/playwright@4.8.0 wait-on@7.2.0 http-server@14.1.0 fast-glob@3.3.0 xml2js@0.6.0 minimist@1.2.0
   npx playwright install chromium
   ```

3. **Create all files with proper substitutions**

4. **Make tools executable**
   ```bash
   chmod +x tools/*.mjs
   ```

5. **Run local validation**
   ```bash
   ${PM} run perf:test || echo "First run expected to find issues"
   ```

6. **Commit in logical chunks**
   ```bash
   git add lighthouserc.json tools/
   git commit -m "chore: add vitals testing tools"
   
   git add .github/workflows/perf-a11y.yml
   git commit -m "ci: add performance & accessibility workflow"
   
   git add docs/vitals-guardrails.md README.md package.json
   git commit -m "docs: add vitals guardrails documentation"
   ```

7. **Push and create PR**
   ```bash
   git push -u origin HEAD
   gh pr create --title "feat: add vitals guardrails for performance & compliance" \
                --body "$PR_BODY"
   ```

## Variable Substitutions

Replace these placeholders:
- `${MODE}` → input mode
- `${PREVIEW_URL}` → input preview_url
- `${BUILD_COMMAND}` → detected or input build command
- `${OUTPUT_DIR}` → detected or input output directory
- `${URL_SOURCE}` → input url_source
- `${MAX_PAGES}` → input max_pages
- `${DEVICE}` → input device
- `${PERFORMANCE_THRESHOLD}` → input thresholds
- `${ENABLE_TRENDING}` → input enable_trending
- `${DEFAULT_BUILD_CMD}` → framework-specific build
- `${GITHUB_REPO}` → detected from git remote
```