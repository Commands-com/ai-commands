# Blog Content Agent Builder

Install a specialized Claude Code subagent that automatically generates SEO-optimized blog posts, turning your static blog into a dynamic content hub.

## What It Does

This command transforms Claude Code into a content creation powerhouse by:

1. **Analyzing your blog structure** and existing content patterns
2. **Creating a specialized subagent** that writes in your brand voice
3. **Setting up automated workflows** for consistent content generation

## Quick Start

```bash
# Install with auto-detection
/blog-content-agent-builder

# Generate your first post immediately
/blog-content-agent-builder topic="New MCP server launch" publish=draft

# Batch generate multiple posts
/blog-content-agent-builder generate_ideas=10 category=tutorials
```

## How The Subagent Works

Once installed, just tell Claude Code what you need:
- "Write a blog post about the new Puppeteer MCP"
- "Create a tutorial for beginners using commands"
- "Generate this week's platform updates post"
- "Write about trending AI prompts"

The blog agent will:
1. **Research** the topic using your existing content
2. **Generate** SEO-optimized title, meta description, and content
3. **Create** code examples and visual elements
4. **Format** with proper markdown and frontmatter
5. **Optimize** for search engines and social sharing

## Example Output

```
Blog Content Agent:
[1/4] Researching topic...
  ✓ Found 3 related MCP servers
  ✓ Identified 5 trending use cases
  
[2/4] Generating content...
  ✓ Title: "Automate Your Browser Testing with Puppeteer MCP"
  ✓ Meta: "Learn how to use the Puppeteer MCP server for..."
  ✓ Word count: 1,247
  
[3/4] Optimizing for SEO...
  ✓ Added schema.org markup
  ✓ Generated social preview image
  ✓ Internal links: 4 related posts
  
[4/4] Blog post ready!
  📝 Location: site/src/blog/2024-01-30-puppeteer-mcp.md
  🎯 SEO Score: 94/100
  📊 Readability: Grade 8
```

## Features

- **Smart Topic Research**: Analyzes your platform data for relevant content
- **Brand Voice Matching**: Learns from existing posts
- **SEO Optimization**: Meta tags, schema markup, keyword density
- **Code Examples**: Auto-generates relevant code snippets
- **Image Suggestions**: Recommends or generates featured images
- **Cross-linking**: Automatically links to related content
- **Publishing Workflow**: Draft → Review → Publish states

## Perfect For

- Platform announcements
- Feature tutorials  
- Creator spotlights
- Weekly roundups
- SEO content campaigns
- Technical deep-dives

## Demo Impact

**Before**: 1 static blog post
**After**: Dynamic blog with fresh, SEO-optimized content
**Time Saved**: 2-3 hours per post → 5 minutes
**SEO Boost**: 3x more organic traffic potential

## Requirements

- Claude Code (claude.ai/code)
- Existing blog structure (Eleventy, Jekyll, Hugo, etc.)
- Basic markdown knowledge for review

## Learn More

See the full [command documentation](command.md) for advanced features, content strategies, and API integration options.