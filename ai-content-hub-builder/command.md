# AI Content Hub Builder
---
description: >
  Create a dedicated AI Content Hub section on your website with a specialized subagent for generating technical content. Keeps AI-generated content separate from your editorial blog while providing value to users.
allowed_tools:
  - Write         # Create hub structure and subagent
  - Read          # Check existing structure
  - MultiEdit     # Update navigation
  - LS            # List directories
tags:
  - content
  - ai-hub
  - subagent
  - setup
category: content-creation
---

## What This Command Does

1. Creates an AI Content Hub directory structure
2. Adds a content generation subagent
3. Updates site navigation
4. Optionally generates sample content

## Arguments

```
/ai-content-hub-builder
[section_name=<string>]       # Hub directory name (default: "ai-hub")
[create_sample=<true|false>]  # Generate sample content (default: false)
[add_to_nav=<true|false>]     # Add to site navigation (default: true)
```

## Instructions for Claude Code

When this command runs:

### 1. Check Prerequisites

First, verify we're in a valid project:
- Look for common static site generators (Eleventy, Jekyll, Hugo)
- Check for a `site/src/` or similar content directory
- If no site structure found, inform the user this needs a website project

### 2. Create Hub Structure

Create the following directory structure:
```
site/src/{section_name}/
├── index.md           # Hub homepage
├── tutorials/         # Step-by-step guides
├── guides/           # Conceptual content
├── comparisons/      # Feature comparisons
└── troubleshooting/  # Problem-solving content
```

### 3. Create Hub Homepage

Write `site/src/{section_name}/index.njk`:
```liquid
---
layout: base-layout.njk
title: AI Content Hub
description: AI-generated tutorials, guides, and resources
eleventyNavigation:
  key: AI Hub
  order: 3
---

<h1>AI Content Hub</h1>

Welcome to our AI-generated content section. All content here is created by AI to help you learn quickly.

**🤖 Note:** This entire section contains AI-generated content. For editorial content, visit our [main blog](/blog/).

## Categories

### [Tutorials](/ai-hub/tutorials/)
Step-by-step guides for specific tasks

### [Guides](/ai-hub/guides/)  
Conceptual overviews and best practices

### [Comparisons](/ai-hub/comparisons/)
Side-by-side analysis of tools and approaches

### [Troubleshooting](/ai-hub/troubleshooting/)
Solutions to common problems
```

### 4. Create the Subagent

Create `.claude/agents/ai-content-generator.md`:
```markdown
---
name: ai-content-generator
description: Generates technical content for the AI Content Hub when asked to create tutorials, guides, or documentation
tools: Read, Write, WebSearch
---

You are a technical content generator for the AI Content Hub. When asked to create content for the AI Hub, you generate well-structured, informative articles.

## Content Guidelines

1. **File format** - Save as `.njk` files for Eleventy
2. **Always mark as AI-generated** - Include `ai_generated: true` in frontmatter
3. **Use clear structure** - Headers, bullet points, code examples
4. **Be factual** - No opinions, just technical information
5. **Include examples** - Practical code snippets when relevant
6. **Save to correct location** - Under `site/src/ai-hub/{category}/`

## Content Types

**Tutorials** (`/tutorials/`): Step-by-step guides
- "Getting Started with X"
- "How to implement Y"
- Clear numbered steps

**Guides** (`/guides/`): Conceptual overviews
- "Understanding X"
- "Best Practices for Y"
- Deeper explanations

**Comparisons** (`/comparisons/`): Analysis
- "X vs Y: Key Differences"
- Feature matrices
- Use case recommendations

**Troubleshooting** (`/troubleshooting/`): Problem-solving
- "Fixing X Error"
- "Common Y Issues"
- Solution steps

## Frontmatter Template

```yaml
---
title: "Clear, Descriptive Title"
description: "One sentence summary"
date: YYYY-MM-DD
category: [tutorials|guides|comparisons|troubleshooting]
tags: [relevant, tags]
ai_generated: true
permalink: /ai-hub/{category}/{url-slug}/
layout: base-layout.njk
---
```

## Important: File Format

- Save all files with `.njk` extension (Eleventy format)
- Use HTML with Liquid templating for content
- Include proper permalink in frontmatter for clean URLs
- Wrap content in `<article class="ai-generated-content">` tags

Example filename: `getting-started-puppeteer-mcp.njk`

When creating content, analyze the request to determine the appropriate category and generate comprehensive, helpful content with proper Eleventy formatting.
```

### 5. Update Navigation (if add_to_nav=true)

Look for the main navigation file (often `_includes/nav.njk` or similar) and add a link to the AI Hub after the Blog link:
```html
<a href="/ai-hub/">AI Hub <span class="text-xs">🤖</span></a>
```

### 6. Create Sample Content (if create_sample=true)

Generate a sample tutorial at `site/src/ai-hub/tutorials/getting-started-claude-code-subagents.njk`:
```liquid
---
title: "Getting Started with Claude Code Subagents"
description: "Learn how to create and use subagents in Claude Code"
date: {today}
category: tutorials
tags: [claude-code, subagents, automation]
ai_generated: true
permalink: /ai-hub/tutorials/getting-started-claude-code-subagents/
---

<article class="ai-generated-content">
  <div class="ai-notice">
    <span>🤖</span> This content is AI-generated
  </div>
  
  <h1>{{ title }}</h1>

This AI-generated tutorial will help you understand Claude Code subagents.

## What are Subagents?

Subagents are specialized AI assistants that handle specific tasks...

[Continue with comprehensive tutorial content]
```

### 7. Final Output

Show the user:
```
✅ AI Content Hub created successfully!

📁 Structure created at: site/src/{section_name}/
🤖 Subagent added: ai-content-generator
🔗 Navigation updated (if applicable)

To use your AI Content Hub:
1. Ask: "Create a tutorial about X in the AI Hub"
2. The ai-content-generator will handle it automatically
3. Find generated content in site/src/{section_name}/

Try it now: "Create a tutorial about getting started with MCP servers in the AI Hub"
```

## Example Usage

After running this command, users can simply ask:
- "Create a tutorial about Puppeteer MCP in the AI Hub"
- "Write a troubleshooting guide for connection errors"
- "Generate a comparison of GitHub vs GitLab integration"

The subagent will automatically create the content in the appropriate category.

## Notes

- All content is clearly marked as AI-generated
- The hub is separate from the main blog to preserve editorial integrity
- Content is SEO-friendly but labeled appropriately
- The subagent works automatically when AI Hub content is requested