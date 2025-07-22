# API Explorer & Documentation Generator

Ethical API reconnaissance tool that discovers, tests, and documents APIs while respecting rate limits, robots.txt, and terms of service. Generates comprehensive documentation including OpenAPI specs, Postman collections, and usage guides.

## Key Features:
- **Ethical Discovery**: Respects robots.txt and implements rate limiting (1 req/sec)
- **Safe Testing**: Uses read-only methods by default (GET/HEAD)
- **Comprehensive Documentation**: Generates OpenAPI, Postman, and Markdown docs
- **Security-First**: Handles credentials securely, strips sensitive data from outputs
- **Smart Analysis**: Detects authentication methods, schemas, and relationships
- **GraphQL Support**: Identifies and documents GraphQL endpoints

## How to Use:
1. **API Base URL**: Enter the base URL of the API to explore
2. **Authentication Type**: Choose auth method (none, bearer, basic, api_key)
3. **Authentication Value**: Provide credentials (handled securely, never logged)
4. **Depth**: Control exploration thoroughness:
   - **Shallow**: Basic discovery and documentation only
   - **Standard**: Full analysis with safe testing methods
   - **Deep**: Extensive testing including write operations (use carefully)

## What It Generates:
- **OpenAPI 3.0 Specification**: Complete API definition with schemas
- **Postman Collection**: Ready-to-import collection with examples
- **Markdown Documentation**: Human-readable guides with code examples
- **Security Analysis**: Authentication requirements and rate limits
- **Test Report**: Coverage metrics and endpoint health status

## Safety & Ethics:
- Implements automatic rate limiting to avoid overwhelming servers
- Respects robots.txt directives and crawl delays
- Uses safe HTTP methods (GET/HEAD) unless depth=deep
- Honors Retry-After headers and implements exponential backoff
- Strips accidentally captured credentials from all outputs
- Includes warnings about respecting API terms of service

## Example Commands:
```bash
# Basic public API exploration
/api-explore base_url="https://api.example.com"

# Authenticated API with controlled depth
/api-explore 
base_url="https://api.github.com"
auth_type=bearer
auth_value="ghp_xxxxxxxxxxxx"
depth=shallow

# API key authentication with standard exploration
/api-explore 
base_url="https://api.service.com/v1"
auth_type=api_key
auth_value="your-api-key"
```

## Prerequisites:
- Puppeteer MCP server for web scraping and browser automation
- Filesystem MCP server for saving documentation
- Memory MCP server for tracking patterns and schemas
- SQLite MCP server for storing metadata and history

## Best Practices:
- Always check the API's terms of service before exploration
- Use shallow depth for initial discovery
- Provide authentication only when necessary
- Review generated documentation for any sensitive data
- Use the tool for integration planning, not vulnerability exploitation