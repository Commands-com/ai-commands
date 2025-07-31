# Subagent Builder

Build perfect Claude Code subagents with best practices and up-to-date documentation. This command reads the latest Claude Code documentation, analyzes your project needs, and creates specialized subagents that follow all recommended patterns.

## Features

- **Documentation-Driven**: Fetches latest subagent best practices from Claude Code docs
- **Interactive Design**: Guides you through agent creation with smart defaults
- **Pattern Library**: Includes proven patterns from performance, blog, and content agents
- **Tool Optimization**: Automatically selects minimal required tools
- **Testing Suite**: Generates test scenarios to validate your agent

## Usage

Basic:
```bash
/subagent-builder
```

With options:
```bash
/subagent-builder name=code-reviewer purpose="Review code for security issues"
/subagent-builder type=specialized category=testing
/subagent-builder proactive=true tools=minimal
```

## What It Creates

1. **Subagent File**: `.claude/agents/{name}.md` with optimized configuration
2. **Test File**: `.claude/agents/tests/{name}-test.md` with validation scenarios
3. **Documentation**: Updates CLAUDE.md with usage instructions
4. **Examples**: Adds example prompts for invoking the agent

## Best Practices Applied

- Single, clear responsibility principle
- Minimal tool permissions
- Proactive detection patterns
- Detailed system prompts
- Version control ready

## Examples

The command includes patterns from successful agents like:
- Performance Optimizer (profiling and benchmarking)
- Blog Content Generator (research and writing)
- Code Reviewer (quality and security checks)
- Debugger (systematic issue resolution)

## Requirements

- Claude Code v1.0+
- Project with `.claude/` directory support
- Internet connection for documentation fetch