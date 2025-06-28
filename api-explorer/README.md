# API Explorer & Documentation Generator

This AI command performs comprehensive API analysis by discovering, testing, and documenting REST APIs automatically. It explores endpoints, generates OpenAPI specifications, tests authentication methods, and creates interactive documentation.

## How to Use:
1. **API Base URL**: Enter the base URL of the API to explore and analyze
2. **Authentication Type**: Choose the auth method (none, bearer token, basic auth, or API key)
3. **Authentication Value**: Provide your token, key, or credentials if using authentication
4. **Output Format**: Select documentation format (OpenAPI, Postman collection, or Markdown)
5. **Test Endpoints**: Choose whether to test discovered endpoints for functionality

## What It Does:
- **Discovery**: Finds API endpoints through robots.txt, common patterns, and documentation scraping
- **Analysis**: Tests HTTP methods, parameters, response schemas, and error handling
- **Documentation**: Generates complete API specifications with examples
- **Testing**: Validates endpoint accessibility and response consistency
- **Security**: Identifies potential security issues and rate limiting

## Generated Output:
- OpenAPI 3.0 specifications
- Postman collections for testing
- Human-readable Markdown documentation
- Code examples in multiple languages
- Test reports with coverage metrics

## Prerequisites:
- Puppeteer MCP server for web scraping and browser automation
- Filesystem MCP server for saving documentation and test results
- Memory MCP server for tracking API patterns and schemas
- SQLite MCP server for storing API metadata and test history