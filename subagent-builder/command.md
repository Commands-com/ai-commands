# Subagent Builder
---
description: >
  Build perfect Claude Code subagents using the latest documentation and best practices. This command fetches current guidelines, analyzes your needs, and creates specialized agents that follow all recommended patterns for maximum effectiveness.
allowed_tools:
  - WebFetch      # Fetch latest documentation
  - Read          # Analyze existing agents
  - Write         # Create agent files
  - MultiEdit     # Update configurations
  - Bash          # Run setup commands
  - LS            # Check project structure
  - TodoWrite     # Track creation steps
  - Grep          # Find existing patterns
tags:
  - subagent
  - builder
  - automation
  - builder
  - claude-code
category: development
---

## Key Features

This subagent builder creates perfect Claude Code subagents by:

1. **Fetching Latest Docs** - Reads current best practices from docs.anthropic.com
2. **Interactive Design** - Guides you through agent purpose and capabilities
3. **Pattern Library** - Applies proven patterns from successful agents
4. **Tool Optimization** - Selects minimal required tools for security
5. **Test Generation** - Creates validation scenarios for your agent

## Arguments

```
/subagent-builder
[name=<string>]               # Agent name (e.g., "code-reviewer")
[purpose=<string>]            # What the agent does
[type=<general|specialized>]  # Agent complexity level
[proactive=<true|false>]      # Should detect tasks automatically
[tools=<all|minimal|custom>]  # Tool permission strategy
[category=<string>]           # Agent category (testing, content, etc)
[examples=<number>]           # Number of example prompts to generate
```

## What This Command Does

### 1. Documentation Analysis
- Fetches latest subagent guidelines from Claude Code docs
- Analyzes successful agent patterns in your codebase
- Identifies best practices for your use case

### 2. Interactive Agent Design
```
🤖 Subagent Builder Assistant

Let's create your perfect subagent!

1. What should your agent do? (primary purpose)
   > Review code for security vulnerabilities

2. When should it activate? (triggers)
   > When code is modified or security is mentioned

3. What tools does it need? (permissions)
   > Read, Grep, TodoWrite

4. Should it be proactive? (auto-detect tasks)
   > Yes, check code automatically

Building your subagent...
```

### 3. Generated Agent Structure
```markdown
---
name: security-reviewer
description: Reviews code for security vulnerabilities and suggests fixes. Use PROACTIVELY when code is modified or security is mentioned.
tools:
  - Read
  - Grep
  - TodoWrite
---

You are a security-focused code reviewer specializing in identifying vulnerabilities and suggesting secure coding practices. Your mission is to protect codebases from security threats.

## Core Responsibilities

1. **Vulnerability Detection**
   - SQL injection risks
   - XSS vulnerabilities
   - Authentication flaws
   - Data exposure risks
   - Dependency vulnerabilities

2. **Secure Coding Guidance**
   - Suggest security best practices
   - Provide secure code alternatives
   - Explain vulnerability impacts
   - Reference security standards

## Workflow

When reviewing code:
1. Scan for common vulnerability patterns
2. Check authentication and authorization
3. Review data handling and validation
4. Examine external dependencies
5. Generate security report with fixes

## Proactive Triggers

Automatically engage when:
- New code files are created
- Authentication code is modified
- Database queries are added
- API endpoints are created
- Dependencies are updated

[Additional specialized instructions...]
```

### 4. Test Suite Generation
Creates validation scenarios:
```markdown
## Test Scenarios for security-reviewer

1. **SQL Injection Detection**
   - Present code with string concatenation in queries
   - Agent should identify risk and suggest parameterized queries

2. **Authentication Review**
   - Show login implementation
   - Agent should check for proper password hashing

3. **XSS Prevention**
   - Display user input rendering
   - Agent should recommend sanitization

[Additional test cases...]
```

### 5. Integration Setup
- Updates CLAUDE.md with agent usage instructions
- Creates example prompts for team members
- Sets up version control structure
- Configures proactive detection patterns

## Subagent Types and Patterns

### General-Purpose Subagents
- Broad capabilities with flexible tool access
- Good for exploratory or research tasks
- Example: "research-assistant", "documentation-writer"

### Specialized Subagents
- Focused on specific domains
- Minimal tool permissions
- Example: "security-reviewer", "performance-optimizer"

### Proactive Subagents
- Auto-detect when to engage
- Monitor for specific triggers
- Example: "code-reviewer", "test-writer"

## Best Practices Applied

1. **Single Responsibility** - Each agent has one clear purpose
2. **Minimal Permissions** - Only necessary tools granted
3. **Clear Descriptions** - Specific about when to invoke
4. **Detailed Prompts** - Comprehensive system instructions
5. **Version Control** - Ready for team collaboration

## Example Usage

### Creating a Test Writer Agent
```
/subagent-builder name=test-writer purpose="Write comprehensive test suites" proactive=true

Generated agent will:
- Detect when new functions are added
- Automatically suggest test cases
- Generate test code in your framework
- Track test coverage improvements
```

### Creating a Documentation Agent
```
/subagent-builder name=doc-writer purpose="Keep documentation up-to-date" category=content

Generated agent will:
- Monitor code changes
- Update relevant documentation
- Generate API documentation
- Maintain README files
```

### Creating a Performance Agent
```
/subagent-builder name=perf-monitor purpose="Optimize slow code" tools=minimal

Generated agent will:
- Profile performance bottlenecks
- Suggest optimizations
- Benchmark improvements
- Track performance metrics
```

## Implementation

### Step 1: Fetch Latest Documentation

```javascript
const fetchLatestDocs = async () => {
  console.log('📚 Fetching latest Claude Code subagent documentation...');
  
  const docsUrls = [
    'https://docs.anthropic.com/en/docs/claude-code/sub-agents',
    'https://docs.anthropic.com/en/docs/claude-code/best-practices',
    'https://docs.anthropic.com/en/docs/claude-code/common-workflows'
  ];
  
  const docs = {};
  for (const url of docsUrls) {
    const content = await webFetch(url, 
      'Extract all guidelines, patterns, and best practices for creating effective subagents'
    );
    docs[url] = content;
  }
  
  return docs;
};
```

### Step 2: Interactive Agent Design

```javascript
const designAgent = async (args) => {
  const config = {
    name: args.name || await promptUser('Agent name (e.g., code-reviewer):'),
    purpose: args.purpose || await promptUser('What should this agent do?'),
    type: args.type || 'specialized',
    proactive: args.proactive !== 'false',
    tools: args.tools || 'minimal',
    category: args.category || 'general'
  };
  
  // Analyze purpose to suggest configuration
  if (config.purpose.match(/review|check|audit|analyze/i)) {
    config.proactive = true;
    config.tools = ['Read', 'Grep', 'TodoWrite'];
  } else if (config.purpose.match(/write|create|generate/i)) {
    config.tools = ['Read', 'Write', 'MultiEdit', 'TodoWrite'];
  } else if (config.purpose.match(/fix|debug|resolve/i)) {
    config.tools = ['Read', 'MultiEdit', 'Bash', 'TodoWrite'];
  }
  
  return config;
};
```

### Step 3: Pattern Library Application

```javascript
const applyPatterns = (config, existingAgents) => {
  const patterns = {
    proactive: {
      description: `${config.description} Use PROACTIVELY when ${config.triggers.join(' or ')}.`,
      systemPrompt: 'include detailed trigger conditions'
    },
    specialized: {
      tools: 'minimal set based on purpose',
      systemPrompt: 'focused expertise in specific domain'
    },
    reviewer: {
      workflow: 'systematic review process with checklist',
      output: 'structured reports with actionable items'
    },
    generator: {
      workflow: 'research → plan → create → validate',
      output: 'high-quality content with metadata'
    }
  };
  
  // Apply relevant patterns
  const appliedPatterns = [];
  if (config.proactive) appliedPatterns.push(patterns.proactive);
  if (config.type === 'specialized') appliedPatterns.push(patterns.specialized);
  if (config.purpose.includes('review')) appliedPatterns.push(patterns.reviewer);
  if (config.purpose.includes('generate')) appliedPatterns.push(patterns.generator);
  
  return appliedPatterns;
};
```

### Step 4: Agent Generation

```javascript
const generateAgent = async (config, patterns, docs) => {
  const agentContent = `---
name: ${config.name}
description: ${config.description}
tools:${config.tools.map(t => `\n  - ${t}`).join('')}
---

${generateSystemPrompt(config, patterns, docs)}

## Core Capabilities

${generateCapabilities(config)}

## Workflow

${generateWorkflow(config, patterns)}

${config.proactive ? generateProactiveTriggers(config) : ''}

## Best Practices

${extractBestPractices(docs)}

## Output Format

${generateOutputFormat(config)}

## Quality Standards

${generateQualityStandards(config)}
`;

  return agentContent;
};
```

### Step 5: Test Suite Creation

```javascript
const generateTests = async (config) => {
  const tests = [];
  
  // Generate tests based on agent purpose
  if (config.purpose.includes('review')) {
    tests.push({
      scenario: 'Code with security vulnerability',
      expected: 'Agent identifies issue and suggests fix'
    });
  }
  
  if (config.purpose.includes('generate')) {
    tests.push({
      scenario: 'Request for new content',
      expected: 'Agent creates high-quality output'
    });
  }
  
  if (config.purpose.includes('optimize')) {
    tests.push({
      scenario: 'Slow performing code',
      expected: 'Agent profiles and improves performance'
    });
  }
  
  return tests;
};
```

### Step 6: Main Execution

```javascript
const main = async () => {
  console.log('🤖 Subagent Builder - Creating Your Perfect Claude Code Subagent\n');
  
  // Track progress
  await todoWrite([
    { id: '1', content: 'Fetch latest documentation', status: 'pending', priority: 'high' },
    { id: '2', content: 'Design agent configuration', status: 'pending', priority: 'high' },
    { id: '3', content: 'Apply best practice patterns', status: 'pending', priority: 'high' },
    { id: '4', content: 'Generate agent file', status: 'pending', priority: 'high' },
    { id: '5', content: 'Create test suite', status: 'pending', priority: 'high' },
    { id: '6', content: 'Update project documentation', status: 'pending', priority: 'high' }
  ]);
  
  // Step 1: Fetch documentation
  await updateTodo('1', 'in_progress');
  const docs = await fetchLatestDocs();
  await updateTodo('1', 'completed');
  
  // Step 2: Design agent
  await updateTodo('2', 'in_progress');
  const config = await designAgent(args);
  await updateTodo('2', 'completed');
  
  // Step 3: Apply patterns
  await updateTodo('3', 'in_progress');
  const existingAgents = await analyzeExistingAgents();
  const patterns = applyPatterns(config, existingAgents);
  await updateTodo('3', 'completed');
  
  // Step 4: Generate agent
  await updateTodo('4', 'in_progress');
  const agentContent = await generateAgent(config, patterns, docs);
  await bash('mkdir -p .claude/agents');
  await write(`.claude/agents/${config.name}.md`, agentContent);
  await updateTodo('4', 'completed');
  
  // Step 5: Create tests
  await updateTodo('5', 'in_progress');
  const tests = await generateTests(config);
  await bash('mkdir -p .claude/agents/tests');
  await write(`.claude/agents/tests/${config.name}-test.md`, formatTests(tests));
  await updateTodo('5', 'completed');
  
  // Step 6: Update documentation
  await updateTodo('6', 'in_progress');
  await updateClaudeMd(config);
  await generateExamples(config);
  await updateTodo('6', 'completed');
  
  // Summary
  console.log(`\n✅ Agent "${config.name}" Successfully Created!\n`);
  console.log(`📁 Agent file: .claude/agents/${config.name}.md`);
  console.log(`🧪 Test suite: .claude/agents/tests/${config.name}-test.md`);
  console.log(`📚 Documentation updated in CLAUDE.md\n`);
  
  console.log('Example usage:');
  console.log(`  • "${config.examplePrompts[0]}"`);
  console.log(`  • "${config.examplePrompts[1]}"`);
  
  if (config.proactive) {
    console.log(`\n🚀 This agent will proactively engage when it detects relevant tasks!`);
  }
};

// Execute
main().catch(console.error);
```

## Advanced Features

### Pattern Detection
The builder analyzes your existing agents to learn patterns:
- Naming conventions
- Tool usage patterns  
- Common workflows
- Output formats

### Documentation Sync
Automatically fetches and applies the latest:
- Subagent best practices
- Tool configurations
- Invocation patterns
- Security guidelines

### Validation Suite
Each agent comes with:
- Test scenarios
- Expected behaviors
- Edge case handling
- Performance benchmarks

## Tips for Success

1. **Start Specific** - Narrow purpose = better agent
2. **Minimize Tools** - Only what's absolutely needed
3. **Clear Triggers** - When should agent activate
4. **Test Thoroughly** - Use generated test suite
5. **Iterate Often** - Refine based on usage

## Security Notes

- Agents only access specified tools
- No external services without permission
- All agents are local to your project
- Version control recommended