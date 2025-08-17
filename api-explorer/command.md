---
name: API Explorer & Documentation Generator
description: Comprehensive API analysis tool that discovers, tests, and documents APIs. Automatically explores REST endpoints, generates OpenAPI specifications, tests authentication methods, and creates interactive documentation. Respects rate limits and robots.txt while providing ethical API reconnaissance.
allowed_tools:
  - puppeteer       # Web scraping and browser automation
  - filesystem      # Save documentation and test results
  - memory          # Track API patterns and schemas
  - sqlite          # Store API metadata and test history
tags:
  - api-discovery
  - documentation
  - testing
  - integration
category: api-tools
---

## Arguments

```
/api-explore 
base_url="<https://api.example.com>"
[auth_type=<none|bearer|basic|api_key>]
[auth_value=<token_or_key>]
[depth=<shallow|standard|deep>]
```
*Defaults → `auth_type=none  depth=standard`*

### Examples

```bash
# Basic public API exploration
/api-explore 
base_url="https://jsonplaceholder.typicode.com"

# Authenticated API with depth control
/api-explore 
base_url="https://api.github.com"
auth_type=bearer
auth_value="ghp_xxxxxxxxxxxx"
depth=shallow

# API key authentication
/api-explore 
base_url="https://api.weather.com/v1"
auth_type=api_key
auth_value="your-api-key"
```

---

## Context – what the AI should do

1. **Parse Arguments and Setup**
   * `base_url` - API base URL to explore
   * `auth_type` - authentication method to use
   * `auth_value` - authentication credentials (handle securely)
   * `depth` - exploration depth (shallow: basic discovery, standard: full analysis, deep: extensive testing)

2. **API Discovery Phase** (Respecting Ethics & Limits)
   * **Robots.txt Compliance**: Check and respect `/robots.txt` directives
   * **Rate Limiting**: Implement 1 request/second default throttle
   * **Common Endpoint Discovery**: Test standard REST patterns (`/api/v1/`, `/users`, `/posts`, etc.)
   * **Documentation Scraping**: Use Puppeteer to find API docs, Swagger/OpenAPI specs
   * **Sitemap Analysis**: Parse sitemap.xml for API endpoints
   * **Respect Terms of Service**: Avoid aggressive crawling or brute-forcing

3. **Endpoint Analysis** (Safe Methods Only)
   * **HTTP Methods Testing**: Test GET and HEAD only (unless depth=deep)
   * **Parameter Discovery**: Analyze query parameters, path parameters from documentation
   * **Response Schema Analysis**: Examine response structures and data types
   * **Error Handling**: Test error responses respectfully (400, 401, 403, 404)
   * **Rate Limit Detection**: Detect limits and honor Retry-After headers

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

6. **Comprehensive Documentation Generation**
   * **OpenAPI 3.0**: Generate complete OpenAPI specification
   * **Postman Collection**: Create importable collection with examples
   * **Markdown Docs**: Human-readable API documentation with usage guides
   * **Code Examples**: Generate example requests in multiple languages
   * **Security Notes**: Document authentication requirements and rate limits
   * **GraphQL Support**: Detect and document GraphQL endpoints if present

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
   * Save all documentation formats: OpenAPI, Postman, and Markdown
   * Create comprehensive test report with coverage metrics
   * Generate summary dashboard with API statistics
   * Include security considerations and best practices
   * Strip any accidentally captured credentials from outputs

10. **Safety & Compliance**
    * **Credential Security**: Never log or save auth values in reports
    * **Respect Rate Limits**: Exponential backoff on 429 responses
    * **Avoid Destructive Operations**: Skip DELETE/PUT/PATCH unless depth=deep
    * **Data Privacy**: Sanitize example responses to remove PII
    * **Terms of Service**: Include warnings about respecting API ToS

> Provide ethical API reconnaissance with focus on documentation and integration guidance. Respect rate limits, robots.txt, and terms of service. Highlight security considerations without exploiting vulnerabilities.