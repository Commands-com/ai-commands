# AI Code Reviewer

Production-ready code review assistant that performs comprehensive analysis of code quality, security vulnerabilities, performance bottlenecks, and maintainability metrics. Supports GitHub pull requests, local codebases, and CI/CD integration through multiple output formats.

## Key Features:
- **Multi-Language Support**: JavaScript, TypeScript, Python, Go, Rust, Java, C#, C++, Ruby
- **Comprehensive Analysis**: Security, performance, style, maintainability, complexity, documentation
- **Intelligent Scope**: Automatically focuses on relevant code based on context (PRs analyze changes)
- **Severity Filtering**: Focus on issues that matter with configurable thresholds
- **Privacy-First**: All analysis happens locally, no code sent to external services
- **Learning System**: Builds knowledge base of patterns for improved recommendations

## How to Use:
1. **Source**: GitHub URL (repository/PR) or local file/directory path
2. **Languages**: Comma-separated languages or "auto" for detection
3. **Focus**: Analysis area - security, performance, style, maintainability, complexity, documentation, or all
4. **Severity Threshold**: Filter issues by severity (info/warning/error)
5. **Save Report**: Generate markdown report file or display in response only

## Output:
- **Report Location**: `./code-review-report-[timestamp].md` (when save_report=yes)
- **Format**: Comprehensive markdown report with findings organized by severity and category

## Example Commands:
```bash
# Basic GitHub PR review
/code-review source="https://github.com/org/repo/pull/123"

# Security-focused review with high threshold
/code-review source="./src" focus=security severity_threshold=error

# High-priority issues only
/code-review source="." severity_threshold=error focus=security

# Maintainability review for Python code
/code-review source="./backend" languages=python focus=maintainability,complexity
```

## Report Sections:
- **Summary Dashboard**: Overall score, issue distribution, top recommendations
- **Issue Details**: File location, severity, category, fix suggestions with code examples
- **Metrics**: Cyclomatic complexity, code duplication, test coverage
- **Learning Notes**: Educational explanations for each issue type

## Security & Privacy:
- All code analysis performed locally
- Review patterns anonymized after 7 days
- Memory retention: 30 days for learning
- No external API calls except GitHub (when analyzing PRs)

## Prerequisites:
- GitHub MCP server for repository analysis
- Filesystem MCP server for local code inspection
- Memory MCP server for tracking patterns and recommendations
- SQLite MCP server for storing review history and metrics

## Best Practices:
- For PR reviews, provide the GitHub PR URL for automatic diff analysis
- Use severity thresholds to focus on critical issues
- Combine multiple focus areas for comprehensive reviews (e.g., `focus=security,performance`)
- Save reports for tracking improvements over time