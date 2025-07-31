# Parallel Content Generator

Run multiple AI content generation agents simultaneously for impressive live demos. Watch as 5-10 articles are created in parallel with real-time progress tracking.

## What It Does

This command creates a powerful demonstration by:

1. **Launching multiple content agents** simultaneously
2. **Showing live progress** for each generation
3. **Creating real content** in your AI Hub
4. **Demonstrating AI scalability** visually

## Prerequisites

You must first install the AI Content Hub:
```bash
/ai-content-hub-agent-builder
```

## Quick Start

```bash
# Generate 5 articles on trending topics
/parallel-content-generator

# Generate 10 specific tutorials
/parallel-content-generator count=10 type=tutorials

# Custom topic list
/parallel-content-generator topics="Puppeteer MCP,GitHub Integration,Security Best Practices"

# Maximum parallel demo
/parallel-content-generator count=20 stagger=true animate=true
```

## Demo Impact

Watch the magic happen:
- **5 agents** start analyzing different topics
- **Progress bars** show real-time generation status
- **Live updates** as content is created
- **Completed articles** appear in your AI Hub
- **Total time**: ~30 seconds for 5 full articles

## Visual Demo

```
🚀 Parallel Content Generator - Starting 5 agents...

[1] Getting Started with Puppeteer MCP     ████████░░ 80% Writing examples...
[2] GitHub MCP Integration Guide           ██████░░░░ 60% Optimizing SEO...
[3] Debugging MCP Connection Issues        █████████░ 90% Final review...
[4] MCP Security Best Practices           ███░░░░░░░ 30% Researching topic...
[5] Puppeteer vs Playwright Comparison    ████████░░ 100% ✓ Complete!

⏱️ Time elapsed: 23s | 📝 Articles completed: 1/5
```

## Features

- **Real-time Progress**: WebSocket updates every 500ms
- **Staggered Start**: Optional dramatic effect
- **Topic Intelligence**: Auto-generates relevant topics
- **Type Focus**: Generate specific content types
- **Error Handling**: Graceful failure recovery
- **Performance Metrics**: Shows generation speed

## Perfect For

- **Live Demos**: Show the power of AI content generation
- **Presentations**: Impressive visual for stakeholders  
- **Testing**: Stress-test your content system
- **Content Sprints**: Quickly populate your AI Hub

## Learn More

See the full [command documentation](command.md) for advanced options and customization.