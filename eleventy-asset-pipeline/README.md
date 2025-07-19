# Eleventy Asset Pipeline

A comprehensive asset management solution for Eleventy projects that provides production-grade JavaScript bundling, CSS processing, and cache-busting with minimal configuration.

## Overview

The Eleventy Asset Pipeline command scaffolds a complete build system for managing JavaScript and CSS assets in your Eleventy projects. It provides:

- **JavaScript bundling** with esbuild for optimal performance
- **CSS processing** with PostCSS, Tailwind CSS, and automatic minification
- **Cache-busting** with content-based hashing for all assets
- **Development/Production modes** with appropriate optimizations
- **Bundle splitting** for better caching and performance
- **Source maps** for easier debugging

## Features

### JavaScript Management
- ES module bundling with automatic code splitting
- Development mode with individual file processing for faster rebuilds
- Production mode with optimized bundles and minification
- Automatic import path rewriting for hashed assets
- Support for both bundled and individual file loading

### CSS Processing
- PostCSS pipeline with Tailwind CSS integration
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

## Usage

1. Navigate to your Eleventy project root
2. Run the command with your preferred settings
3. The command will:
   - Install required dependencies
   - Create the directory structure
   - Generate build scripts and configuration files
   - Set up example JavaScript and CSS files
   - Configure Eleventy for the asset pipeline

## Configuration Options

The command respects these environment variables:

| Variable | Default | Description |
|----------|---------|-------------|
| `BUNDLE_MODE` | `false` | Enable bundle mode for production builds |
| `NODE_ENV` | `development` | Set to `production` for optimized builds |
| `HASH_LEN` | `10` | Length of content hash for cache-busting |
| `SOURCE_MAPS` | `true` | Generate source maps in production |

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
BUNDLE_MODE=true npm run build:bundles
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
- Ensure `BUNDLE_MODE` is set correctly

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

## License

This command is provided under the MIT License. See the LICENSE file for details.