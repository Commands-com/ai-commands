# Review Team Command

A project-aware code review management command for Claude Code that creates and orchestrates a team of specialized review agents tailored to your specific project.

## Overview

The `/review-team` command analyzes your project structure and creates six specialized agents customized for your project type. Whether you're working on a React app, Express API, Python package, or any other project, the review team adapts to provide relevant, focused feedback.

## Installation

This command is part of the AI Commands collection. Once installed, you can use it in any project to set up a review team.

## Quick Start

```bash
# Just run it! (analyzes project and creates tailored agents)
/review-team

# Run a specific agent
/review-team agent=code-quality-reviewer

# Check status only
/review-team check=true
```

## How It Works

1. **Checks Existing Agents** - Looks for review team in `.claude/agents/`
2. **Analyzes Your Project** (if needed) - Detects language, framework, UI components, APIs, databases
3. **Customizes Agents** - Tailors each agent with project-specific context and focus areas
4. **Reviews Smart** - Reviews uncommitted git changes or files in your current context window

> **⚠️ Important**: After creating new agents, you must restart Claude Code for them to become available. The agents are saved to your `.claude/agents/` directory and will be accessible after restart.

## Review Team Members

1. **implementation-verifier** - Validates code against specifications
2. **reality-checker** - Assesses actual vs claimed completion
3. **code-quality-reviewer** - Identifies complexity and maintainability issues
4. **compliance-validator** - Ensures adherence to project standards
5. **completion-auditor** - Verifies end-to-end functionality
6. **ui-tester** - Tests user interfaces across platforms

## Usage Examples

### Basic Usage
```bash
# Run full review (auto-creates agents if needed)
/review-team

# Run specific agent
/review-team agent=reality-checker

# Just check status
/review-team check=true
```

### Direct Agent Usage
```bash
# Once created, use agents directly for targeted reviews
# NOTE: Restart Claude Code after creating agents for them to be available
task subagent_type=implementation-verifier prompt="Check if auth matches the spec"
task subagent_type=ui-tester prompt="Test the new dashboard on mobile"
task subagent_type=code-quality-reviewer prompt="Review the data layer for over-engineering"
```

### Management
```bash
# Force recreate all agents
/review-team create=true

# Show detailed help
/review-team verbose=true
```

## Command Arguments

- `agent` - Run specific agent only (optional)
- `check` - Only check status, don't run review (optional)
- `create` - Force create/recreate all agents (optional)
- `verbose` - Show detailed help and descriptions (optional)

## Best Practices

1. **Just Run It** - The command handles setup automatically
2. **Review Often** - Run reviews before commits, not after
3. **Be Specific** - Give agents context about what to review
4. **Act on Feedback** - Address issues as they're found
5. **Layer Reviews** - Different agents catch different issues

## Recommended Workflow

1. Start with `reality-checker` for honest assessment
2. Use `implementation-verifier` to check against specs
3. Run `completion-auditor` to verify functionality
4. Apply `code-quality-reviewer` for simplification
5. Test with `ui-tester` for user experience
6. Finish with `compliance-validator` for standards

## Project-Specific Customization Examples

### React Project
- UI tester focuses on component testing and user interactions
- Quality reviewer checks for React best practices and hooks usage
- Implementation verifier validates prop types and state management

### Express API Project  
- Implementation verifier prioritizes endpoint validation
- Completion auditor tests API integration points
- Reality checker ensures error handling is production-ready

### Python Package
- Compliance validator checks PEP standards
- Quality reviewer identifies Pythonic vs non-Pythonic code
- UI tester adapts to CLI interface testing

## Tips

- Agents are created in your project's `.claude/agents/` directory
- Each agent is customized with your project's specific context
- Agents automatically update if project type changes significantly
- Review findings focus on what matters for your tech stack
- Use `create=true` to refresh agents after major project changes

## License

MIT - Part of the AI Commands collection