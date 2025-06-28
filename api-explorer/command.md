# API Explorer & Documentation Generator
---
description: >
  Comprehensive API analysis tool that discovers, tests, and documents APIs.
  Automatically explores REST endpoints, generates OpenAPI specifications,
  tests authentication methods, and creates interactive documentation.
  Perfect for API integration, testing, and reverse engineering.
allowed_tools:
  - puppeteer       # Web scraping and browser automation
  - filesystem      # Save documentation and test results
  - memory          # Track API patterns and schemas
  - sqlite          # Store API metadata and test history
---

## Arguments

```
/api-explore 
base_url="<https://api.example.com>"
[auth_type=<none|bearer|basic|api_key>]
[auth_value=<token_or_key>]
[output_format=<openapi|postman|markdown>]
[test_endpoints=<yes|no>]
```
*Defaults → `auth_type=none  output_format=openapi  test_endpoints=yes`*

### Example

```
/api-explore 
base_url="https://jsonplaceholder.typicode.com"
output_format=openapi
test_endpoints=yes
```

---

## Context – what the AI should do

1. **Parse Arguments and Setup**
   * `base_url` - API base URL to explore
   * `auth_type` - authentication method to use
   * `auth_value` - authentication credentials
   * `output_format` - desired documentation format
   * `test_endpoints` - whether to test discovered endpoints

2. **API Discovery Phase**
   * **Robots.txt Analysis**: Check `/robots.txt` for API paths
   * **Common Endpoint Discovery**: Test standard REST patterns (`/api/v1/`, `/users`, `/posts`, etc.)
   * **Documentation Scraping**: Use Puppeteer to find API docs, Swagger/OpenAPI specs
   * **Sitemap Analysis**: Parse sitemap.xml for API endpoints
   * **Link Extraction**: Follow discovered API documentation pages

3. **Endpoint Analysis**
   * **HTTP Methods Testing**: Test GET, POST, PUT, DELETE, PATCH for each endpoint
   * **Parameter Discovery**: Analyze query parameters, path parameters, request bodies
   * **Response Schema Analysis**: Examine response structures and data types
   * **Error Handling**: Test error responses (400, 401, 403, 404, 500)
   * **Rate Limiting**: Detect and document rate limiting behavior

4. **Authentication Testing**
   * **Bearer Token**: Test Authorization header with provided token
   * **API Key**: Test various API key locations (header, query, body)
   * **Basic Auth**: Test username/password combinations
   * **OAuth Discovery**: Look for OAuth endpoints and flows

5. **Schema Generation**
   * **Request Schemas**: Generate JSON schemas for request bodies
   * **Response Schemas**: Create schemas for response structures
   * **Data Type Inference**: Analyze actual data to infer types and constraints
   * **Relationship Mapping**: Identify relationships between endpoints

6. **Documentation Generation**
   * **OpenAPI 3.0**: Generate complete OpenAPI specification
   * **Postman Collection**: Create importable Postman collection
   * **Markdown Docs**: Human-readable API documentation
   * **Code Examples**: Generate example requests in multiple languages

7. **Testing and Validation**
   * **Endpoint Health Checks**: Verify all endpoints are accessible
   * **Response Time Analysis**: Measure and document performance
   * **Data Consistency**: Check for consistent response formats
   * **Security Analysis**: Identify potential security issues

8. **Knowledge Base Updates**
   * Store API patterns in memory: `/memory__create_entities` for endpoints, schemas
   * Build relationship graph: API → has_endpoint → Endpoint → returns → Schema
   * Track common patterns across different APIs
   * Store in SQLite: endpoint metadata, response times, test results

9. **Output Generation**
   * Save documentation: `/filesystem__write_file` for each format
   * Create test report with coverage metrics
   * Generate summary dashboard with API statistics
   * Export findings for integration with other tools

> Focus on being thorough but respectful of rate limits. Provide actionable insights for API integration and highlight any security or usability concerns.