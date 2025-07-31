# Parallel Content Generator
---
description: >
  Launch multiple AI content generation agents simultaneously for impressive live demos. Creates real content in your AI Hub while showing real-time progress tracking. Perfect for demonstrating the power of AI-assisted content creation at scale.
allowed_tools:
  - Task          # Launch parallel agents
  - Read          # Check AI Hub status
  - Write         # Track generation state
  - Bash          # Start progress server
  - TodoWrite     # Coordinate tasks
  - WebSearch     # Find trending topics
tags:
  - demo
  - content
  - parallel
  - ai-hub
  - showcase
category: demo-tools
---

## Key Features

This command orchestrates an impressive parallel content generation demo:

1. **Parallel Execution** - Run 5-20 content agents simultaneously
2. **Live Progress Tracking** - Real-time updates via TodoWrite
3. **Smart Topic Selection** - Auto-generates relevant topics or uses your list
4. **Visual Impact** - Progress shown through Claude Code's task tracking
5. **Real Content** - Actually creates usable content in your AI Hub

## Arguments

```
/parallel-content-generator
[count=<number>]              # Number of parallel agents (default: 5, max: 20)
[topics=<comma-separated>]    # Specific topics to generate
[type=<content-type>]         # Focus on specific type (tutorials/guides/etc)
```

## What This Command Does

1. **Validates AI Hub** - Ensures the AI Content Hub is installed
2. **Generates Topics** - Creates relevant topic list if not provided
3. **Launches Agents** - Uses Task tool to run multiple content generation agents in parallel
4. **Tracks Progress** - Uses TodoWrite to show real-time progress
5. **Creates Content** - Generates actual content in your AI Hub

## Quick Start

```bash
# Basic demo with 5 agents
/parallel-content-generator

# Generate 10 tutorials
/parallel-content-generator count=10 type=tutorials

# Custom topics
/parallel-content-generator topics="MCP Setup,API Integration,Performance Tips"
```

## Demo Impact

When you run this command, Claude Code will:
- Create a todo list showing all content being generated
- Launch multiple Task agents in parallel
- Each agent updates its todo item as it progresses
- You'll see real-time updates in Claude Code's interface
- Actual content files are created in your AI Hub

## Topic Generation Strategy

When topics aren't provided, the command generates them based on:
- Popular MCP servers (Puppeteer, GitHub, Playwright, etc.)
- Common tasks (automation, testing, deployment)
- Content type templates:
  - Tutorials: "Getting Started with {MCP}"
  - Guides: "Understanding {concept}"
  - Comparisons: "{tool1} vs {tool2}"
  - Troubleshooting: "Fixing {issue}"

## Instructions for Claude Code

When this command is run:

1. First, check that the AI Content Hub exists at `site/src/ai-hub/`. If not, tell the user to run `/ai-content-hub-agent-builder` first.

2. Parse the arguments:
   - `count`: How many articles to generate (default 5, max 20)
   - `topics`: If provided, split by commas. If not, generate topics
   - `type`: If provided, focus on that content type

3. Generate topics if needed:
   - For tutorials: "Getting Started with Puppeteer MCP", "Building Your First Command", etc.
   - For guides: "Understanding MCP Architecture", "Best Practices for Commands", etc.
   - Mix different types if no type specified

4. Create a comprehensive todo list with TodoWrite:
   - One todo item per article to generate
   - Set all to "pending" initially
   - Use descriptive names like "Tutorial: Getting Started with Puppeteer MCP"

5. Launch parallel Task agents:
   - For each topic, use the Task tool to run the ai-content-hub-generator agent
   - Each Task should generate one piece of content
   - Tell each agent to update its todo item as it progresses:
     - "in_progress" when starting
     - Update the content field with status like "Researching topic...", "Writing content...", "Optimizing SEO..."
     - "completed" when done

6. The Task prompt for each agent should be:
   ```
   Use the ai-content-hub-generator agent to create a [type] about "[topic]" in the AI Hub.
   
   As you work:
   1. Update your todo item (ID: [todo-id]) with your progress
   2. Save the final content to site/src/ai-hub/[type]/
   3. Include all required frontmatter (title, description, date, type, ai_generated: true)
   4. Make it comprehensive with examples and practical information
   ```

7. Monitor progress:
   - The TodoWrite updates will show real-time progress
   - When all todos are marked "completed", show a summary

8. Final summary should include:
   - Total articles generated
   - Time taken
   - Location of generated content
   - Suggestion to view the AI Hub

## Example Output

The user will see something like:

```
🚀 Parallel Content Generator
Generating 5 articles for your AI Hub...

✓ Created todo list with 5 content tasks
✓ Launching 5 parallel content generation agents

[The todo list will show real-time updates as each agent progresses]

✅ Content Generation Complete!
   • Generated: 5 articles
   • Location: site/src/ai-hub/
   • View at: http://localhost:8080/ai-hub/
```

## Best Practices

1. **Start Small**: Begin with 5 agents for initial demos
2. **Specific Topics**: Provide topics for targeted content
3. **Type Focus**: Use type parameter for consistent content
4. **Review Content**: AI-generated content should be reviewed

## Prerequisites

- AI Content Hub must be installed first
- The ai-content-hub-generator agent must exist
- Valid site structure with build process