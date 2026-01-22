# MCP Server Configuration Guide

This guide explains how to configure the MCP servers (Ant Design and BullMQ) in Cursor IDE.

## Prerequisites

- Docker installed and running
- Cursor IDE installed
- Both MCP server containers built and running

## Build and Start Services

For detailed build and deployment instructions, see [QUICK_START.md](./QUICK_START.md).

**Quick reference:**
```bash
cd /Users/vasilisoikonomou/Projects/MCP-servers
docker compose build
docker compose up -d
docker compose ps  # Verify both are running
```

You should see both `antd-mcp-server` and `bullmq-mcp-server` running.

## Cursor MCP Configuration

### Configuration File Location

The MCP configuration file location depends on your operating system:

- **macOS/Linux**: `~/.cursor/mcp.json`
- **Windows**: `%USERPROFILE%\.cursor\mcp.json`

### Create or Edit the Configuration File

1. **Create the directory** (if it doesn't exist):
   ```bash
   # macOS/Linux
   mkdir -p ~/.cursor
   
   # Windows (PowerShell)
   New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.cursor"
   ```

2. **Create or edit the `mcp.json` file** with the following configuration:

```json
{
  "mcpServers": {
    "antd-components": {
      "command": "docker",
      "args": ["exec", "-i", "antd-mcp-server", "npm", "start"]
    },
    "bullmq": {
      "command": "docker",
      "args": ["exec", "-i", "bullmq-mcp-server", "npx", "-y", "@adamhancock/bullmq-mcp"]
    }
  }
}
```

### Configuration Explanation

#### Ant Design MCP Server
- **Name**: `antd-components`
- **Command**: `docker exec -i antd-mcp-server npm start`
- **Purpose**: Provides access to Ant Design component documentation, props, and examples

#### BullMQ MCP Server
- **Name**: `bullmq`
- **Command**: `docker exec -i bullmq-mcp-server npx -y @adamhancock/bullmq-mcp`
- **Purpose**: Provides tools for managing BullMQ job queues

**Important flags:**
- `-i`: Keeps stdin open (required for MCP stdio communication)
- `exec`: Runs a command in the running container
- `-y` (for BullMQ): Automatically answers yes to npx prompts

## Complete Example Configuration

If you have other MCP servers configured, your `mcp.json` might look like this:

```json
{
  "mcpServers": {
    "shadcn": {
      "command": "npx",
      "args": ["shadcn@latest", "mcp"]
    },
    "zustand Docs": {
      "url": "https://gitmcp.io/pmndrs/zustand"
    },
    "antd-components": {
      "command": "docker",
      "args": ["exec", "-i", "antd-mcp-server", "npm", "start"]
    },
    "bullmq": {
      "command": "docker",
      "args": ["exec", "-i", "bullmq-mcp-server", "npx", "-y", "@adamhancock/bullmq-mcp"]
    }
  }
}
```

## After Configuration

1. **Restart Cursor IDE** completely (quit and reopen) to load the new MCP servers
2. **Verify the servers are loaded** by checking Cursor's MCP status or trying to use the tools

## Testing the Configuration

### Test Ant Design MCP Server

Ask Cursor questions like:
- "What Ant Design components are available?"
- "Show me the Button component documentation"
- "What props does the Table component accept?"
- "Show me code examples for the Modal component"

### Test BullMQ MCP Server

Ask Cursor questions like:
- "List all BullMQ queues"
- "Show me the status of a queue"
- "What tools are available for BullMQ?"

## Troubleshooting

### Container Issues

For container-related troubleshooting (won't start, can't connect, rebuild issues), see [QUICK_START.md](./QUICK_START.md).

### Server Not Found in Cursor

If Cursor can't find the servers:

1. **Verify containers are running**:
   ```bash
   docker ps | grep -E "antd-mcp|bullmq-mcp"
   ```

2. **Check container names match**:
   - `antd-mcp-server`
   - `bullmq-mcp-server`

3. **Verify Docker is accessible** from Cursor

4. **Check JSON syntax** is valid (use a JSON validator)

### Configuration Not Loading

- Make sure the `mcp.json` file is in the correct location
- Verify the JSON syntax is valid (no trailing commas, proper quotes)
- Restart Cursor completely (quit and reopen)
- Check Cursor's logs for MCP-related errors

### BullMQ Package Name Issues

If the BullMQ server fails to start, the package name might be different. Check the [LobeChat deployment page](https://lobechat.com/discover/mcp/adamhancock-bullmq-mcp?activeTab=deployment) for the correct package name and update:

1. The `CMD` in `bullmq-mcp/Dockerfile`
2. The `args` in your `mcp.json` configuration

## Managing the Services

For service management commands (start, stop, restart, logs, rebuild), see [QUICK_START.md](./QUICK_START.md).

**Quick reference:**
- Start: `docker compose up -d`
- Stop: `docker compose stop`
- Restart: `docker compose restart`
- Logs: `docker compose logs -f [service-name]`
- Rebuild: `docker compose down && docker compose build --no-cache && docker compose up -d`

## Available MCP Tools

### Ant Design MCP Server Tools
- `list-components`: Lists all available Ant Design components
- `get-component-props`: Gets props and API documentation for a component
- `get-component-docs`: Gets detailed documentation for a component
- `list-component-examples`: Lists examples for a component
- `get-component-example`: Gets code for a specific example
- `search-components`: Search for components by name pattern

### BullMQ MCP Server Tools
Refer to the [BullMQ MCP documentation](https://lobechat.com/discover/mcp/adamhancock-bullmq-mcp) for available tools. Common tools include:
- Queue management
- Job status and monitoring
- Queue statistics
- Job operations (add, remove, retry, etc.)

## Notes

For important notes about MCP communication (stdio, persistent containers, ports), see [QUICK_START.md](./QUICK_START.md).

**Configuration-specific notes:**
- The `-i` flag is **required** for Docker exec to keep stdin open
- Container names must match exactly: `antd-mcp-server` and `bullmq-mcp-server`

## Additional Resources

- [Ant Design MCP Server](https://github.com/hannesj/mcp-antd-components)
- [BullMQ MCP Server](https://lobechat.com/discover/mcp/adamhancock-bullmq-mcp)
- [MCP Protocol Documentation](https://modelcontextprotocol.io/)
