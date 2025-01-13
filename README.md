# Apollo-mcp

An MCP server implementation for Apollo.io integration, enabling AI assistants to interact with Apollo's sales intelligence platform through the Model Context Protocol.

## Development Checklist

### Project Setup
- [ ] Initialize TypeScript project
  ```bash
  npm init -y
  npm install @modelcontextprotocol/sdk
  npm install -D typescript @types/node
  ```
- [ ] Configure TypeScript
  - [ ] Create tsconfig.json with ESM support
  - [ ] Set up build and dev scripts
  - [ ] Configure module resolution
- [ ] Add Apollo.io dependencies
  ```bash
  npm install axios
  ```
- [ ] Setup environment configuration
  - [ ] Create .env management
  - [ ] Add Apollo API key configuration
  - [ ] Configure rate limiting settings

### Core Server Implementation
- [ ] Create server instance
  ```typescript
  const server = new Server({
    name: "apollo-server",
    version: "1.0.0",
  }, {
    capabilities: {
      resources: {},
      tools: {}
    }
  });
  ```
- [ ] Implement transport layer
  ```typescript
  const transport = new StdioServerTransport();
  await server.connect(transport);
  ```
- [ ] Add error handling
  - [ ] Protocol errors
  - [ ] Transport errors
  - [ ] Apollo API errors

### Apollo API Client
- [ ] Create API wrapper class
  ```typescript
  class ApolloClient {
    constructor(private apiKey: string) {}
    
    async enrichPeople(domains: string[], emails: string[]) {
      // People enrichment endpoint
      return this.post('/v1/people/enrich', { domains, emails });
    }
    
    async enrichOrganization(domain: string) {
      // Organization enrichment endpoint
      return this.post('/v1/organizations/enrich', { domain });
    }
    
    async searchPeople(params: SearchPeopleParams) {
      // People search endpoint
      return this.post('/v1/people/search', params);
    }
    
    // Additional Apollo API methods...
  }
  ```

### Resources Implementation
- [ ] Define resource schemas aligned with Apollo data models
  ```typescript
  interface PersonResource {
    uri: string;
    name: string;
    data: {
      first_name: string;
      last_name: string;
      email: string;
      organization: string;
      title: string;
      // Additional Apollo person fields...
    }
  }
  ```
- [ ] Implement resource handlers
  ```typescript
  server.setRequestHandler(ListResourcesRequestSchema, async () => {
    return {
      resources: [
        {
          uri: "apollo://people",
          name: "People Search",
          description: "Search and enrich people data"
        },
        {
          uri: "apollo://organizations",
          name: "Organization Data",
          description: "Organization enrichment and search"
        }
      ]
    };
  });
  ```
- [ ] Add resource caching
  - [ ] Cache enriched data
  - [ ] Implement TTL for cached resources
  - [ ] Handle cache invalidation

### Tools Implementation
- [ ] People Enrichment Tool
  ```typescript
  {
    name: "enrich-person",
    description: "Enrich person data using email or domain",
    inputSchema: {
      type: "object",
      properties: {
        email: { type: "string" },
        domain: { type: "string" },
      },
      anyOf: [
        { required: ["email"] },
        { required: ["domain"] }
      ]
    }
  }
  ```
- [ ] Organization Enrichment Tool
  ```typescript
  {
    name: "enrich-organization",
    description: "Get detailed organization data",
    inputSchema: {
      type: "object",
      properties: {
        domain: { type: "string" }
      },
      required: ["domain"]
    }
  }
  ```
- [ ] People Search Tool
  ```typescript
  {
    name: "search-people",
    description: "Search for people with filters",
    inputSchema: {
      type: "object",
      properties: {
        q: { type: "string" },
        person_titles: { 
          type: "array",
          items: { type: "string" }
        },
        organization_domains: { 
          type: "array",
          items: { type: "string" }
        },
        // Additional Apollo search parameters...
      }
    }
  }
  ```

### Rate Limiting & Security
- [ ] Implement rate limiting
  - [ ] Track API usage
  - [ ] Cache responses
  - [ ] Queue requests
- [ ] Add security measures
  - [ ] API key validation
  - [ ] Request validation
  - [ ] Error handling
  ```typescript
  class RateLimiter {
    private requests: number = 0;
    private resetTime: number = Date.now();
    
    async checkLimit() {
      // Implement rate limiting logic
      // Apollo's rate limits vary by endpoint and plan
    }
  }
  ```

### Testing
- [ ] Set up test framework
  ```bash
  npm install -D jest @types/jest ts-jest
  ```
- [ ] Create test utilities
  ```typescript
  // Mock Apollo API responses
  const mockEnrichPersonResponse = {
    first_name: "John",
    last_name: "Doe",
    email: "john@example.com",
    // ...
  };
  ```
- [ ] Write tests
  - [ ] API wrapper tests
  - [ ] Rate limiter tests
  - [ ] Tool implementation tests
  - [ ] Resource handler tests

### Building & Packaging
- [ ] Configure build process
  ```json
  {
    "scripts": {
      "build": "tsc",
      "dev": "tsc --watch",
      "start": "node dist/index.js",
      "test": "jest",
      "lint": "eslint src/**/*.ts"
    }
  }
  ```
- [ ] Set up distribution
  - [ ] Package configuration
  - [ ] Dependencies management
  - [ ] Type definitions

## Getting Started

1. Clone the repository
```bash
git clone https://github.com/yourusername/Apollo-mcp.git
cd Apollo-mcp
```

2. Install dependencies
```bash
npm install
```

3. Configure environment
```bash
cp .env.example .env
# Add your Apollo API key to .env
```

4. Build and start
```bash
npm run build
npm start
```

## Usage with Claude Desktop

Configure Claude Desktop to use the Apollo MCP server:

```json
{
  "mcpServers": {
    "apollo": {
      "command": "node",
      "args": ["dist/index.js"],
      "env": {
        "APOLLO_API_KEY": "your_api_key_here",
        "RATE_LIMIT_PER_SECOND": "10"
      }
    }
  }
}
```

## Example Queries

Here are some example queries you can use with Claude once the server is connected:

1. Person Enrichment:
   ```text
   Can you find more information about john@example.com?
   ```

2. Organization Research:
   ```text
   What can you tell me about microsoft.com as a company?
   ```

3. People Search:
   ```text
   Find CTOs at companies in the healthcare industry
   ```

## Error Handling

The server implements comprehensive error handling for common scenarios:

- Rate limiting exceeded
- Invalid API key
- Network errors
- Invalid input data
- Apollo API errors

Each error is properly mapped to appropriate MCP protocol errors and includes helpful error messages.

## Contributing

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Acknowledgments

- [Model Context Protocol](https://modelcontextprotocol.io)
- [MCP TypeScript SDK](https://github.com/modelcontextprotocol/typescript-sdk)
- [Apollo.io API](https://docs.apollo.io/)