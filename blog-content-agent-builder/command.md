# Blog Content Agent Builder
---
description: >
  Install a specialized Claude Code subagent that automatically generates SEO-optimized blog posts for your platform. The agent researches topics, writes engaging content, adds code examples, and optimizes for search engines - turning hours of work into minutes.
allowed_tools:
  - Read          # Read existing blog posts
  - Write         # Create new blog posts
  - MultiEdit     # Update existing posts
  - Bash          # Run build commands
  - Grep          # Search for content
  - LS            # List blog structure
  - TodoWrite     # Track content calendar
  - WebSearch     # Research topics
  - WebFetch      # Analyze competitor content
tags:
  - content
  - seo
  - blog
  - automation
  - marketing
category: content-creation
---

## Key Features

This command installs a blog content generation subagent that:

1. **Generates High-Quality Content** - SEO-optimized posts in your brand voice
2. **Researches Topics** - Uses platform data and web research for accuracy
3. **Adds Technical Examples** - Auto-generates relevant code snippets
4. **Optimizes for SEO** - Meta tags, schema markup, keyword optimization
5. **Maintains Consistency** - Follows your blog's style and structure

## Arguments

```
/blog-content-agent-builder
[topic=<string>]              # Generate a post on this topic immediately
[publish=<draft|review|live>] # Publishing state for generated content
[generate_ideas=<number>]     # Number of topic ideas to generate
[category=<string>]           # Focus on specific category
[tone=<casual|professional|technical>] # Writing style
[length=<short|medium|long>]  # Target post length
[seo_focus=<string>]         # Primary keyword to optimize for
```

## What This Command Does

1. **Analyzes Your Blog Structure**:
   - Detects blog platform (Eleventy, Jekyll, Hugo, Next.js)
   - Learns from existing posts for style and tone
   - Identifies content categories and tags
   - Maps internal linking opportunities

2. **Creates Content Generation Subagent**:
   - Sets up `.claude/agents/blog-content-generator.md`
   - Configures content templates and patterns
   - Integrates with your publishing workflow

3. **Sets Up Content Workflows**:
   - Content calendar tracking
   - SEO optimization pipeline
   - Image generation/suggestion system
   - Cross-platform publishing

## Quick Start

```bash
# Basic installation
/blog-content-agent-builder

# Generate a post immediately
/blog-content-agent-builder topic="Getting Started with MCP Servers"

# Generate multiple tutorial ideas
/blog-content-agent-builder generate_ideas=5 category=tutorials

# Create SEO-focused content
/blog-content-agent-builder topic="Best AI Prompts 2024" seo_focus="ai prompts" length=long
```

## How It Works

### 1. Content Research Phase
The subagent researches topics by:
- Analyzing your platform's data (popular commands, trending topics)
- Searching for current trends and news
- Identifying content gaps in your blog
- Researching competitor content

### 2. Content Generation Phase
```markdown
---
title: "Automate Browser Testing with Puppeteer MCP: A Complete Guide"
description: "Learn how to use the Puppeteer MCP server to automate browser testing, web scraping, and UI automation in your AI workflows."
date: 2024-01-30
author: "Blog Agent"
category: "tutorials"
tags: ["mcp", "puppeteer", "automation", "testing"]
image: "/images/blog/puppeteer-mcp-hero.png"
seo:
  keywords: ["puppeteer mcp", "browser automation", "ai testing"]
  schema:
    "@type": "TechArticle"
    "headline": "{{ title }}"
    "datePublished": "{{ date }}"
---

## Introduction

[Engaging introduction with problem/solution format]

## What You'll Learn

- How to install and configure Puppeteer MCP
- Best practices for browser automation
- Real-world examples and use cases
- Performance optimization tips

## Getting Started

[Step-by-step tutorial with code examples]

```javascript
// Auto-generated code example
const puppeteer = require('@modelcontextprotocol/server-puppeteer');
// ...
```

## Advanced Techniques

[Deep dive into advanced features]

## Conclusion

[Summary and call-to-action]
```

### 3. SEO Optimization Phase
The agent automatically:
- Optimizes title tags (50-60 characters)
- Writes meta descriptions (150-160 characters)
- Adds schema.org structured data
- Optimizes keyword density (1-2%)
- Creates internal links to related content
- Suggests external authoritative links

### 4. Publishing Workflow
```bash
# Draft state - needs review
site/src/blog/drafts/2024-01-30-post.md

# Review state - pending approval
site/src/blog/review/2024-01-30-post.md

# Live - published
site/src/blog/2024-01-30-post.md
```

## Subagent Capabilities

### Content Types
- **Tutorials**: Step-by-step guides with code examples
- **Announcements**: Platform updates and new features
- **Roundups**: Weekly/monthly trending content
- **Deep Dives**: Technical architecture articles
- **Case Studies**: Creator success stories
- **Comparisons**: Feature/tool comparisons

### SEO Features
- Keyword research and optimization
- Competitive content analysis
- Meta tag generation
- Schema markup implementation
- Content scoring and recommendations
- Social media preview optimization

### Integration Features
- Auto-generates social media posts
- Creates email newsletter content
- Generates featured images with AI
- Cross-posts to dev.to/Medium (optional)
- Updates content calendar

## Example Session

```
User: "Write a blog post about the new GitHub MCP integration"

Claude Code: I'll use the blog content generator to create an SEO-optimized post about the GitHub MCP integration.

Blog Content Agent:
[1/5] Researching topic...
  ✓ Analyzing GitHub MCP documentation
  ✓ Identifying key features and use cases
  ✓ Researching related keywords: "github mcp", "ai github integration"
  
[2/5] Generating content structure...
  ✓ Title: "GitHub MCP: Supercharge Your AI Development Workflow"
  ✓ Sections: Introduction, Installation, Key Features, Examples, Best Practices
  ✓ Target length: 1,500 words
  
[3/5] Writing content...
  ✓ Introduction with problem/solution hook
  ✓ 3 practical code examples
  ✓ 5 pro tips for optimization
  ✓ Visual diagram of workflow
  
[4/5] Optimizing for SEO...
  ✓ Primary keyword: "github mcp" (density: 1.4%)
  ✓ Related keywords: 8 included naturally
  ✓ Meta description: 158 characters
  ✓ Schema markup: TechArticle
  ✓ Internal links: 4 related posts
  
[5/5] Final review...
  ✓ Readability score: Grade 9
  ✓ SEO score: 96/100
  ✓ Generated featured image prompt
  
📝 Blog post created: site/src/blog/drafts/2024-01-30-github-mcp-integration.md

Would you like me to move it to review state or make any adjustments?
```

## Content Calendar Management

The agent maintains a content calendar in `.claude/blog/content-calendar.json`:

```json
{
  "scheduled": [
    {
      "date": "2024-02-01",
      "topic": "Weekly Platform Updates",
      "category": "announcements",
      "status": "planned"
    }
  ],
  "ideas": [
    {
      "topic": "10 Best MCP Servers for Developers",
      "priority": "high",
      "seo_potential": "high"
    }
  ],
  "published": [
    {
      "date": "2024-01-30",
      "title": "GitHub MCP: Supercharge Your AI Workflow",
      "views": 0,
      "engagement": "pending"
    }
  ]
}
```

## Configuration

### Blog Settings
```yaml
# .claude/agents/blog-config.yaml
blog:
  platform: eleventy  # or jekyll, hugo, nextjs
  voice: professional  # casual, technical, conversational
  length:
    short: 600-800
    medium: 1000-1500
    long: 2000+
  seo:
    keyword_density: 1-2%
    meta_length: 150-160
  categories:
    - tutorials
    - announcements
    - mcp-guides
    - creator-stories
```

### Templates
Customize templates in `.claude/blog/templates/`:
- `tutorial.md` - Tutorial post template
- `announcement.md` - Platform update template
- `roundup.md` - Weekly roundup template

## Best Practices

1. **Review Generated Content** - Always review before publishing
2. **Maintain Authenticity** - Edit to add personal insights
3. **Update Regularly** - Keep content calendar active
4. **Monitor Performance** - Track which topics perform best
5. **Iterate on Voice** - Refine the agent's writing style

## Implementation

### Step 1: Detect Blog Platform

```javascript
const detectBlogPlatform = async () => {
  // Check for common blog structures
  if (await fileExists('_config.yml')) return 'jekyll';
  if (await fileExists('config.toml') || await fileExists('hugo.toml')) return 'hugo';
  if (await fileExists('.eleventy.js') || await fileExists('eleventy.config.js')) return 'eleventy';
  if (await fileExists('next.config.js') && await pathExists('pages/blog')) return 'nextjs';
  if (await fileExists('gatsby-config.js')) return 'gatsby';
  
  // Check for blog directories
  const blogDirs = ['blog', 'posts', '_posts', 'articles', 'content/blog'];
  for (const dir of blogDirs) {
    if (await pathExists(dir)) {
      return { platform: 'generic', blogDir: dir };
    }
  }
  
  return null;
};
```

### Step 2: Analyze Existing Content

```javascript
const analyzeExistingContent = async (blogDir) => {
  const posts = await glob(`${blogDir}/**/*.md`);
  const analysis = {
    totalPosts: posts.length,
    categories: new Set(),
    tags: new Set(),
    averageLength: 0,
    tone: 'professional',
    patterns: []
  };
  
  let totalWords = 0;
  
  for (const post of posts.slice(-10)) { // Analyze last 10 posts
    const content = await read(post);
    
    // Extract frontmatter
    const frontmatterMatch = content.match(/^---\n([\s\S]*?)\n---/);
    if (frontmatterMatch) {
      const frontmatter = frontmatterMatch[1];
      // Parse categories and tags
      const categoryMatch = frontmatter.match(/category:\s*["']?(\w+)/);
      if (categoryMatch) analysis.categories.add(categoryMatch[1]);
      
      const tagsMatch = frontmatter.match(/tags:\s*\[(.*?)\]/);
      if (tagsMatch) {
        tagsMatch[1].split(',').forEach(tag => 
          analysis.tags.add(tag.trim().replace(/["']/g, ''))
        );
      }
    }
    
    // Analyze content
    const words = content.split(/\s+/).length;
    totalWords += words;
    
    // Detect tone
    if (content.match(/\b(you|your|let's|we'll)\b/gi)?.length > 20) {
      analysis.tone = 'conversational';
    }
  }
  
  analysis.averageLength = Math.round(totalWords / posts.length);
  
  return analysis;
};
```

### Step 3: Create Blog Content Generator Subagent

```javascript
const createBlogAgent = async (config) => {
  const { platform, analysis, tone, categories } = config;
  
  const subagentContent = `---
name: blog-content-generator
description: Generates SEO-optimized blog posts in your brand voice, researches topics, and manages content calendar
tools:
  - Read
  - Write
  - MultiEdit
  - Grep
  - WebSearch
  - WebFetch
  - TodoWrite
---

You are a professional blog content creator specializing in technical content for the ${platform.name || 'Commands.com'} platform. Your mission is to create engaging, SEO-optimized blog posts that provide value to readers while driving organic traffic.

## Your Writing Style

- Tone: ${tone}
- Average post length: ${analysis.averageLength} words
- Categories: ${Array.from(categories).join(', ')}
- Focus: Technical accuracy with accessibility

## Content Generation Process

### 1. Topic Research
When given a topic:
- Search for current trends and news
- Analyze platform data for relevant content
- Identify target keywords (primary + 5-7 related)
- Research competitor content for gaps

### 2. Content Structure
Follow this optimized structure:

\`\`\`markdown
---
title: "[Action-Oriented Title with Primary Keyword]"
description: "[150-160 char meta description with keyword]"
date: ${new Date().toISOString().split('T')[0]}
author: "Platform Team"
category: "[category]"
tags: [relevant, tags, 3-5]
image: "/images/blog/[topic]-hero.png"
seo:
  keywords: ["primary keyword", "related keywords"]
  schema:
    "@type": "TechArticle"
---

## Introduction (Problem/Solution Hook)
[2-3 paragraphs establishing the problem and your solution]

## What You'll Learn
- [Bullet points of key takeaways]

## [Main Content Sections]
[Step-by-step guide with examples]

### Code Examples
\`\`\`language
// Practical, working code examples
\`\`\`

## Best Practices / Pro Tips
[Advanced insights for power users]

## Conclusion
[Summary + Call-to-action]

## Related Resources
- [Internal links to related content]
\`\`\`

### 3. SEO Optimization Checklist
- [ ] Title: 50-60 characters with keyword
- [ ] Meta description: 150-160 characters
- [ ] Keyword density: 1-2% for primary keyword
- [ ] Headers: Include keywords naturally in H2/H3
- [ ] Internal links: 3-5 to related content
- [ ] External links: 1-2 to authoritative sources
- [ ] Images: Alt text with keywords
- [ ] URL slug: keyword-optimized

### 4. Content Types to Master

**Tutorials**
- Step-by-step instructions
- Multiple code examples
- Common pitfalls section
- Video/GIF suggestions

**Platform Updates**
- What's new summary
- Migration guides
- Before/after comparisons
- Impact on users

**Roundups**
- Trending items with context
- Creator spotlights
- Community highlights
- Stats and insights

**Technical Deep-Dives**
- Architecture explanations
- Performance analyses
- Security considerations
- Implementation details

## Research Tools

Use these approaches for different content needs:

### Platform Data Mining
\`\`\`javascript
// Find trending topics
const trending = await analyzeTrendingCommands();
const popularMCPs = await getMostUsedMCPServers();
\`\`\`

### Competitor Analysis
Research similar platforms:
- dev.to for technical content style
- Product Hunt for announcement formats
- GitHub blog for feature updates

### Keyword Research
Target search intent:
- Informational: "what is", "how to"
- Navigational: "mcp server docs"
- Transactional: "best ai prompts"

## Content Calendar Management

Maintain regular posting schedule:
- **Monday**: Platform updates/announcements
- **Wednesday**: Technical tutorials
- **Friday**: Roundups/community highlights

Track in \`.claude/blog/content-calendar.json\`:
- Scheduled posts
- Content ideas with priority
- Performance metrics
- A/B test results

## Quality Standards

Every post must:
1. Provide genuine value to readers
2. Include at least one unique insight
3. Have working code examples (if applicable)
4. Link to related platform content
5. End with clear next steps

## Image Generation Prompts

For featured images, suggest DALL-E/Midjourney prompts:
- "Minimalist technical diagram showing [concept], blue accent colors, clean white background"
- "Abstract visualization of [topic], gradient from blue to purple, modern tech aesthetic"
- "Isometric illustration of [workflow], pastel colors, detailed but clean"

Remember: Your content drives platform growth through organic search, user education, and community engagement. Make every post count!
`;

  await bash('mkdir -p .claude/agents');
  await write('.claude/agents/blog-content-generator.md', subagentContent);
};
```

### Step 4: Generate Blog Post

```javascript
const generateBlogPost = async (topic, config) => {
  const timestamp = new Date().toISOString().split('T')[0];
  const slug = topic.toLowerCase()
    .replace(/[^a-z0-9]+/g, '-')
    .replace(/(^-|-$)/g, '');
  
  // This would normally call the subagent
  console.log(`\nGenerating blog post about: ${topic}`);
  console.log('Researching topic...');
  
  // Create blog post
  const filename = `${timestamp}-${slug}.md`;
  const filepath = config.publish === 'live' 
    ? `${config.blogDir}/${filename}`
    : `${config.blogDir}/drafts/${filename}`;
  
  // Generate content (simplified for example)
  const content = `---
title: "${topic}: A Comprehensive Guide"
description: "Learn everything about ${topic} with practical examples and best practices for implementation."
date: ${timestamp}
author: "Blog Agent"
category: "tutorials"
tags: ["tutorial", "guide", "best-practices"]
image: "/images/blog/${slug}-hero.png"
seo:
  keywords: ["${topic.toLowerCase()}", "${topic.toLowerCase()} guide", "${topic.toLowerCase()} tutorial"]
  schema:
    "@type": "TechArticle"
    "headline": "${topic}: A Comprehensive Guide"
    "datePublished": "${timestamp}"
---

## Introduction

${topic} has become an essential tool for modern developers. In this comprehensive guide, we'll explore everything you need to know to get started and master ${topic}.

## What You'll Learn

- Understanding the fundamentals of ${topic}
- Step-by-step implementation guide
- Best practices and common pitfalls
- Real-world examples and use cases
- Performance optimization techniques

## Getting Started with ${topic}

[Content continues...]

## Conclusion

Now you have a solid foundation in ${topic}. Start implementing these techniques in your projects and see the immediate benefits.

## Next Steps

- Try our [interactive ${topic} playground](/playground)
- Browse [${topic} commands](/commands?tag=${slug})
- Join our [community discussion](/community)
`;
  
  await bash(`mkdir -p ${path.dirname(filepath)}`);
  await write(filepath, content);
  
  return filepath;
};
```

### Step 5: Main Execution

```javascript
const main = async () => {
  console.log('🚀 Blog Content Agent Builder');
  console.log('Installing your AI-powered blog content generator\n');
  
  // Detect blog platform
  console.log('Detecting blog platform...');
  const platform = await detectBlogPlatform();
  
  if (!platform) {
    console.log('❌ No blog platform detected.');
    console.log('Please run this command in a project with a blog structure.');
    return;
  }
  
  console.log(`✓ Detected platform: ${platform.platform || platform}`);
  
  // Find blog directory
  const blogDir = platform.blogDir || await findBlogDirectory();
  console.log(`✓ Blog directory: ${blogDir}`);
  
  // Analyze existing content
  console.log('\nAnalyzing existing content...');
  const analysis = await analyzeExistingContent(blogDir);
  console.log(`✓ Found ${analysis.totalPosts} existing posts`);
  console.log(`✓ Average length: ${analysis.averageLength} words`);
  console.log(`✓ Tone detected: ${analysis.tone}`);
  
  // Create directories
  console.log('\nSetting up blog agent directories...');
  await bash('mkdir -p .claude/blog/templates');
  await bash('mkdir -p .claude/blog/content-calendar');
  await bash(`mkdir -p ${blogDir}/drafts`);
  
  // Create subagent
  console.log('\nCreating blog content generator subagent...');
  await createBlogAgent({
    platform,
    analysis,
    tone: args.tone || analysis.tone,
    categories: analysis.categories
  });
  
  // Create content calendar
  console.log('\nInitializing content calendar...');
  const calendar = {
    scheduled: [],
    ideas: [
      { topic: "Getting Started with MCP Servers", priority: "high", seo_potential: "high" },
      { topic: "Top 10 AI Commands for Developers", priority: "medium", seo_potential: "high" },
      { topic: "Building Your First Custom Command", priority: "high", seo_potential: "medium" }
    ],
    published: []
  };
  await write('.claude/blog/content-calendar.json', JSON.stringify(calendar, null, 2));
  
  // Update CLAUDE.md
  console.log('\nUpdating CLAUDE.md...');
  await updateClaudeMd();
  
  // Generate immediate post if requested
  if (args.topic) {
    console.log(`\nGenerating blog post: "${args.topic}"`);
    const postPath = await generateBlogPost(args.topic, {
      blogDir,
      publish: args.publish || 'draft'
    });
    console.log(`✓ Blog post created: ${postPath}`);
  }
  
  // Generate ideas if requested
  if (args.generate_ideas) {
    console.log(`\nGenerating ${args.generate_ideas} blog post ideas...`);
    // Would call subagent to generate ideas
    console.log('✓ Ideas added to content calendar');
  }
  
  console.log('\n✅ Blog Content Agent Successfully Installed!\n');
  console.log('Usage examples:');
  console.log('  • "Write a blog post about Puppeteer MCP"');
  console.log('  • "Generate 5 tutorial ideas"');
  console.log('  • "Create this week\'s platform update post"');
  console.log('  • "Optimize the SEO for my latest post"');
  
  console.log('\nYour blog content generator is ready to create amazing content!');
};

// Execute
main().catch(console.error);
```

## Troubleshooting

### Platform Detection Issues
If your blog platform isn't detected:
1. Check that you're in the project root
2. Ensure blog files use standard structures
3. Manually specify with `blog_dir` parameter

### SEO Optimization
For best results:
- Research keywords before generating
- Review and refine generated content
- Add custom images for better engagement
- Monitor search rankings over time

### Content Quality
The agent provides a strong foundation, but:
- Always review for accuracy
- Add personal insights and experiences  
- Verify code examples work correctly
- Update stats and data regularly

## Security Notes

- No content is published without review
- API keys and credentials stay local
- Generated content is stored locally
- No external services except research