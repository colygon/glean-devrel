# Glean MCP Integration Guide

## Quick Setup with Claude Desktop

### Automatic Configuration (Recommended)

```bash
# Install and configure in one command
npx @gleanwork/configure-mcp-server \
  --client claude \
  --token YOUR_GLEAN_API_TOKEN \
  --instance your-company
```

This will automatically update your Claude Desktop configuration at:
- macOS: `~/Library/Application Support/Claude/claude_desktop_config.json`
- Windows: `%APPDATA%\Claude\claude_desktop_config.json`

### Manual Configuration

1. **Get your Glean credentials**
   - API Token: Settings → API Keys
   - Instance name: Your subdomain (e.g., `acme` from `acme.glean.com`)

2. **Edit Claude Desktop config**

```json
{
  "mcpServers": {
    "glean": {
      "command": "npx",
      "args": ["-y", "@gleanwork/local-mcp-server"],
      "env": {
        "GLEAN_INSTANCE": "your-company",
        "GLEAN_API_TOKEN": "your-api-token"
      }
    }
  }
}
```

3. **Restart Claude Desktop**

4. **Test it**

Open Claude and ask:
```
Can you search our company knowledge base for "Q4 objectives"?
```

Claude will use the Glean MCP tools to search and return results.

---

## Available MCP Tools

### 1. company_search

Search your company's indexed content.

**Example prompts:**
- "Search for our security policy"
- "Find recent presentations about product strategy"
- "What documents mention the new hire process?"

**Tool parameters:**
```typescript
{
  query: string;           // Search query
  datasources?: string[];  // Filter by source (e.g., ["confluence", "google-drive"])
  maxResults?: number;     // Limit results (default: 10)
  dateFilter?: {
    start?: string;        // ISO date
    end?: string;
  }
}
```

### 2. chat

Have a conversation with Glean's AI assistant.

**Example prompts:**
- "Ask Glean: What are the company's core values?"
- "Chat with our knowledge base about the benefits policy"
- "Use Glean to explain how our authentication system works"

**Tool parameters:**
```typescript
{
  messages: Array<{
    role: 'user' | 'assistant';
    content: string;
  }>;
  includeCitations?: boolean;  // Return source documents
}
```

### 3. people_profile_search

Find information about employees.

**Example prompts:**
- "Who is the PM for the mobile app?"
- "Find engineers on the platform team"
- "Who reports to Sarah Johnson?"

**Tool parameters:**
```typescript
{
  query: string;        // Name, title, team, skills, etc.
  maxResults?: number;  // Limit results
}
```

### 4. read_documents

Retrieve full content of specific documents.

**Example prompts:**
- "Read the Q4 roadmap document"
- "Get the content from [Glean URL]"
- "Show me the full text of document ID 12345"

**Tool parameters:**
```typescript
{
  documentIds?: string[];  // Glean document IDs
  urls?: string[];         // Direct URLs to documents
}
```

---

## Integration with Other MCP Clients

### Cursor IDE

```bash
npx @gleanwork/configure-mcp-server \
  --client cursor \
  --token YOUR_GLEAN_API_TOKEN \
  --instance your-company
```

Config location: `~/.cursor/mcp.json`

### Windsurf

```bash
npx @gleanwork/configure-mcp-server \
  --client windsurf \
  --token YOUR_GLEAN_API_TOKEN \
  --instance your-company
```

### Custom MCP Client

Add to your MCP client configuration:

```json
{
  "mcpServers": {
    "glean": {
      "command": "node",
      "args": [
        "/path/to/node_modules/@gleanwork/local-mcp-server/dist/index.js"
      ],
      "env": {
        "GLEAN_INSTANCE": "your-company",
        "GLEAN_API_TOKEN": "your-api-token"
      }
    }
  }
}
```

---

## Remote MCP Server (Enterprise)

For production deployments, use Glean's remote MCP server:

1. **Enable MCP in Glean Admin Console**
   - Navigate to: Admin → Integrations → MCP
   - Toggle "Enable MCP Integration"
   - Configure allowed clients

2. **No client-side configuration needed**
   - Users authenticate via Glean SSO
   - MCP tools automatically available
   - Centrally managed permissions

3. **Benefits**
   - Automatic updates
   - Better performance
   - Simplified credential management
   - Enterprise audit logs

---

## Use Cases

### 1. Engineering Onboarding

**Scenario**: New engineer exploring codebase

```
Prompt to Claude:
"I'm looking at the authentication module. Can you search our docs 
for how we handle OAuth tokens and explain the flow?"
```

Claude uses `company_search` → finds design docs → explains with citations

### 2. Sales Enablement

**Scenario**: Sales rep preparing for demo

```
Prompt to Claude:
"Search for customer success stories in healthcare. Then summarize 
the top 3 with their results and challenges solved."
```

Claude uses `company_search` → finds case studies → creates summary

### 3. Support Troubleshooting

**Scenario**: Support engineer debugging issue

```
Prompt to Claude:
"Search for known issues related to SSO timeout errors. Also, who 
is the engineer responsible for authentication?"
```

Claude uses `company_search` + `people_profile_search` → provides context

### 4. HR Self-Service

**Scenario**: Employee has benefits question

```
Prompt to Claude:
"What's our parental leave policy? And who should I contact 
to start the process?"
```

Claude uses `company_search` + `people_profile_search` → answers fully

---

## Advanced: Custom MCP Tool Development

You can extend Glean's MCP server with custom tools:

```typescript
// custom-glean-tool.ts
import { Tool } from '@modelcontextprotocol/sdk';

export const customSearchTool: Tool = {
  name: 'glean_search_with_filter',
  description: 'Search Glean with pre-configured filters for specific use case',
  inputSchema: {
    type: 'object',
    properties: {
      query: { type: 'string' },
      useCase: { 
        type: 'string',
        enum: ['engineering', 'sales', 'hr', 'finance']
      }
    },
    required: ['query', 'useCase']
  },
  execute: async ({ query, useCase }) => {
    // Map use case to datasource filters
    const filters = {
      engineering: ['github', 'confluence-engineering'],
      sales: ['salesforce', 'gong', 'confluence-sales'],
      hr: ['workday', 'confluence-hr'],
      finance: ['netsuite', 'expensify']
    };

    // Call Glean API with filters
    const results = await gleanClient.search({
      query,
      datasources: filters[useCase]
    });

    return results;
  }
};
```

---

## Troubleshooting

### MCP Server Not Appearing

1. **Check configuration**
   ```bash
   # macOS
   cat ~/Library/Application\ Support/Claude/claude_desktop_config.json
   
   # Validate JSON
   cat ~/Library/Application\ Support/Claude/claude_desktop_config.json | jq .
   ```

2. **Verify credentials**
   ```bash
   # Test API token
   curl -H "Authorization: Bearer YOUR_TOKEN" \
     https://your-company-be.glean.com/api/v1/ping
   ```

3. **Check logs**
   - Claude Desktop: Help → View Logs
   - Look for MCP initialization errors

### Permission Errors

**Error**: "User not authorized to access document"

**Solution**: Ensure your Glean user has access to the requested content. MCP respects all Glean permissions.

### Rate Limiting

**Error**: "Rate limit exceeded"

**Solution**: 
- Reduce query frequency
- Use batch operations where possible
- Contact Glean support for rate limit increase

### Slow Responses

**Optimization tips**:
- Use `maxResults` to limit search results
- Add specific `datasources` filters
- Consider remote MCP server for better performance

---

## Best Practices

### 1. Prompt Engineering

**Good prompts:**
- "Search Glean for X and summarize the top 3 results"
- "Find the document about Y and extract the section on Z"
- "Who works on team X? Then search for their recent projects"

**Avoid:**
- Overly broad: "Tell me everything about the company"
- Too vague: "Find some stuff about products"

### 2. Security

- Never expose API tokens in client-side code
- Use environment variables or secret management
- Rotate tokens regularly
- Audit MCP usage via Glean admin console

### 3. Performance

- Cache frequently accessed documents
- Use specific searches over broad queries
- Leverage datasource filters to reduce search scope
- Monitor API usage and optimize

### 4. User Experience

- Set expectations about response times
- Provide fallbacks when Glean is unavailable
- Show citations so users can verify information
- Collect feedback to improve relevance

---

## Next Steps

1. **Try the examples** - Use the prompts above in Claude
2. **Build a custom integration** - Create your own MCP client
3. **Explore the API** - Read [developers.glean.com](https://developers.glean.com)
4. **Join the community** - Share your use cases and learnings

**Need help?**
- Docs: https://docs.glean.com
- Support: support@glean.com
- GitHub: https://github.com/gleanwork/mcp-server
