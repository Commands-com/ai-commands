# Parallel Content Demo
---
description: >
  Create an impressive demonstration by generating multiple pieces of content simultaneously. Uses Claude Code's Task tool to run parallel content generation with real-time progress tracking via TodoWrite.
allowed_tools:
  - Task          # Run parallel content generation
  - TodoWrite     # Track progress  
  - Read          # Verify AI Hub exists
  - LS            # Check directories
tags:
  - demo
  - content
  - parallel
  - showcase
category: demo-tools
---

## What This Command Does

1. Validates the AI Content Hub exists
2. Generates or uses provided topics
3. Creates a todo list for tracking
4. Launches parallel content generation tasks
5. Shows real-time progress updates

## Arguments

```
/parallel-content-demo
[count=<number>]              # Number of articles (default: 5, max: 10)
[type=<content-type>]         # Focus on specific type (tutorials/guides/etc)
[topics=<comma-separated>]    # Custom topics instead of auto-generated
```

## Instructions for Claude Code

When this command runs:

### 1. Validate Prerequisites

Check that the AI Content Hub exists:
- Look for `site/src/ai-hub/` directory
- Verify the ai-content-generator subagent exists
- If missing, tell user to run `/ai-content-hub-builder` first

### 2. Generate Topics

If topics not provided, generate them based on the count and type:

**For tutorials:**
- "Getting Started with Puppeteer MCP"
- "Building Your First Command"  
- "Setting Up GitHub Integration"
- "Creating Custom Prompts"
- "Implementing MCP Servers"

**For guides:**
- "Understanding MCP Architecture"
- "Best Practices for Commands"
- "Security in AI Applications"
- "Performance Optimization"
- "Integration Patterns"

**For comparisons:**
- "Puppeteer vs Playwright MCP"
- "Local vs Cloud Deployment"
- "Different Authentication Methods"

**For troubleshooting:**
- "Fixing Connection Errors"
- "Debugging Command Failures"
- "Resolving Permission Issues"

Mix types if no specific type requested.

### 3. Create Todo List

Use TodoWrite to create a comprehensive todo list:
```javascript
{
  todos: [
    {
      id: "content-1",
      content: "Tutorial: Getting Started with Puppeteer MCP",
      status: "pending",
      priority: "high"
    },
    {
      id: "content-2", 
      content: "Guide: Understanding MCP Architecture",
      status: "pending",
      priority: "high"
    },
    // ... more items based on count
  ]
}
```

### 4. Launch Parallel Tasks

For each topic, use the Task tool to invoke the ai-content-generator subagent:

```
Task description: "Generate tutorial: Getting Started with Puppeteer MCP"
Task prompt: 
  Use the ai-content-generator to create a tutorial about "Getting Started with Puppeteer MCP" in the AI Hub.
  
  IMPORTANT: 
  1. Save as .njk file (Eleventy format) with proper frontmatter
  2. Include permalink in frontmatter for clean URLs
  3. Update todo item "content-1" as you progress:
     - Mark as "in_progress" when starting
     - Update content with status: "Researching topic...", "Writing content...", "Adding examples...", etc.
     - Mark as "completed" when done

Subagent type: ai-content-generator
```

Launch all tasks simultaneously (don't await each one).

### 5. Monitor Progress

The todo list will automatically update as each task progresses. When all todos show "completed":

1. **Collect Generated Content**: Read the metadata from each generated file to get titles and paths

2. **Update Hub Index**: Update the AI Hub homepage (`site/src/ai-hub/index.njk`) to include a "Recently Generated" section with links to all the new content:

```markdown
## Recently Generated Content

### Latest Batch (Generated on [date])

**Tutorials:**
- [Getting Started with Puppeteer MCP](/ai-hub/tutorials/getting-started-puppeteer-mcp/)
- [GitHub Integration Setup](/ai-hub/tutorials/github-integration-setup/)

**Guides:**
- [Understanding MCP Architecture](/ai-hub/guides/understanding-mcp-architecture/)

**Comparisons:**
- [Puppeteer vs Playwright](/ai-hub/comparisons/puppeteer-vs-playwright/)

**Troubleshooting:**
- [Connection Issues](/ai-hub/troubleshooting/connection-issues/)
```

3. **Update Category Pages**: For each category that received new content, update its index page (e.g., `site/src/ai-hub/tutorials/index.njk`) to list the new articles at the top.

4. **Display Summary**:

```
✅ Parallel Content Generation Complete!

Generated 5 articles in 45 seconds:
✓ Tutorial: Getting Started with Puppeteer MCP
✓ Guide: Understanding MCP Architecture  
✓ Tutorial: GitHub Integration Setup
✓ Comparison: Puppeteer vs Playwright
✓ Troubleshooting: Connection Issues

📁 Content created in: site/src/ai-hub/
🔗 Hub index updated with new content links
📂 Category pages updated

🌐 View your AI Hub at: http://localhost:8080/ai-hub/

Average time per article: 9 seconds
Total words generated: ~7,500
```

## Example Topics by Category

**Tutorials** (step-by-step):
- Getting Started with [MCP Server]
- Building Your First [Feature]
- Implementing [Integration]
- Setting Up [Tool]

**Guides** (conceptual):
- Understanding [Architecture]
- Best Practices for [Feature]
- Deep Dive into [System]
- Mastering [Concept]

**Comparisons** (analysis):
- [Tool A] vs [Tool B]
- Choosing Between [Options]
- [Approach 1] vs [Approach 2]

**Troubleshooting** (solutions):
- Fixing [Error Type]
- Debugging [Issue]
- Resolving [Problem]
- Common [Feature] Issues

## Demo Tips

For the best demo experience:
1. Start with 5 articles for a quick demo (30-45 seconds)
2. Use 10 for a more impressive show
3. Watch the todo list update in real-time
4. Open the AI Hub after to show the generated content

## Error Handling

If any task fails:
- Continue with other tasks
- Show which succeeded/failed in summary
- Suggest checking logs for failed items

## Important Notes

- This is a demo command - content quality may vary
- All content is marked as AI-generated
- Review content before publishing
- The impressive part is the parallel execution and real-time updates