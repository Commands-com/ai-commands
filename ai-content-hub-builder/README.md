# AI Content Hub Builder

Create a dedicated AI Content Hub section on your website with a specialized subagent for generating technical content. Perfect for demonstrating AI capabilities while keeping AI-generated content separate from your editorial blog.

## What It Does

This command:

1. **Creates an AI Content Hub** - A separate `/ai-hub/` section for AI-generated content
2. **Adds a content generation subagent** - Specialized for creating tutorials, guides, and documentation
3. **Sets up clear labeling** - All AI content is marked as such

## Quick Start

```bash
# Create AI Content Hub with default settings
/ai-content-hub-builder

# Customize the hub location
/ai-content-hub-builder section_name="resources"

# Generate sample content immediately
/ai-content-hub-builder create_sample=true
```

## How It Works

After running this command:
- Ask Claude: "Create a tutorial about Puppeteer MCP in the AI Hub"
- The subagent will automatically handle it
- Content appears in `site/src/ai-hub/` with proper formatting

## Features

- **Separate Section**: Keeps AI content distinct from editorial blog
- **Auto-categorization**: Tutorials, guides, comparisons, troubleshooting
- **SEO Optimized**: Still benefits from search traffic
- **Clear Labels**: Every page shows "AI-Generated Content"

## Perfect For

- Quick resource generation
- Technical documentation
- Tutorial libraries
- Demo content for presentations

## Requirements

- Claude Code (claude.ai/code)
- Website with static site generator (Eleventy, Jekyll, etc.)

## Next Steps

After installation:
1. Ask Claude to generate content for the AI Hub
2. Use `/parallel-content-demo` for impressive multi-content demos
3. Review and publish the generated content

## Learn More

See the full [command documentation](command.md) for customization options.