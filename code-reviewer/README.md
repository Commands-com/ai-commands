# AI Code Reviewer

This AI command performs intelligent code review by analyzing source code for quality, security vulnerabilities, performance issues, and best practices. It can review GitHub pull requests or local codebases.

## How to Use:
1. **Source**: Provide either a GitHub URL (repository or pull request) or a local file/directory path
2. **Languages**: Specify programming languages to focus on, or leave as "auto" for automatic detection
3. **Focus**: Choose the primary review focus area (security, performance, style, or all)
4. **Save Report**: Choose whether to generate detailed report files

## Prerequisites:
- GitHub MCP server for repository analysis
- Filesystem MCP server for local code inspection
- Memory MCP server for tracking patterns and recommendations
- SQLite MCP server for storing review history and metrics

The command will provide constructive, educational feedback with specific line numbers, explanations of issues found, and recommendations for improvement.