# Eleventy Asset Pipeline

A comprehensive asset management solution for Eleventy projects that validates your setup and provides production-grade JavaScript bundling, CSS processing, and cache-busting with minimal configuration.

## Overview

The Eleventy Asset Pipeline command first validates your Eleventy project structure, then scaffolds a complete build system for managing JavaScript and CSS assets. It provides:

- **Project Validation** to ensure compatibility before making changes
- **JavaScript bundling** with esbuild for optimal performance
- **CSS processing** with PostCSS, Tailwind CSS, and automatic minification
- **Cache-busting** with content-based hashing for all assets
- **Development/Production modes** with appropriate optimizations
- **Bundle splitting** for better caching and performance
- **Source maps** for easier debugging

## Features

### Project Validation
- Checks for Eleventy configuration files
- Verifies package.json exists
- Detects source directory structure
- Warns about potential conflicts
- Creates backups of modified files

### JavaScript Management
- ES module bundling with automatic code splitting
- Development mode with individual file processing for faster rebuilds
- Production mode with optimized bundles and minification
- Automatic import path rewriting for hashed assets
- Support for both bundled and individual file loading

### CSS Processing
- PostCSS pipeline with optional Tailwind CSS integration
- Automatic vendor prefixing with Autoprefixer
- Production minification with cssnano
- Content-based hashing for cache-busting
- Source map generation

### Asset Pipeline Features
- Content-based hashing for all assets (JS, CSS)
- Automatic HTML reference updates
- Bundle manifest generation for dynamic imports
- Clean build process that removes old hashed files
- Configurable hash length and source map generation

## Prerequisites

Before running this command, ensure you have:
- An existing Eleventy project
- Node.js 14+ installed
- npm or yarn package manager

## How to Use

1. **Bundle Mode**: Choose whether to bundle JavaScript for production or keep files separate for development
2. **Hash Length**: Select the length of content hashes for cache-busting (8-16 characters)
3. **Source Maps**: Enable/disable source map generation in production builds
4. **CSS Framework**: Choose between Tailwind CSS or no framework

The command will:
- Validate your Eleventy project structure
- Check for existing configurations
- Install required dependencies
- Create the asset pipeline with all necessary files
- Set up build scripts in package.json

## Configuration Options

| Parameter | Default | Description |
|-----------|---------|-------------|
| `bundle_mode` | `false` | Enable bundle mode for production builds |
| `hash_length` | `10` | Length of content hash for cache-busting |
| `source_maps` | `true` | Generate source maps in production |
| `css_framework` | `tailwind` | CSS framework to configure |

## Project Structure

After running the command, your project will have:

```
your-project/
├── build/                    # Build scripts
│   ├── rev-imports.mjs      # JavaScript processor
│   ├── hash-css.mjs         # CSS hasher
│   ├── minify-css.mjs       # CSS minifier
│   └── hash-generated-assets.mjs
├── scripts/
│   └── build-assets.js      # Main bundler script
├── src/
│   ├── assets/
│   │   ├── js/             # JavaScript source files
│   │   ├── styles/         # CSS source files
│   │   └── bundles/        # Bundle entry points
│   ├── _data/              # Eleventy data files
│   └── _includes/
│       └── layouts/        # Layout templates
├── postcss.config.js       # PostCSS configuration
├── tailwind.config.js      # Tailwind configuration
└── eleventy.config.js      # Updated Eleventy config
```

## Development Workflow

### Development Mode
```bash
npm run dev
```
- Runs Eleventy with live reload
- Processes CSS with PostCSS in watch mode
- Individual JavaScript files for faster rebuilds
- No minification for easier debugging

### Production Build
```bash
npm run build
```
- Minifies and optimizes all assets
- Generates content-hashed filenames
- Creates optimized bundles
- Produces production-ready output

### Bundle Mode
```bash
npm run build:bundles
```
- Creates optimized JavaScript bundles
- Enables code splitting for better caching
- Ideal for production deployments

## Using Assets in Templates

### CSS
```nunjucks
<!-- Automatically hashed CSS -->
<link rel="stylesheet" href="{{ '/assets/styles/main.css' | rev }}">
```

### JavaScript Bundles
```nunjucks
<!-- Use bundles in production -->
{% if bundles.main %}
<script src="{{ bundles.main }}" type="module"></script>
{% endif %}

<!-- Or individual files in development -->
{% if not bundles.main %}
<script src="{{ '/assets/js/app.js' | rev }}" type="module"></script>
{% endif %}
```

### Dynamic Configuration
```nunjucks
<!-- Hashed configuration file -->
<script src="{{ '/assets/config.js' | rev }}"></script>
```

## Customization

### Adding New Bundles
Edit `scripts/build-assets.js` to define new bundles:

```javascript
const bundles = bundleMode ? {
  'main': ['src/assets/bundles/main-bundle.js'],
  'admin': ['src/assets/bundles/admin-bundle.js'],
  'your-bundle': ['src/assets/bundles/your-bundle.js']
} : {
  // Development mode mappings
};
```

### Extending PostCSS
Add plugins to `postcss.config.js`:

```javascript
module.exports = {
  plugins: [
    require('tailwindcss'),
    require('autoprefixer'),
    require('your-plugin'),
    // ... more plugins
  ]
}
```

### Custom Build Steps
The pipeline uses Eleventy events for extensibility:
- `eleventy.before` - Runs before the build
- `eleventy.after` - Runs after the build

## Troubleshooting

### Common Issues

**Missing Dependencies**
```bash
npm install --save-dev @11ty/eleventy esbuild postcss autoprefixer cssnano tailwindcss fast-glob dotenv npm-run-all
```

**Build Failures**
- Check that all directories exist
- Verify PostCSS and Tailwind configs are in the root
- Ensure bundle mode is set correctly

**Assets Not Found**
- Run a clean build: `npm run clean && npm run build`
- Check the asset manifest at `_site/asset-manifest.json`
- Verify file paths in templates

### Debug Mode
```bash
DEBUG=1 npm run build
```

## Best Practices

1. **Development**: Use individual files for faster rebuilds
2. **Production**: Always use bundle mode with minification
3. **Caching**: Rely on content hashes, not query strings
4. **Performance**: Enable code splitting for large applications
5. **Security**: Keep dependencies updated regularly

## Safety Features

- Validates Eleventy project before making changes
- Creates .backup files for any modified configurations
- Lists all files to be created/modified before proceeding
- Adapts to your existing project structure
- Preserves your custom Eleventy settings

## License

This command is provided under the MIT License. See the LICENSE file for details.