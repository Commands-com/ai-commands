---
name: Eleventy Asset Pipeline
description: Scaffold and maintain a production-grade asset pipeline for Eleventy sites. Validates project structure, sets up JavaScript bundling with esbuild, CSS processing with PostCSS/Tailwind, and implements cache-busting with content hashing. Creates a complete build system with hot reloading.
allowed_tools:
  - filesystem      # Create pipeline files and configurations
tags:
  - eleventy
  - build-tools
  - asset-pipeline
  - performance
category: development
---

## Arguments

```
/eleventy-asset-pipeline
[bundle_mode=<true|false>]
[hash_length=<8|10|12|16>]
[source_maps=<true|false>]
[css_framework=<tailwind|none>]
```
*Defaults → `bundle_mode=false  hash_length=10  source_maps=true  css_framework=tailwind`*

### Examples

```bash
# Basic setup with defaults
/eleventy-asset-pipeline

# Production-optimized setup
/eleventy-asset-pipeline bundle_mode=true source_maps=false

# Custom hash length without Tailwind
/eleventy-asset-pipeline hash_length=16 css_framework=none
```

---

## Context – what the AI should do

1. **Validate Project Structure**
   * Check if current directory is an Eleventy project (look for .eleventy.js or eleventy.config.js)
   * Verify package.json exists
   * Check for src directory structure or common Eleventy patterns
   * Analyze existing assets to determine appropriate pipeline configuration
   * Warn if incompatible setup detected

2. **Parse Arguments**
   * `bundle_mode` - Whether to bundle JS entry points (production) or keep separate (dev)
   * `hash_length` - Length of content hash for cache busting
   * `source_maps` - Whether to generate source maps in production
   * `css_framework` - CSS framework to configure (currently Tailwind or none)

3. **Dependency Management**
   * Check which dependencies are already installed
   * Generate npm install command for missing dependencies only
   * Required: @11ty/eleventy esbuild postcss autoprefixer cssnano fast-glob dotenv npm-run-all
   * Optional: tailwindcss (if css_framework=tailwind)

4. **Project Analysis**
   * Detect existing Eleventy configuration
   * Identify current asset structure (src/assets, _includes, etc.)
   * Check for existing build scripts to avoid conflicts
   * Determine if project uses CommonJS or ES modules

5. **Pipeline Implementation**
   * All file creation follows the snippets below
   * Adapt paths based on detected project structure
   * Preserve existing files with .backup extension if conflicts
   * Use the complete snippets to ensure one-shot success

---

## Implementation

> **Note**: The following implementation uses the exact snippets that enable one-shot pipeline creation. The AI should use these verbatim after validating the project structure.

### Step 1: Update package.json scripts
```javascript
// >>> ELEVENTY_ASSET_PIPELINE package.json BEGIN
const scripts = {
  "dev": "npm run clean:bundles && NODE_ENV=development npm-run-all --parallel dev:*",
  "dev:eleventy": "eleventy --serve --quiet",
  "dev:css": "mkdir -p _site/assets/styles && postcss src/assets/styles/main.css -o _site/assets/styles/main.css --watch",
  "build": "npm run clean && NODE_ENV=production npm run build:css && eleventy",
  "build:bundles": "npm run clean && BUNDLE_MODE=true node scripts/build-assets.js && NODE_ENV=production npm run build:css && eleventy",
  "build:css": "mkdir -p _site/assets/styles && postcss src/assets/styles/main.css -o _site/assets/styles/main.css",
  "build:assets": "node scripts/build-assets.js",
  "clean": "rm -rf _site",
  "clean:bundles": "node -e \"const fs=require('fs'); ['_site/assets/*-*.js', '_site/assets/*-*.js.map', '_site/assets/chunks/'].forEach(p => { try { const glob = require('fast-glob'); const files = glob.sync(p); files.forEach(f => fs.rmSync(f, {force:true})); } catch {} })\""
};

// Also add to devDependencies if not present:
const devDependencies = {
  "npm-run-all": "^4.1.5"
};
// <<< ELEVENTY_ASSET_PIPELINE package.json END
```

### Step 2: Create directory structure
```bash
mkdir -p build scripts src/assets/{js,styles,bundles} src/assets/js/{admin,vendor} src/_includes/layouts src/_data

# Make build scripts executable
chmod +x build/*.mjs
```

### Step 3: Create PostCSS configuration
Create `postcss.config.js`:
```javascript
// >>> ELEVENTY_ASSET_PIPELINE postcss.config BEGIN
module.exports = {
  plugins: [
    require('tailwindcss'),
    require('autoprefixer'),
    ...(process.env.NODE_ENV === 'production' ? [require('cssnano')] : [])
  ]
}
// <<< ELEVENTY_ASSET_PIPELINE postcss.config END
```

### Step 4: Create Tailwind configuration
Create `tailwind.config.js`:
```javascript
// >>> ELEVENTY_ASSET_PIPELINE tailwind.config BEGIN
module.exports = {
  content: [
    './src/**/*.{html,njk,md,js}',
    './_site/**/*.html'
  ],
  theme: {
    extend: {},
  },
  plugins: [],
}
// <<< ELEVENTY_ASSET_PIPELINE tailwind.config END
```

### Step 5: Create main CSS file
Create `src/assets/styles/main.css`:
```css
/* >>> ELEVENTY_ASSET_PIPELINE main.css BEGIN */
@tailwind base;
@tailwind components;
@tailwind utilities;

/* Your custom styles here */
/* <<< ELEVENTY_ASSET_PIPELINE main.css END */
```

### Step 6: Generate core asset bundler
Create `scripts/build-assets.js`:
```javascript
// >>> ELEVENTY_ASSET_PIPELINE build-assets BEGIN
const esbuild = require('esbuild');
const fs = require('fs');
const path = require('path');

const bundleMode = process.env.BUNDLE_MODE === 'true';

// Define your bundles - customize based on your app structure
const bundles = bundleMode ? {
  // Production bundles: single entry points that import multiple files
  'main': ['src/assets/bundles/main-bundle.js'],
  'admin': ['src/assets/bundles/admin-bundle.js'],
  'vendor': ['src/assets/bundles/vendor-bundle.js']
} : {
  // Development mode: individual files for faster rebuilds
  'main': [
    'src/assets/js/app.js',
    'src/assets/js/navigation.js',
    'src/assets/js/utils.js'
  ],
  'admin': [
    'src/assets/js/admin/dashboard.js',
    'src/assets/js/admin/users.js'
  ],
  'vendor': [
    'src/assets/js/vendor/analytics.js'
  ]
};

const outdir = '_site/assets';
if (!fs.existsSync(outdir)) {
  fs.mkdirSync(outdir, { recursive: true });
}

function cleanupOldBundles() {
  console.log('🧹 Cleaning up old bundle files...');
  const bundleNames = Object.keys(bundles).join('|');
  const pattern = new RegExp(`^(${bundleNames})-[A-Z0-9]+\\.(js|js\\.map)$`);
  
  if (fs.existsSync(outdir)) {
    const files = fs.readdirSync(outdir).filter(file => file.match(pattern));
    files.forEach(file => fs.rmSync(path.join(outdir, file), { force: true }));
  }
  
  const chunksDir = path.join(outdir, 'chunks');
  if (fs.existsSync(chunksDir)) {
    fs.rmSync(chunksDir, { recursive: true, force: true });
  }
}

async function buildAssets() {
  try {
    console.log(`🔧 Building in ${bundleMode ? 'BUNDLE' : 'INDIVIDUAL'} mode...`);
    cleanupOldBundles();

    // Build all bundles in parallel for better performance
    const buildPromises = Object.entries(bundles).map(async ([bundleName, files]) => {
      const existingFiles = files.filter(file => fs.existsSync(file));
      if (existingFiles.length === 0) {
        console.warn(`⚠️  No files found for bundle: ${bundleName}`);
        return null;
      }

      console.log(`📦 Building ${bundleName} bundle...`);
      return esbuild.build({
        entryPoints: existingFiles,
        bundle: true,
        minify: process.env.NODE_ENV === 'production',
        sourcemap: process.env.NODE_ENV === 'production' 
          ? (process.env.SOURCE_MAPS !== 'false' ? 'external' : false)
          : true,
        outdir,
        format: 'esm',
        splitting: true,
        target: ['es2020'],
        entryNames: `${bundleName}-[hash]`,
        chunkNames: 'chunks/[name]-[hash]',
        metafile: true,
        write: true,
        external: ['*.png', '*.jpg', '*.svg', '*.woff', '*.woff2'] // Don't bundle assets
      });
    });

    await Promise.all(buildPromises);

    // Generate bundle manifest for Eleventy
    const bundleManifest = {};
    for (const bundleName of Object.keys(bundles)) {
      const bundleFile = fs.readdirSync(outdir).find(file => 
        file.startsWith(`${bundleName}-`) && file.endsWith('.js')
      );
      if (bundleFile) {
        bundleManifest[bundleName] = `/assets/${bundleFile}`;
      }
    }
    
    // Ensure _data directory exists
    const dataDir = 'src/_data';
    if (!fs.existsSync(dataDir)) {
      fs.mkdirSync(dataDir, { recursive: true });
    }
    
    fs.writeFileSync(
      path.join(dataDir, 'bundles.json'), 
      JSON.stringify(bundleManifest, null, 2)
    );
    
    console.log('✅ Assets built successfully!');
    console.log('📋 Bundle manifest:', bundleManifest);

  } catch (error) {
    console.error('❌ Build failed:', error);
    process.exit(1);
  }
}

// Run the build
buildAssets();
// <<< ELEVENTY_ASSET_PIPELINE build-assets END
```

### Step 7: Create bundle entry points
Create `src/assets/bundles/main-bundle.js`:
```javascript
// >>> ELEVENTY_ASSET_PIPELINE main-bundle BEGIN
// Main bundle entry point - imports all core functionality
export * from '../js/app.js';
export * from '../js/navigation.js';
export * from '../js/utils.js';

// Mark as bundled to prevent double initialization
window.__bundled = true;

// Initialize on DOM ready
if (document.readyState === 'loading') {
  document.addEventListener('DOMContentLoaded', init);
} else {
  init();
}

function init() {
  console.log('Main bundle initialized');
  // Initialize imported modules
  import('../js/app.js').then(m => m.initApp && m.initApp());
  import('../js/navigation.js').then(m => m.initNavigation && m.initNavigation());
}
// <<< ELEVENTY_ASSET_PIPELINE main-bundle END
```

Create `src/assets/bundles/admin-bundle.js`:
```javascript
// >>> ELEVENTY_ASSET_PIPELINE admin-bundle BEGIN
// Admin bundle entry point
export * from '../js/admin/dashboard.js';
export * from '../js/admin/users.js';

// Mark as bundled
window.__bundled = true;

console.log('Admin bundle loaded');
// <<< ELEVENTY_ASSET_PIPELINE admin-bundle END
```

Create `src/assets/bundles/vendor-bundle.js`:
```javascript
// >>> ELEVENTY_ASSET_PIPELINE vendor-bundle BEGIN
// Third-party scripts bundle
export * from '../js/vendor/analytics.js';
// <<< ELEVENTY_ASSET_PIPELINE vendor-bundle END
```

### Step 8: Create example JavaScript files
Create `src/assets/js/app.js`:
```javascript
// >>> ELEVENTY_ASSET_PIPELINE app.js BEGIN
export function initApp() {
  console.log('App initialized');
  
  // Example: Dark mode toggle
  const toggle = document.querySelector('[data-dark-mode-toggle]');
  if (toggle) {
    toggle.addEventListener('click', () => {
      document.documentElement.classList.toggle('dark');
      localStorage.setItem('darkMode', 
        document.documentElement.classList.contains('dark')
      );
    });
  }
}

// Auto-init if not bundled
if (typeof window !== 'undefined' && !window.__bundled) {
  initApp();
}
// <<< ELEVENTY_ASSET_PIPELINE app.js END
```

Create `src/assets/js/navigation.js`:
```javascript
// >>> ELEVENTY_ASSET_PIPELINE navigation.js BEGIN
export function initNavigation() {
  console.log('Navigation initialized');
  
  // Mobile menu toggle
  const menuToggle = document.querySelector('[data-menu-toggle]');
  const menu = document.querySelector('[data-menu]');
  
  if (menuToggle && menu) {
    menuToggle.addEventListener('click', () => {
      menu.classList.toggle('hidden');
      menuToggle.setAttribute('aria-expanded', 
        menu.classList.contains('hidden') ? 'false' : 'true'
      );
    });
  }
  
  // Active link highlighting
  const currentPath = window.location.pathname;
  document.querySelectorAll('nav a').forEach(link => {
    if (link.getAttribute('href') === currentPath) {
      link.classList.add('active');
    }
  });
}

// Auto-init if not bundled
if (typeof window !== 'undefined' && !window.__bundled) {
  initNavigation();
}
// <<< ELEVENTY_ASSET_PIPELINE navigation.js END
```

Create `src/assets/js/utils.js`:
```javascript
// >>> ELEVENTY_ASSET_PIPELINE utils.js BEGIN
// Utility functions
export function debounce(func, wait) {
  let timeout;
  return function executedFunction(...args) {
    const later = () => {
      clearTimeout(timeout);
      func(...args);
    };
    clearTimeout(timeout);
    timeout = setTimeout(later, wait);
  };
}

export function formatDate(date, locale = 'en-US') {
  return new Intl.DateTimeFormat(locale, {
    year: 'numeric',
    month: 'long',
    day: 'numeric'
  }).format(new Date(date));
}

export function slugify(text) {
  return text
    .toString()
    .toLowerCase()
    .trim()
    .replace(/[^\w\s-]/g, '')
    .replace(/[\s_-]+/g, '-')
    .replace(/^-+|-+$/g, '');
}

// DOM ready helper
export function ready(fn) {
  if (document.readyState !== 'loading') {
    fn();
  } else {
    document.addEventListener('DOMContentLoaded', fn);
  }
}
// <<< ELEVENTY_ASSET_PIPELINE utils.js END
```

Create `src/assets/js/admin/dashboard.js`:
```javascript
// >>> ELEVENTY_ASSET_PIPELINE dashboard.js BEGIN
export function initDashboard() {
  console.log('Admin dashboard initialized');
  
  // Example: Stats counter animation
  const stats = document.querySelectorAll('[data-stat-value]');
  stats.forEach(stat => {
    const target = parseInt(stat.dataset.statValue);
    let current = 0;
    const increment = target / 100;
    
    const timer = setInterval(() => {
      current += increment;
      if (current >= target) {
        current = target;
        clearInterval(timer);
      }
      stat.textContent = Math.floor(current).toLocaleString();
    }, 10);
  });
}

// Initialize if on dashboard page
if (document.querySelector('[data-admin-dashboard]')) {
  initDashboard();
}
// <<< ELEVENTY_ASSET_PIPELINE dashboard.js END
```

Create `src/assets/js/admin/users.js`:
```javascript
// >>> ELEVENTY_ASSET_PIPELINE users.js BEGIN
export function initUserManagement() {
  console.log('User management initialized');
  
  // Example: User search
  const searchInput = document.querySelector('[data-user-search]');
  const userRows = document.querySelectorAll('[data-user-row]');
  
  if (searchInput && userRows.length) {
    searchInput.addEventListener('input', (e) => {
      const search = e.target.value.toLowerCase();
      
      userRows.forEach(row => {
        const text = row.textContent.toLowerCase();
        row.style.display = text.includes(search) ? '' : 'none';
      });
    });
  }
}

// Initialize if on users page
if (document.querySelector('[data-admin-users]')) {
  initUserManagement();
}
// <<< ELEVENTY_ASSET_PIPELINE users.js END
```

Create `src/assets/js/vendor/analytics.js`:
```javascript
// >>> ELEVENTY_ASSET_PIPELINE analytics.js BEGIN
// Third-party analytics wrapper
export function initAnalytics(config = {}) {
  // Only initialize in production (check if localhost)
  if (window.location.hostname === 'localhost' || window.location.hostname === '127.0.0.1') {
    console.log('Analytics disabled on localhost');
    return;
  }
  
  // Example analytics initialization
  window.dataLayer = window.dataLayer || [];
  
  function gtag() {
    window.dataLayer.push(arguments);
  }
  
  window.gtag = gtag;
  gtag('js', new Date());
  gtag('config', config.measurementId || 'GA_MEASUREMENT_ID');
}

// Auto-init with default config
if (typeof window !== 'undefined') {
  initAnalytics();
}
// <<< ELEVENTY_ASSET_PIPELINE analytics.js END
```

### Step 9: Generate import rewriter with dependency-aware hashing
Create `build/rev-imports.mjs`:
```javascript
// >>> ELEVENTY_ASSET_PIPELINE rev-imports BEGIN
#!/usr/bin/env node
import fg from "fast-glob";
import fs from "fs";
import path from "path";
import crypto from "crypto";
import esbuild from "esbuild";

const SRC = "src/assets";
const OUT = "_site/assets";
const LEN = parseInt(process.env.HASH_LEN || "10");

const digest = (str) =>
  crypto.createHash("md5").update(str).digest("hex").slice(0, LEN);

const manifest = {};
const fileHashes = new Map();

// Detect ES module syntax
const hasModuleSyntax = (code) => 
  /(^\s*import\s)|(^\s*export\s)/m.test(code);

console.log("🔍 Processing JavaScript files...");

// Skip if in bundle mode - build-assets.js handles everything
if (process.env.BUNDLE_MODE === 'true') {
  console.log("📦 Bundle mode active - skipping individual file processing");
  process.exit(0);
}

// First pass: process all files and compute their initial hashes
console.log("🔍 Computing initial file hashes...");
const dependencies = new Map(); // Track dependencies for each file

for (const file of fg.sync(`${SRC}/**/!(*.bundle).js`)) {
  const src = fs.readFileSync(file, 'utf8');
  let processedCode;
  
  if (process.env.NODE_ENV === "production") {
    try {
      if (hasModuleSyntax(src)) {
        const result = await esbuild.transform(src, {
          minify: true,
          format: "esm",
          target: "es2020"
        });
        processedCode = result.code;
      } else {
        const result = await esbuild.build({
          stdin: { 
            contents: src, 
            resolveDir: path.dirname(file),
            sourcefile: path.basename(file) 
          },
          bundle: false,
          minify: true,
          format: "iife",
          target: "es2020",
          platform: "browser",
          write: false
        });
        processedCode = result.outputFiles[0].text;
      }
    } catch (error) {
      console.error(`⚠️  Error processing ${file}:`, error.message);
      processedCode = src; // Fall back to original
    }
  } else {
    processedCode = src;
  }
  
  // Extract dependencies
  const deps = [];
  processedCode.replace(
    /(?<=\b(?:from\s*|import\s*\(?\s*)['"])(\.\.?\/[^'"]+?\.js)(?=['"])/g,
    (rel) => {
      const depAbs = path.posix.join(path.dirname(file).replace(/\\/g, '/'), rel);
      deps.push(depAbs.replace(/\//g, path.sep));
      return rel;
    }
  );
  dependencies.set(file, deps);
  
  const hash = digest(processedCode);
  fileHashes.set(file, { hash, processedCode, initialHash: hash });
}

// Second pass: include dependency hashes in the final hash
console.log("🔄 Computing dependency-aware hashes...");
const getDependencyHash = (file) => {
  const deps = dependencies.get(file) || [];
  const depHashes = deps.map(dep => {
    const depData = fileHashes.get(dep);
    return depData ? depData.hash : '';
  }).sort().join(',');
  
  const fileData = fileHashes.get(file);
  // Combine content hash with dependency hashes
  return digest(fileData.initialHash + depHashes);
};

// Update all hashes to include dependencies
for (const file of fileHashes.keys()) {
  const fileData = fileHashes.get(file);
  fileData.hash = getDependencyHash(file);
}

// Third pass: rewrite imports and write files
console.log("📝 Rewriting imports and generating files...");
for (const [file, data] of fileHashes) {
  const { hash, processedCode } = data;
  const relPath = path.relative(SRC, file);
  const outRel = relPath.replace(/\.js$/, `.${hash}.js`);
  const outAbs = path.join(OUT, outRel);

  // Rewrite imports to hashed versions
  const newCode = processedCode.replace(
    /(?<=\b(?:from\s*|import\s*\(?\s*)['"])(\.\.?\/[^'"]+?\.js)(?=['"])/g,
    (importPath) => {
      const absoluteImportPath = path.join(path.dirname(file), importPath);
      const normalizedPath = path.normalize(absoluteImportPath);
      const importData = fileHashes.get(normalizedPath);
      
      if (!importData) {
        return importPath; // Keep original if not found
      }
      
      return importPath.replace(/\.js$/, `.${importData.hash}.js`);
    }
  );

  // Ensure output directory exists
  fs.mkdirSync(path.dirname(outAbs), { recursive: true });
  
  // Clean up old versions - keep only the most recent
  const dir = path.dirname(outAbs);
  const baseFileName = path.basename(file, '.js');
  const currentFileName = path.basename(outAbs);
  
  if (fs.existsSync(dir)) {
    // Find all hashed versions of this file
    const hashedFiles = fs.readdirSync(dir)
      .filter(f => f.startsWith(baseFileName + '.') && f.endsWith('.js') && f !== currentFileName)
      .map(f => ({
        name: f,
        path: path.join(dir, f),
        mtime: fs.statSync(path.join(dir, f)).mtime
      }))
      .sort((a, b) => b.mtime - a.mtime); // Sort by modification time, newest first
    
    // Keep only the most recent old version, delete the rest
    for (let i = 0; i < hashedFiles.length; i++) {
      if (i >= 1) { // Keep index 0 (most recent), delete the rest
        fs.rmSync(hashedFiles[i].path);
      }
    }
  }
  
  // Write new file
  fs.writeFileSync(outAbs, newCode);
  
  // Add to manifest
  manifest[`assets/${relPath}`] = `/assets/${outRel}`;
}

// Helper to safely merge manifest updates
function mergeManifest(updates) {
  const manifestPath = "_site/asset-manifest.json";
  let existing = {};
  
  try {
    if (fs.existsSync(manifestPath)) {
      existing = JSON.parse(fs.readFileSync(manifestPath, "utf8"));
    }
  } catch (e) {
    console.warn("⚠️  Could not read existing manifest");
  }
  
  const merged = { ...existing, ...updates };
  fs.mkdirSync(path.dirname(manifestPath), { recursive: true });
  fs.writeFileSync(manifestPath, JSON.stringify(merged, null, 2));
  return merged;
}

// Update manifest
mergeManifest(manifest);
console.log(`✅ Processed ${fileHashes.size} JavaScript files with dependency-aware hashing`);
// <<< ELEVENTY_ASSET_PIPELINE rev-imports END
```

### Step 10: Generate CSS hasher
Create `build/hash-css.mjs`:
```javascript
// >>> ELEVENTY_ASSET_PIPELINE hash-css BEGIN
#!/usr/bin/env node
import fs from 'fs';
import path from 'path';
import crypto from 'crypto';
import fg from 'fast-glob';

const siteDir = '_site';
const manifestPath = path.join(siteDir, 'asset-manifest.json');
const hashLen = parseInt(process.env.HASH_LEN || "10");

function generateHash(filePath) {
  const content = fs.readFileSync(filePath);
  return crypto.createHash('md5').update(content).digest('hex').slice(0, hashLen);
}

function updateHtmlFiles(oldPath, newPath) {
  const htmlFiles = fg.sync(`${siteDir}/**/*.html`);
  let updatedCount = 0;
  
  for (const file of htmlFiles) {
    let content = fs.readFileSync(file, 'utf8');
    // Match both absolute and relative paths
    const patterns = [
      new RegExp(`(['"])/?${oldPath}(['"])`, 'g'),
      new RegExp(`href="/${oldPath}"`, 'g'),
      new RegExp(`href="${oldPath}"`, 'g')
    ];
    
    let updated = false;
    for (const pattern of patterns) {
      if (pattern.test(content)) {
        content = content.replace(pattern, (match, p1, p2) => {
          if (p1 && p2) return `${p1}/${newPath}${p2}`;
          return match.replace(oldPath, newPath);
        });
        updated = true;
      }
    }
    
    if (updated) {
      fs.writeFileSync(file, content);
      updatedCount++;
    }
  }
  
  return updatedCount;
}

// Helper to safely merge manifest updates
function mergeManifest(updates) {
  let manifest = {};
  try {
    if (fs.existsSync(manifestPath)) {
      manifest = JSON.parse(fs.readFileSync(manifestPath, "utf8"));
    }
  } catch (e) {
    console.warn("⚠️  Could not read existing manifest");
  }
  
  const merged = { ...manifest, ...updates };
  fs.writeFileSync(manifestPath, JSON.stringify(merged, null, 2));
  return merged;
}

console.log('🎨 Hashing CSS files...');
const cssFiles = fg.sync(`${siteDir}/**/!(*.min).css`);
const cssManifest = {};

if (cssFiles.length === 0) {
  console.log('⚠️  No CSS files found to hash');
  process.exit(0);
}

for (const file of cssFiles) {
  const hash = generateHash(file);
  const ext = path.extname(file);
  const name = path.basename(file, ext);
  const dir = path.dirname(file);
  
  // Skip if already hashed
  if (/\.[a-f0-9]{10}\.css$/.test(file)) {
    console.log(`⏭️  Skipping already hashed: ${path.basename(file)}`);
    continue;
  }
  
  const newName = `${name}.${hash}${ext}`;
  const newPath = path.join(dir, newName);
  
  // Remove old hashed versions
  const pattern = new RegExp(`^${name}\\.[a-f0-9]{${hashLen}}\\.css$`);
  fs.readdirSync(dir)
    .filter(f => f !== newName && pattern.test(f))
    .forEach(f => {
      console.log(`🗑️  Removing old version: ${f}`);
      fs.rmSync(path.join(dir, f), { force: true });
    });
  
  // Rename file
  fs.renameSync(file, newPath);
  
  const originalPath = path.relative(siteDir, file).replace(/\\/g, '/');
  const hashedPath = path.relative(siteDir, newPath).replace(/\\/g, '/');
  cssManifest[originalPath] = `/${hashedPath}`;
  
  // Update HTML references
  const updateCount = updateHtmlFiles(originalPath, hashedPath);
  console.log(`✅ ${originalPath} → ${hashedPath} (${updateCount} HTML files updated)`);
}

mergeManifest(cssManifest);
console.log(`✅ CSS hashing complete! Processed ${cssFiles.length} files`);
// <<< ELEVENTY_ASSET_PIPELINE hash-css END
```

### Step 11: Generate CSS minifier
Create `build/minify-css.mjs`:
```javascript
// >>> ELEVENTY_ASSET_PIPELINE minify-css BEGIN
#!/usr/bin/env node
import fs from 'fs';
import postcss from 'postcss';
import cssnano from 'cssnano';
import fg from 'fast-glob';

if (process.env.NODE_ENV !== 'production') {
  console.log('🔧 Development mode: skipping CSS minification');
  process.exit(0);
}

console.log('📦 Minifying CSS for production...');
const cssFiles = fg.sync('_site/**/*.css');

if (cssFiles.length === 0) {
  console.log('⚠️  No CSS files found to minify');
  process.exit(0);
}

let processedCount = 0;
let totalSaved = 0;

for (const file of cssFiles) {
  // Skip already minified files
  if (file.includes('.min.css')) {
    console.log(`⏭️  Skipping minified: ${file}`);
    continue;
  }
  
  try {
    const content = fs.readFileSync(file, 'utf8');
    const originalSize = Buffer.byteLength(content, 'utf8');
    
    const result = await postcss([
      cssnano({
        preset: ['default', {
          discardComments: {
            removeAll: true,
          },
        }]
      })
    ]).process(content, { from: file, to: file });
    
    const minifiedSize = Buffer.byteLength(result.css, 'utf8');
    const saved = originalSize - minifiedSize;
    const percent = Math.round((saved / originalSize) * 100);
    
    fs.writeFileSync(file, result.css);
    
    if (result.map) {
      fs.writeFileSync(`${file}.map`, result.map.toString());
    }
    
    console.log(`✅ ${file} - saved ${saved} bytes (${percent}%)`);
    processedCount++;
    totalSaved += saved;
    
  } catch (error) {
    console.error(`❌ Error minifying ${file}:`, error.message);
  }
}

const totalSavedKB = (totalSaved / 1024).toFixed(2);
console.log(`✅ CSS minification complete! Processed ${processedCount} files, saved ${totalSavedKB}KB total`);
// <<< ELEVENTY_ASSET_PIPELINE minify-css END
```

### Step 12: Generate hash-generated-assets script
Create `build/hash-generated-assets.mjs`:
```javascript
// >>> ELEVENTY_ASSET_PIPELINE hash-generated-assets BEGIN
#!/usr/bin/env node
import fs from "fs";
import path from "path";
import crypto from "crypto";
import fg from "fast-glob";

const SITE_DIR = "_site";
const HASH_LEN = parseInt(process.env.HASH_LEN || "10");

// Skip in development
if (process.env.NODE_ENV === 'development') {
  console.log("🔧 Development mode: skipping generated asset hashing");
  process.exit(0);
}

// Define generated assets to hash (customize as needed)
const GENERATED_ASSETS = [
  `${SITE_DIR}/assets/config.js`,
  `${SITE_DIR}/assets/env.js`,
  // Add other dynamically generated files here
];

function generateHash(content) {
  return crypto.createHash("md5").update(content).digest("hex").slice(0, HASH_LEN);
}

function updateHtmlReferences(oldPath, newPath) {
  const htmlFiles = fg.sync(`${SITE_DIR}/**/*.html`);
  let updateCount = 0;
  
  for (const file of htmlFiles) {
    let content = fs.readFileSync(file, "utf8");
    if (content.includes(oldPath)) {
      content = content.replace(new RegExp(oldPath, 'g'), newPath);
      fs.writeFileSync(file, content);
      updateCount++;
    }
  }
  
  return updateCount;
}

// Helper to merge manifest
function mergeManifest(updates) {
  const manifestPath = path.join(SITE_DIR, "asset-manifest.json");
  let manifest = {};
  
  try {
    if (fs.existsSync(manifestPath)) {
      manifest = JSON.parse(fs.readFileSync(manifestPath, "utf8"));
    }
  } catch (e) {
    console.warn("⚠️  Could not read existing manifest");
  }
  
  const merged = { ...manifest, ...updates };
  fs.writeFileSync(manifestPath, JSON.stringify(merged, null, 2));
  return merged;
}

console.log("🔍 Processing generated assets...");
const updates = {};
let processedCount = 0;

for (const assetPath of GENERATED_ASSETS) {
  if (!fs.existsSync(assetPath)) {
    console.log(`⏭️  Skipping non-existent: ${assetPath}`);
    continue;
  }
  
  const content = fs.readFileSync(assetPath, 'utf8');
  const hash = generateHash(content);
  const ext = path.extname(assetPath);
  const base = path.basename(assetPath, ext);
  const dir = path.dirname(assetPath);
  const hashedName = `${base}.${hash}${ext}`;
  const hashedPath = path.join(dir, hashedName);
  
  // Clean up old versions
  const pattern = new RegExp(`^${base}\\.[a-f0-9]{${HASH_LEN}}${ext}$`);
  fs.readdirSync(dir)
    .filter(f => pattern.test(f) && f !== hashedName)
    .forEach(f => {
      console.log(`🗑️  Removing old: ${f}`);
      fs.rmSync(path.join(dir, f), { force: true });
    });
  
  // Create hashed copy
  fs.copyFileSync(assetPath, hashedPath);
  
  // Update references
  const originalUrl = '/' + path.relative(SITE_DIR, assetPath).replace(/\\/g, '/');
  const hashedUrl = '/' + path.relative(SITE_DIR, hashedPath).replace(/\\/g, '/');
  const updateCount = updateHtmlReferences(originalUrl, hashedUrl);
  
  updates[originalUrl.substring(1)] = hashedUrl;
  console.log(`✅ ${base}${ext} → ${hashedName} (${updateCount} HTML files updated)`);
  processedCount++;
}

if (processedCount > 0) {
  mergeManifest(updates);
  console.log(`✅ Generated asset hashing complete! Processed ${processedCount} files`);
} else {
  console.log("ℹ️  No generated assets to process");
}
// <<< ELEVENTY_ASSET_PIPELINE hash-generated-assets END
```

### Step 13: Update Eleventy config
Create or update `eleventy.config.js`:
```javascript
// >>> ELEVENTY_ASSET_PIPELINE eleventy.config BEGIN
const fs = require('fs');
const path = require('path');
const crypto = require('crypto');
const esbuild = require('esbuild');

// Load environment variables if .env exists
if (fs.existsSync('.env')) {
  require('dotenv').config();
}

module.exports = function(eleventyConfig) {
  // Ignore build artifacts and cache
  eleventyConfig.ignores.add("src/_data/.cache/");
  eleventyConfig.ignores.add("**/.cache/**");
  eleventyConfig.watchIgnores.add("src/_data/.cache/**/*");
  eleventyConfig.watchIgnores.add("**/.cache/**");
  
  // Asset pipeline: Before build
  eleventyConfig.on("eleventy.before", () => {
    const { execSync } = require("child_process");
    
    // Only run rev-imports if not in bundle mode
    if (process.env.BUNDLE_MODE !== 'true') {
      execSync("node build/rev-imports.mjs", {
        stdio: "inherit",
        env: { ...process.env }
      });
    }
  });
  
  // Asset pipeline: After build
  eleventyConfig.on("eleventy.after", () => {
    const { execSync } = require("child_process");
    
    // Run post-build scripts
    const scripts = [
      "build/hash-generated-assets.mjs",
      "build/hash-css.mjs",
      "build/minify-css.mjs"
    ];
    
    for (const script of scripts) {
      if (fs.existsSync(script)) {
        execSync(`node ${script}`, { 
          stdio: "inherit",
          env: { ...process.env }
        });
      }
    }
  });
  
  // Function to load bundle manifest dynamically
  function getBundleManifest() {
    const bundleManifestPath = path.join(__dirname, 'src/_data/bundles.json');
    if (fs.existsSync(bundleManifestPath)) {
      try {
        return JSON.parse(fs.readFileSync(bundleManifestPath, 'utf8'));
      } catch (e) {
        console.warn('Could not parse bundles.json:', e.message);
        return {};
      }
    }
    return {};
  }
  
  // Make bundles globally available
  eleventyConfig.addGlobalData("bundles", getBundleManifest());
  
  // Shortcode for bundle paths
  eleventyConfig.addShortcode("bundle", function(bundleName) {
    const manifest = getBundleManifest();
    return manifest[bundleName] || '';
  });
  
  // Filter for cache-busted assets
  eleventyConfig.addFilter("rev", (src) => {
    const manifestPath = path.join(__dirname, '_site/asset-manifest.json');
    if (fs.existsSync(manifestPath)) {
      const manifest = JSON.parse(fs.readFileSync(manifestPath, 'utf8'));
      
      // Normalize path
      let normalizedSrc = src.replace(/^\/+/, '');
      
      // Try multiple variations
      const variations = [
        normalizedSrc,
        normalizedSrc.replace('src/', ''),
        `assets/${normalizedSrc}`,
        normalizedSrc.replace('src/assets/', 'assets/')
      ];
      
      for (const variant of variations) {
        if (manifest[variant]) {
          return manifest[variant];
        }
      }
    }
    
    // If not in manifest, return normalized path
    return '/' + src.replace(/^\/+/, '').replace('src/', '');
  });
  
  // Bundle filter for on-demand bundling
  eleventyConfig.addFilter("bundleRev", async (src) => {
    src = src.replace(/^\/+/, "");
    
    try {
      const result = await esbuild.build({
        entryPoints: [path.join(__dirname, src)],
        bundle: true,
        format: "esm",
        write: false,
        platform: 'browser',
        target: 'es2020',
        minify: process.env.NODE_ENV === 'production'
      });
      
      const code = result.outputFiles[0].contents;
      const hash = crypto.createHash("md5").update(code).digest("hex").slice(0, 10);
      const ext = path.extname(src);
      const name = path.basename(src, ext);
      const outputDir = path.dirname(src).replace(/^src\//, '');
      const outPath = path.join("_site", outputDir, `${name}.${hash}${ext}`);
      
      // Write bundled file
      fs.mkdirSync(path.dirname(outPath), { recursive: true });
      fs.writeFileSync(outPath, code);
      
      return `/${outputDir}/${name}.${hash}${ext}`;
    } catch (error) {
      console.error(`Bundle error for ${src}:`, error);
      return eleventyConfig.getFilter("rev")(src);
    }
  });
  
  // Copy static assets
  eleventyConfig.addPassthroughCopy("src/assets/images");
  eleventyConfig.addPassthroughCopy("src/assets/fonts");
  eleventyConfig.addPassthroughCopy({"src/favicon.ico": "/favicon.ico"});
  
  // Watch targets
  eleventyConfig.addWatchTarget("src/assets/**/*.js");
  eleventyConfig.addWatchTarget("src/assets/**/*.css");
  eleventyConfig.setWatchJavaScriptDependencies(false);
  
  return {
    dir: {
      input: "src",
      includes: "_includes",
      layouts: "_includes/layouts",
      data: "_data",
      output: "_site"
    },
    markdownTemplateEngine: "njk",
    htmlTemplateEngine: "njk",
    dataTemplateEngine: "njk"
  };
};
// <<< ELEVENTY_ASSET_PIPELINE eleventy.config END
```

### Step 14: Create example layout
Create `src/_includes/layouts/base.njk`:
```html
<!-- >>> ELEVENTY_ASSET_PIPELINE base.njk BEGIN -->
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>{{ title or "My Site" }}</title>
  
  <!-- Cache-busted CSS -->
  <link rel="stylesheet" href="{{ '/assets/styles/main.css' | rev }}">
  
  <!-- Preload critical bundles -->
  {% if bundles.main %}
  <link rel="modulepreload" href="{{ bundles.main }}">
  {% endif %}
</head>
<body>
  <main>
    {{ content | safe }}
  </main>
  
  <!-- JavaScript bundles -->
  {% if bundles.main %}
  
  {% endif %}
  
  <!-- Page-specific bundles -->
  {% if page.bundle == 'admin' and bundles.admin %}
  
  {% endif %}
  
  <!-- Or use individual files in dev -->
  {% if not bundles.main %}
  
  
  {% endif %}
</body>
</html>
<!-- <<< ELEVENTY_ASSET_PIPELINE base.njk END -->
```

### Step 15: Create config.njk for dynamic config
Create `src/assets/config.njk`:
```javascript
---
permalink: /assets/config.js
eleventyExcludeFromCollections: true
---
// >>> ELEVENTY_ASSET_PIPELINE config.njk BEGIN
// Dynamically generated config - will be hashed in production
window.APP_CONFIG = {
  environment: "{{ eleventy.env.runMode }}",
  buildTime: "{{ page.date.toISOString() }}",
  version: "{{ pkg.version or '1.0.0' }}",
  api: {
    baseUrl: "{{ env.API_URL or '/api' }}"
  }
};
// <<< ELEVENTY_ASSET_PIPELINE config.njk END
```

---

## Using the Pipeline

### In Templates
```nunjucks
<!-- Basic usage -->
<link rel="stylesheet" href="{{ '/assets/styles/main.css' | rev }}">


<!-- Bundle usage -->


<!-- Conditional bundles -->
{% if page.isAdmin %}

{% endif %}

<!-- Individual files (non-bundled) -->

```

### Accessing Manifest Data
```javascript
// In your templates via global data
const mainBundle = bundles.main; // "/assets/main-a1b2c3d4e5.js"

// In Node.js scripts
const manifest = require('./_site/asset-manifest.json');
const cssPath = manifest['assets/styles/main.css']; // "/assets/styles/main.1234567890.css"
```

---

## Re-running the Command
This command is **safe to re-run** anytime:
* It will update code between sentinel comments only
* Manual edits outside sentinels are preserved
* Use it to upgrade or fix the pipeline

---


## Troubleshooting

### Common Issues
1. **Missing dependencies**: Run the npm install command from Prerequisites
2. **Build fails**: Check that all directories exist (`mkdir -p` commands)
3. **Bundles not found**: Ensure `BUNDLE_MODE=true` is set for production
4. **CSS not building**: Verify PostCSS and Tailwind configs are in root
5. **Windows paths**: The pipeline normalizes paths automatically
6. **Source maps missing**: Check `SOURCE_MAPS` env var isn't set to `false`
7. **Browser cache issues with modules**: The pipeline uses dependency-aware hashing - when any module changes, all importing modules get new hashes to break browser cache chains

### Debug Mode
```bash
DEBUG=1 npm run build  # Verbose output
```

### Clean Start
```bash
npm run clean
rm -rf node_modules package-lock.json
npm install
npm run build
```

---

## Validation & Safety

The AI should perform these checks before implementing:

1. **Project Validation**
   * ✓ Eleventy config file exists (.eleventy.js or eleventy.config.js)
   * ✓ package.json is present
   * ✓ Source directory structure matches Eleventy patterns
   * ✓ No conflicting asset pipeline already exists

2. **Backup Strategy**
   * Create .backup files for any existing configs that will be modified
   * List all files that will be created/modified before proceeding
   * Warn about any potential conflicts

3. **Adaptation Logic**
   * If src/ directory exists, use it as base
   * If _site/ doesn't exist, check for custom output directory in config
   * Adapt script paths if package.json shows different structure
   * Respect existing Eleventy configuration patterns

4. **Success Indicators**
   * All files created successfully
   * No npm errors when installing dependencies
   * Build scripts added to package.json
   * Asset manifest generation confirmed

---

## Next Steps
1. Run `npm install` to install any missing dependencies
2. Use `npm run dev` for development with hot reloading
3. Use `npm run build:bundles` for production builds
4. Customize bundle definitions in `scripts/build-assets.js`
5. Add your JavaScript files to appropriate bundle arrays
6. Configure Tailwind theme in `tailwind.config.js`
