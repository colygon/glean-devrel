# MCP Quick Start Guide

Learn how to set up and use Model Context Protocol (MCP) with Glean in under 10 minutes.

## What is MCP?

Model Context Protocol (MCP) is an open standard that enables AI assistants to securely connect to external data sources and tools. With MCP, Glean's AI can:

- Query your databases
- Access your APIs
- Read documentation
- Interact with third-party services
- Execute complex workflows

## Prerequisites

- Glean terminal installed
- Basic familiarity with command line
- Node.js 16+ or Python 3.8+ (depending on MCP server)

## Installing Your First MCP Server

Let's install a simple MCP server to get started.

### Option 1: Using Glean Marketplace (Easiest)

1. Open Glean
2. Press `Cmd/Ctrl + P` to open Command Palette
3. Type "MCP Marketplace"
4. Browse available servers
5. Click "Install" on any server
6. Follow the configuration wizard

### Option 2: Manual Installation

**Install a GitHub MCP Server:**

```bash
# Clone the MCP server
git clone https://github.com/example/github-mcp-server
cd github-mcp-server

# Install dependencies
npm install

# Configure
cp .env.example .env
# Edit .env with your GitHub token

# Start the server
npm start
```

**Register with Glean:**

```bash
glean mcp add github-mcp ./github-mcp-server
```

## Configuring MCP

### Configuration File

Glean stores MCP configuration in `~/.glean/mcp-config.json`:

```json
{
  "servers": [
    {
      "name": "github-mcp",
      "command": "node",
      "args": ["/path/to/github-mcp-server/index.js"],
      "env": {
        "GITHUB_TOKEN": "${GITHUB_TOKEN}"
      }
    }
  ]
}
```

### Environment Variables

Store sensitive data like API keys in environment variables:

```bash
# Add to your ~/.zshrc or ~/.bashrc
export GITHUB_TOKEN="your_token_here"
export OPENAI_API_KEY="your_key_here"
```

Reload your shell:
```bash
source ~/.zshrc
```

## Using MCP with Glean AI

Once configured, MCP servers enhance Glean's AI capabilities.

### Example: Querying GitHub

**Ask Glean AI:**
```
"Show me my recent pull requests"
```

Glean will:
1. Use the GitHub MCP server
2. Fetch your PRs
3. Display results in a clean format

### Example: Database Queries

With a PostgreSQL MCP server:

**Ask Glean AI:**
```
"Show users who signed up in the last 7 days"
```

Glean generates and executes the SQL query safely.

## Available MCP Servers

### Popular Servers

**Development Tools:**
- `github-mcp` - GitHub API integration
- `gitlab-mcp` - GitLab operations
- `docker-mcp` - Docker container management

**Databases:**
- `postgres-mcp` - PostgreSQL queries
- `mysql-mcp` - MySQL operations  
- `mongodb-mcp` - MongoDB queries

**Cloud Services:**
- `aws-mcp` - AWS resource management
- `gcp-mcp` - Google Cloud operations
- `azure-mcp` - Azure integration

**Productivity:**
- `notion-mcp` - Notion workspace access
- `slack-mcp` - Slack messaging
- `jira-mcp` - Issue tracking

Browse all servers: [marketplace.glean.dev](https://marketplace.glean.dev)

## Managing MCP Servers

### List Installed Servers

```bash
glean mcp list
```

### Start a Server

```bash
glean mcp start github-mcp
```

### Stop a Server

```bash
glean mcp stop github-mcp
```

### Update a Server

```bash
glean mcp update github-mcp
```

### Remove a Server

```bash
glean mcp remove github-mcp
```

### View Logs

```bash
glean mcp logs github-mcp
```

## Building Your First MCP Server

Let's create a simple MCP server that provides system information.

### Step 1: Initialize Project

```bash
mkdir my-system-mcp
cd my-system-mcp
npm init -y
npm install @modelcontextprotocol/sdk
```

### Step 2: Create Server

Create `index.js`:

```javascript
const { MCPServer } = require('@modelcontextprotocol/sdk');

const server = new MCPServer({
  name: 'system-info',
  version: '1.0.0',
});

// Register a tool
server.tool('get_system_info', {
  description: 'Get current system information',
  parameters: {},
  handler: async () => {
    const os = require('os');
    return {
      platform: os.platform(),
      arch: os.arch(),
      cpus: os.cpus().length,
      memory: {
        total: os.totalmem(),
        free: os.freemem(),
      },
      uptime: os.uptime(),
    };
  },
});

server.start();
```

### Step 3: Add to Glean

```bash
glean mcp add my-system ~/.../my-system-mcp/index.js
glean mcp start my-system
```

### Step 4: Test

Ask Glean AI: "What's my system information?"

## Security Best Practices

### 1. Environment Variables

Never hardcode secrets:

```javascript
// ❌ Bad
const apiKey = 'sk-1234567890';

// ✅ Good
const apiKey = process.env.GITHUB_TOKEN;
```

### 2. Validate Inputs

Always validate and sanitize inputs:

```javascript
server.tool('query_database', {
  handler: async ({ query }) => {
    // Validate query
    if (!isValidSQL(query)) {
      throw new Error('Invalid query');
    }
    // Execute safely
  },
});
```

### 3. Least Privilege

Grant minimum necessary permissions:

```json
{
  "permissions": [
    "filesystem:read",  // ✅ Specific
    "network:https"     // ❌ Avoid "network:*"
  ]
}
```

### 4. Audit Logs

Log all MCP operations:

```bash
# Enable MCP logging
export GLEAN_MCP_LOG_LEVEL=debug
```

## Troubleshooting

### Server Won't Start

**Check configuration:**
```bash
glean mcp config validate
```

**View logs:**
```bash
glean mcp logs server-name
```

**Common issues:**
- Missing environment variables
- Port already in use
- Incorrect file paths

### Server Not Responding

**Restart the server:**
```bash
glean mcp restart server-name
```

**Check process:**
```bash
ps aux | grep mcp
```

### Permission Errors

**Review permissions in config:**
```bash
cat ~/.glean/mcp-config.json
```

**Update permissions:**
Edit the config file and restart Glean.

## Advanced Topics

### Custom Protocols

MCP supports custom protocols for specialized needs:

```javascript
server.protocol('custom_action', {
  version: '1.0',
  handler: async (request) => {
    // Handle custom protocol
  },
});
```

### Streaming Responses

For long-running operations:

```javascript
server.tool('long_operation', {
  handler: async function* ({ params }) {
    for (let i = 0; i < 100; i++) {
      yield { progress: i };
      await doWork();
    }
    return { result: 'complete' };
  },
});
```

### Error Handling

Implement proper error handling:

```javascript
server.tool('risky_operation', {
  handler: async (params) => {
    try {
      return await operation(params);
    } catch (error) {
      // Log error
      console.error(error);
      // Return user-friendly message
      throw new MCPError('Operation failed', {
        code: 'OPERATION_FAILED',
        details: error.message,
      });
    }
  },
});
```

## Next Steps

### Learn More

- 📖 [MCP Specification](https://spec.modelcontextprotocol.io)
- 🛠️ [Server Development Guide](https://docs.glean.dev/mcp/development)
- 🔒 [Security Best Practices](https://docs.glean.dev/mcp/security)
- 🌟 [Example Servers](https://github.com/gleandotdev/mcp-examples)

### Build Something

- Create an MCP server for your favorite API
- Submit it to the Glean Marketplace
- Apply for a bounty or grant

### Join the Community

- Discord: #mcp-development channel
- Weekly workshops on MCP
- Office hours every Friday

## Resources

- **Marketplace**: [marketplace.glean.dev](https://marketplace.glean.dev)
- **Documentation**: [docs.glean.dev/mcp](https://docs.glean.dev/mcp)
- **GitHub**: [github.com/gleandotdev/mcp](https://github.com/gleandotdev/mcp)
- **Examples**: [github.com/gleandotdev/mcp-examples](https://github.com/gleandotdev/mcp-examples)

---

**Ready to supercharge Glean with MCP! 🚀**
