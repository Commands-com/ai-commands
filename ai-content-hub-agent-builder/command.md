# AI Content Hub Agent Builder
---
description: >
  Create a dedicated AI Content Hub for your platform - a separate section for AI-generated tutorials, guides, and resources. Sets up the infrastructure for impressive parallel content generation demos while keeping AI content distinct from your editorial blog.
allowed_tools:
  - Read          # Read existing structure
  - Write         # Create hub structure
  - MultiEdit     # Update navigation
  - Bash          # Run build commands
  - Grep          # Search for integration points
  - LS            # List directories
  - TodoWrite     # Track setup tasks
tags:
  - content
  - ai-hub
  - infrastructure
  - demo
category: content-creation
---

## Key Features

This command creates a dedicated AI content infrastructure that:

1. **Separates AI Content** - Dedicated section distinct from main blog
2. **Enables Parallel Generation** - Infrastructure for multi-agent demos
3. **Maintains Quality** - Structured templates and validation
4. **Preserves SEO Value** - Optimized content in separate namespace
5. **Supports Live Demos** - Real-time progress tracking

## Arguments

```
/ai-content-hub-agent-builder
[section_name=<string>]       # Name for the AI section (default: "ai-hub")
[content_types=<string>]      # Comma-separated content types to support
[enable_search=<true|false>]  # Add search functionality to hub
[visual_style=<cards|list>]   # Display style for content index
[max_agents=<number>]         # Max parallel agents to support (default: 5)
```

## What This Command Does

1. **Creates AI Content Hub Structure**:
   - Separate directory for AI-generated content
   - Category-based organization
   - Index pages with filtering
   - Clear AI-generated labels

2. **Installs Content Generation Agent**:
   - Specialized for technical content
   - Understands platform context (MCP, commands, prompts)
   - Optimized for parallel execution
   - Progress tracking capabilities

3. **Sets Up Demo Infrastructure**:
   - WebSocket support for live updates
   - Progress bars for generation status
   - Parallel execution framework
   - Visual feedback system

## Quick Start

```bash
# Basic installation
/ai-content-hub-agent-builder

# Custom section with specific types
/ai-content-hub-agent-builder section_name="resources" content_types="tutorials,troubleshooting"

# Full demo setup
/ai-content-hub-agent-builder visual_style=cards max_agents=10 enable_search=true
```

## Hub Structure

```
site/src/
├── blog/                    # Your editorial blog (unchanged)
├── ai-hub/                  # New AI content section
│   ├── index.njk           # Hub homepage with categories
│   ├── tutorials/          # Step-by-step guides
│   │   ├── index.njk      # Tutorial index
│   │   └── *.md           # Individual tutorials
│   ├── guides/            # How-to guides
│   ├── comparisons/       # Tool/feature comparisons
│   ├── troubleshooting/   # Problem-solving guides
│   └── _includes/
│       ├── ai-header.njk  # "AI-Generated" banner
│       └── hub-nav.njk    # Hub navigation
```

## Content Types

### Tutorials
Step-by-step guides for specific tasks:
- "Getting Started with Puppeteer MCP"
- "Building Your First Command"
- "Integrating Multiple MCP Servers"

### Guides
Conceptual and best practice content:
- "Understanding MCP Architecture"
- "Security Best Practices for Commands"
- "Optimizing Command Performance"

### Comparisons
Side-by-side analysis:
- "Puppeteer vs Playwright MCP"
- "Local vs Cloud MCP Servers"
- "Command Categories Explained"

### Troubleshooting
Problem-solving resources:
- "Common MCP Connection Issues"
- "Debugging Command Failures"
- "Performance Optimization Tips"

## Visual Styles

### Card Layout
```html
<div class="ai-content-grid">
  <article class="ai-content-card">
    <div class="ai-badge">AI Generated</div>
    <h3>Getting Started with Puppeteer MCP</h3>
    <p>Learn how to automate browser tasks...</p>
    <div class="ai-meta">
      <span>Tutorial</span>
      <span>5 min read</span>
    </div>
  </article>
</div>
```

### List Layout
```html
<div class="ai-content-list">
  <article class="ai-content-item">
    <span class="ai-indicator">🤖</span>
    <div class="ai-content-details">
      <h3>Getting Started with Puppeteer MCP</h3>
      <p>Tutorial • 5 min read • AI Generated</p>
    </div>
  </article>
</div>
```

## Implementation

### Step 1: Create Hub Structure

```javascript
const createAIHub = async (config) => {
  const { sectionName = 'ai-hub', contentTypes = ['tutorials', 'guides', 'comparisons', 'troubleshooting'] } = config;
  
  console.log(`Creating AI Content Hub: ${sectionName}`);
  
  // Create directory structure
  const hubPath = `site/src/${sectionName}`;
  await bash(`mkdir -p ${hubPath}`);
  
  // Create directories for each content type
  for (const type of contentTypes) {
    await bash(`mkdir -p ${hubPath}/${type}`);
    
    // Create index page for each type
    const indexContent = `---
layout: ai-hub-layout.njk
title: AI-Generated ${type.charAt(0).toUpperCase() + type.slice(1)}
section: ${type}
pagination:
  data: collections.${sectionName}${type}
  size: 12
  alias: items
---

<div class="ai-hub-header">
  <h1>{{ title }}</h1>
  <div class="ai-notice">
    🤖 This content is AI-generated to help you learn quickly
  </div>
</div>

<div class="ai-content-${config.visualStyle || 'cards'}">
  {% for item in items %}
    {% include "ai-content-item.njk" %}
  {% endfor %}
</div>

{% include "pagination.njk" %}
`;
    
    await write(`${hubPath}/${type}/index.njk`, indexContent);
  }
  
  // Create main hub index
  const hubIndex = `---
layout: base-layout.njk
title: AI Content Hub
description: AI-generated tutorials, guides, and resources for Commands.com
---

<div class="ai-hub-hero">
  <h1>AI Content Hub</h1>
  <p class="lead">Instantly generated tutorials and guides to help you get started</p>
  <div class="ai-disclaimer">
    <strong>🤖 Note:</strong> All content in this section is AI-generated. 
    For editorial content, visit our <a href="/blog/">main blog</a>.
  </div>
</div>

<div class="ai-hub-categories">
  ${contentTypes.map(type => `
  <div class="category-card">
    <h2>${type.charAt(0).toUpperCase() + type.slice(1)}</h2>
    <p>${getCategoryDescription(type)}</p>
    <a href="/${sectionName}/${type}/" class="btn btn-primary">
      Browse ${type}
    </a>
  </div>
  `).join('')}
</div>

<div class="ai-hub-demo" id="demo-container">
  <!-- Parallel generation demo will appear here -->
</div>
`;
  
  await write(`${hubPath}/index.njk`, hubIndex);
};

const getCategoryDescription = (type) => {
  const descriptions = {
    tutorials: 'Step-by-step guides for specific tasks and integrations',
    guides: 'Conceptual overviews and best practices',
    comparisons: 'Side-by-side analysis of tools and approaches',
    troubleshooting: 'Solutions to common problems and issues'
  };
  return descriptions[type] || 'AI-generated content';
};
```

### Step 2: Create AI Hub Layout

```javascript
const createHubLayout = async () => {
  const layoutContent = `---
layout: base-layout.njk
---

<div class="ai-hub-container">
  <nav class="ai-hub-nav">
    <a href="/ai-hub/" class="hub-home">AI Hub Home</a>
    <div class="hub-sections">
      <a href="/ai-hub/tutorials/">Tutorials</a>
      <a href="/ai-hub/guides/">Guides</a>
      <a href="/ai-hub/comparisons/">Comparisons</a>
      <a href="/ai-hub/troubleshooting/">Troubleshooting</a>
    </div>
  </nav>
  
  <div class="ai-generated-banner">
    <span class="ai-icon">🤖</span>
    <span>This content is AI-generated</span>
  </div>
  
  <main class="ai-hub-content">
    {{ content | safe }}
  </main>
</div>

<style>
  .ai-hub-container {
    max-width: 1200px;
    margin: 0 auto;
    padding: 2rem;
  }
  
  .ai-generated-banner {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    padding: 0.75rem 1.5rem;
    border-radius: 0.5rem;
    display: inline-flex;
    align-items: center;
    gap: 0.5rem;
    margin-bottom: 2rem;
  }
  
  .ai-hub-nav {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 2rem;
    padding-bottom: 1rem;
    border-bottom: 2px solid #e5e7eb;
  }
  
  .hub-sections {
    display: flex;
    gap: 2rem;
  }
  
  .hub-sections a {
    color: #6b7280;
    text-decoration: none;
    font-weight: 500;
    transition: color 0.2s;
  }
  
  .hub-sections a:hover {
    color: #667eea;
  }
  
  /* Card layout styles */
  .ai-content-cards {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
    gap: 2rem;
  }
  
  .ai-content-card {
    background: white;
    border: 1px solid #e5e7eb;
    border-radius: 0.5rem;
    padding: 1.5rem;
    transition: transform 0.2s, box-shadow 0.2s;
    position: relative;
  }
  
  .ai-content-card:hover {
    transform: translateY(-2px);
    box-shadow: 0 4px 6px -1px rgb(0 0 0 / 0.1);
  }
  
  .ai-badge {
    position: absolute;
    top: 1rem;
    right: 1rem;
    background: #667eea;
    color: white;
    padding: 0.25rem 0.75rem;
    border-radius: 9999px;
    font-size: 0.75rem;
    font-weight: 600;
  }
</style>
`;

  await write('site/src/_includes/ai-hub-layout.njk', layoutContent);
};
```

### Step 3: Create Content Generation Agent

```javascript
const createContentAgent = async (config) => {
  const agentContent = `---
name: ai-content-hub-generator
description: Generates technical content for the AI Content Hub with progress tracking for parallel execution
tools:
  - Read
  - Write
  - WebSearch
  - WebFetch
  - TodoWrite
---

You are an AI content generator for the Commands.com AI Content Hub. Your role is to create helpful technical content that complements the main editorial blog while being clearly marked as AI-generated.

## Content Guidelines

1. **Always Technical**: Focus on practical, how-to content
2. **Platform Specific**: Reference Commands.com features (MCP servers, commands, prompts)
3. **Clear Structure**: Use consistent formatting and sections
4. **Code Examples**: Include working examples when relevant
5. **No Opinion**: Stick to facts and instructions, avoid editorial voice

## Content Templates

### Tutorial Template
\`\`\`markdown
---
title: "[Task]: A Step-by-Step Tutorial"
description: "Learn how to [accomplish task] with detailed instructions"
date: YYYY-MM-DD
type: tutorial
category: [relevant-category]
difficulty: [beginner|intermediate|advanced]
time_to_complete: "X minutes"
ai_generated: true
---

## What You'll Learn

In this tutorial, you'll learn how to:
- [Learning objective 1]
- [Learning objective 2]
- [Learning objective 3]

## Prerequisites

Before starting, make sure you have:
- [Prerequisite 1]
- [Prerequisite 2]

## Step 1: [First Step]

[Clear instructions with code example if relevant]

\`\`\`javascript
// Example code
\`\`\`

## Step 2: [Second Step]

[Continue with clear steps...]

## Troubleshooting

Common issues and solutions:
- **Problem**: [Description]
  **Solution**: [Fix]

## Next Steps

- Try [related tutorial]
- Explore [related feature]
- Read [related guide]
\`\`\`

### Guide Template
\`\`\`markdown
---
title: "Understanding [Topic]: A Comprehensive Guide"
description: "Everything you need to know about [topic]"
date: YYYY-MM-DD
type: guide
category: [relevant-category]
ai_generated: true
---

## Overview

[Brief introduction to the topic]

## Key Concepts

### [Concept 1]
[Explanation with examples]

### [Concept 2]
[Explanation with examples]

## Best Practices

1. **[Practice 1]**: [Explanation]
2. **[Practice 2]**: [Explanation]

## Common Use Cases

- **[Use Case 1]**: [Description]
- **[Use Case 2]**: [Description]

## Related Resources

- [Link to related content]
- [Link to documentation]
\`\`\`

## Progress Tracking

When generating content, emit progress updates for the parallel execution system:

\`\`\`javascript
// Progress format
{
  "id": "unique-task-id",
  "status": "generating", // "pending", "generating", "reviewing", "complete", "error"
  "progress": 45, // 0-100
  "title": "Getting Started with Puppeteer MCP",
  "type": "tutorial",
  "message": "Generating code examples..."
}
\`\`\`

## Parallel Execution Support

You will often be run in parallel with other instances. To support this:

1. Use unique IDs for all generated content
2. Emit regular progress updates
3. Handle resource conflicts gracefully
4. Keep memory usage reasonable

## Quality Checks

Before marking content as complete:
- ✓ Title is clear and descriptive
- ✓ All code examples are syntactically correct
- ✓ Links are properly formatted
- ✓ Metadata is complete
- ✓ Content meets minimum length requirements
- ✓ No placeholder text remains

Remember: You're creating supplementary educational content, not editorial pieces. Keep it factual, helpful, and clearly AI-generated.
`;

  await write('.claude/agents/ai-content-hub-generator.md', agentContent);
};
```

### Step 4: Add Progress Tracking System

```javascript
const setupProgressTracking = async () => {
  // Create WebSocket server for live updates
  const wsServerCode = `
const WebSocket = require('ws');
const wss = new WebSocket.Server({ port: 8088 });

const activeGenerations = new Map();

wss.on('connection', (ws) => {
  // Send current state to new connections
  ws.send(JSON.stringify({
    type: 'init',
    generations: Array.from(activeGenerations.values())
  }));
  
  ws.on('message', (message) => {
    const data = JSON.parse(message);
    
    if (data.type === 'progress') {
      activeGenerations.set(data.id, data);
      
      // Broadcast to all clients
      wss.clients.forEach((client) => {
        if (client.readyState === WebSocket.OPEN) {
          client.send(JSON.stringify(data));
        }
      });
    }
  });
});

console.log('Progress tracking server running on ws://localhost:8088');
`;

  await write('.claude/ai-hub/progress-server.js', wsServerCode);
  
  // Create client-side progress tracker
  const clientCode = `
class AIContentProgress {
  constructor() {
    this.ws = new WebSocket('ws://localhost:8088');
    this.container = document.getElementById('demo-container');
    this.generations = new Map();
    
    this.ws.onmessage = (event) => {
      const data = JSON.parse(event.data);
      
      if (data.type === 'init') {
        data.generations.forEach(g => this.updateGeneration(g));
      } else {
        this.updateGeneration(data);
      }
    };
  }
  
  updateGeneration(data) {
    this.generations.set(data.id, data);
    this.render();
  }
  
  render() {
    const html = Array.from(this.generations.values()).map(gen => \`
      <div class="generation-item \${gen.status}">
        <div class="generation-header">
          <span class="generation-title">\${gen.title}</span>
          <span class="generation-type">\${gen.type}</span>
        </div>
        <div class="generation-progress">
          <div class="progress-bar">
            <div class="progress-fill" style="width: \${gen.progress}%"></div>
          </div>
          <span class="progress-text">\${gen.progress}%</span>
        </div>
        <div class="generation-status">\${gen.message}</div>
      </div>
    \`).join('');
    
    this.container.innerHTML = \`
      <h2>Live Content Generation</h2>
      <div class="generations-list">\${html}</div>
    \`;
  }
}

// Initialize on page load
if (document.getElementById('demo-container')) {
  new AIContentProgress();
}
`;

  await write('site/src/assets/js/ai-progress.js', clientCode);
};
```

### Step 5: Update Navigation

```javascript
const updateNavigation = async () => {
  const navPath = 'site/src/_includes/components/nav.njk';
  const navContent = await read(navPath);
  
  if (!navContent.includes('AI Hub')) {
    const aiHubLink = `
            <li>
              <a href="/ai-hub/" class="text-gray-700 dark:text-gray-300 hover:text-blue-600 dark:hover:text-blue-400 px-3 py-2 rounded-md text-sm font-medium">
                AI Hub <span class="text-xs text-purple-600">New</span>
              </a>
            </li>`;
    
    // Add after Blog link
    const updatedNav = navContent.replace(
      /<a href="\/blog\/".+?<\/a>\s*<\/li>/s,
      (match) => match + aiHubLink
    );
    
    await write(navPath, updatedNav);
  }
};
```

### Step 6: Main Installation

```javascript
const main = async () => {
  console.log('🤖 AI Content Hub Agent Builder');
  console.log('Creating a dedicated space for AI-generated content\n');
  
  const config = {
    sectionName: args.section_name || 'ai-hub',
    contentTypes: args.content_types ? args.content_types.split(',') : ['tutorials', 'guides', 'comparisons', 'troubleshooting'],
    enableSearch: args.enable_search !== 'false',
    visualStyle: args.visual_style || 'cards',
    maxAgents: parseInt(args.max_agents) || 5
  };
  
  // Create AI Hub structure
  console.log('Creating AI Content Hub structure...');
  await createAIHub(config);
  console.log('✓ Hub structure created');
  
  // Create layouts and includes
  console.log('\nSetting up layouts and components...');
  await createHubLayout();
  console.log('✓ Layouts created');
  
  // Create content generation agent
  console.log('\nInstalling content generation agent...');
  await bash('mkdir -p .claude/agents .claude/ai-hub');
  await createContentAgent(config);
  console.log('✓ Content agent installed');
  
  // Set up progress tracking
  console.log('\nSetting up progress tracking for demos...');
  await setupProgressTracking();
  console.log('✓ Progress tracking ready');
  
  // Update navigation
  console.log('\nUpdating site navigation...');
  await updateNavigation();
  console.log('✓ Navigation updated');
  
  // Add styles
  console.log('\nAdding AI Hub styles...');
  const stylesAddition = `
/* AI Content Hub Styles */
.ai-hub-hero {
  text-align: center;
  padding: 4rem 2rem;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  border-radius: 1rem;
  margin-bottom: 3rem;
}

.ai-disclaimer {
  background: rgba(255, 255, 255, 0.2);
  padding: 1rem;
  border-radius: 0.5rem;
  margin-top: 2rem;
  backdrop-filter: blur(10px);
}

.ai-hub-categories {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 2rem;
  margin-bottom: 4rem;
}

.category-card {
  background: white;
  border: 2px solid #e5e7eb;
  border-radius: 0.75rem;
  padding: 2rem;
  text-align: center;
  transition: all 0.3s;
}

.category-card:hover {
  border-color: #667eea;
  transform: translateY(-4px);
  box-shadow: 0 10px 25px -5px rgba(102, 126, 234, 0.2);
}

/* Progress tracking styles */
.generation-item {
  background: white;
  border: 1px solid #e5e7eb;
  border-radius: 0.5rem;
  padding: 1.5rem;
  margin-bottom: 1rem;
  transition: all 0.3s;
}

.generation-item.complete {
  border-color: #10b981;
  background: #f0fdf4;
}

.progress-bar {
  background: #e5e7eb;
  height: 8px;
  border-radius: 4px;
  overflow: hidden;
  margin: 0.5rem 0;
}

.progress-fill {
  background: linear-gradient(90deg, #667eea, #764ba2);
  height: 100%;
  transition: width 0.3s ease;
}
`;
  
  const mainCss = await read('site/src/assets/css/main.css');
  await write('site/src/assets/css/main.css', mainCss + '\n' + stylesAddition);
  
  console.log('✓ Styles added');
  
  // Update CLAUDE.md
  console.log('\nUpdating CLAUDE.md...');
  const claudeMdAddition = `
## AI Content Hub

This project has an AI Content Hub installed for generating supplementary technical content. The hub is separate from the main blog and clearly marks all content as AI-generated.

### Using the AI Content Hub

1. **Generate individual content**: "Create a tutorial about Puppeteer MCP in the AI Hub"
2. **Batch generation**: Use \`/parallel-content-generator\` for impressive demos
3. **Content types**: tutorials, guides, comparisons, troubleshooting

### Hub Structure
- Location: \`site/src/${config.sectionName}/\`
- Agent: \`.claude/agents/ai-content-hub-generator.md\`
- Progress tracking: Port 8088 (WebSocket)

The AI Hub provides valuable technical content while preserving the editorial integrity of the main blog.
`;
  
  const claudeMd = await read('CLAUDE.md');
  await write('CLAUDE.md', claudeMd + claudeMdAddition);
  
  // Create sample content for demo
  console.log('\nCreating sample content for demo...');
  const sampleTutorial = `---
title: "Getting Started with Puppeteer MCP: A Step-by-Step Tutorial"
description: "Learn how to set up and use the Puppeteer MCP server for browser automation"
date: ${new Date().toISOString().split('T')[0]}
type: tutorial
category: mcp-servers
difficulty: beginner
time_to_complete: "15 minutes"
ai_generated: true
---

## What You'll Learn

In this tutorial, you'll learn how to:
- Install and configure the Puppeteer MCP server
- Create your first browser automation script
- Handle common automation scenarios
- Debug and troubleshoot issues

## Prerequisites

Before starting, make sure you have:
- Node.js installed (version 14 or higher)
- Basic JavaScript knowledge
- Claude Desktop or compatible MCP client

## Step 1: Install Puppeteer MCP

First, install the Puppeteer MCP server:

\`\`\`bash
npm install -g @modelcontextprotocol/server-puppeteer
\`\`\`

## Step 2: Configure Your MCP Client

Add the Puppeteer server to your MCP configuration...

[Content continues...]
`;
  
  await write(`site/src/${config.sectionName}/tutorials/puppeteer-mcp-tutorial.md`, sampleTutorial);
  
  console.log('\n✅ AI Content Hub Successfully Installed!\n');
  console.log(`Hub location: /site/src/${config.sectionName}/`);
  console.log(`View at: http://localhost:8080/${config.sectionName}/\n`);
  
  console.log('Next steps:');
  console.log('1. Run your build process to see the AI Hub');
  console.log('2. Install /parallel-content-generator for powerful demos');
  console.log('3. Ask Claude to generate content for the AI Hub');
  
  console.log('\nExample usage:');
  console.log('  "Create a tutorial about GitHub MCP in the AI Hub"');
  console.log('  "Generate a troubleshooting guide for common MCP errors"');
};

// Execute
main().catch(console.error);
```

## Troubleshooting

### Build Process
The AI Hub integrates with your existing build process. Run your normal build command to see changes.

### WebSocket Connection
The progress tracking requires WebSocket on port 8088. Ensure this port is available.

### Content Generation
AI-generated content is clearly marked and separated from editorial content. Always review before publishing.

## Security Notes

- AI content is clearly labeled to maintain transparency
- No automatic publishing - all content starts as drafts
- WebSocket server is local-only for demos
- Content validation prevents XSS and injection attacks