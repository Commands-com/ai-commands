# AI Content Hub Agent Builder

Install a specialized Claude Code subagent that creates an AI-powered content hub for your platform - a dedicated section for AI-generated tutorials, guides, and resources that complement your main blog.

## What It Does

This command transforms Claude Code into a content generation system by:

1. **Creating a dedicated AI Content Hub** separate from your main blog
2. **Installing a specialized content agent** that generates technical content
3. **Setting up the infrastructure** for parallel content generation demos

## Quick Start

```bash
# Install the AI Content Hub
/ai-content-hub-agent-builder

# Install with custom section name
/ai-content-hub-agent-builder section_name="ai-resources"

# Install with specific content types
/ai-content-hub-agent-builder content_types="tutorials,guides,comparisons"
```

## Two-Command System

This is part 1 of a powerful demo system:

1. **This Command**: Sets up the AI Content Hub infrastructure
2. **Next Command**: `/parallel-content-generator` - Runs multiple agents simultaneously for impressive demos

## How The Subagent Works

Once installed, the content hub agent can:
- Generate technical tutorials for MCP servers
- Create comparison guides for AI tools
- Write how-to guides for commands
- Produce troubleshooting resources
- Generate code examples and snippets

The agent creates content in a separate section, preserving your blog's editorial voice while providing valuable AI-generated resources.

## Example Structure

```
site/
├── src/
│   ├── blog/           # Your curated blog posts
│   └── ai-hub/         # AI-generated content
│       ├── tutorials/
│       ├── guides/
│       ├── comparisons/
│       └── resources/
```

## Features

- **Separate Section**: Keeps AI content distinct from editorial content
- **Quality Control**: Built-in validation and formatting
- **SEO Optimized**: Still gets search traffic benefits
- **Clearly Labeled**: Each piece marked as "AI-Generated"
- **Parallel Ready**: Optimized for multi-agent demos

## Perfect For

- Building a resource library quickly
- Demonstrating AI capabilities
- Creating comprehensive documentation
- Generating tutorial content at scale
- Impressive live demos

## Requirements

- Claude Code (claude.ai/code)
- Existing website structure
- Node.js for build process

## Next Steps

After installation, use `/parallel-content-generator` to create multiple pieces of content simultaneously for powerful demos!

## Learn More

See the full [command documentation](command.md) for configuration options and customization.