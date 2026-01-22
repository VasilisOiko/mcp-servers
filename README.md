# MCP Server Docker Setup

This repository contains a Docker setup for running multiple MCP (Model Context Protocol) servers.

## Included Servers

1. **Ant Design MCP Server** - [hannesj/mcp-antd-components](https://github.com/hannesj/mcp-antd-components)
   - Provides Ant Design component documentation, props, and examples

2. **BullMQ MCP Server** - [adamhancock/bullmq-mcp](https://lobechat.com/discover/mcp/adamhancock-bullmq-mcp)
   - Provides tools for managing BullMQ job queues

## Quick Start

See [QUICK_START.md](./QUICK_START.md) for the fastest way to get started.

### Build and Start All Services

```bash
# Build all images
docker compose build

# Start all containers in detached mode (runs in background)
docker compose up -d

# Or start specific services
docker compose up -d antd-mcp
docker compose up -d bullmq-mcp
```

The containers run persistently and can be accessed via Docker exec for MCP communication.

## Ant Design Documentation Extraction

The Ant Design documentation is automatically extracted during the Docker build process. The extracted documentation is included in the Docker image, so no additional steps are required.

**Note:** The build process will clone the Ant Design repository to extract documentation, which may take a few minutes during the initial build.

### Optional: Using Custom Extracted Data

If you prefer to extract documentation manually or use a custom version, you can mount a volume with your own extracted data:

```bash
# Extract docs manually (if needed)
docker run -it --rm -v $(pwd)/antd-data:/app/data antd-mcp-server sh -c "
  git clone https://github.com/ant-design/ant-design.git /tmp/ant-design && \
  npm run extract /tmp/ant-design
"

# Run with custom data mounted (uncomment volume in docker-compose.yml)
docker run -it -v $(pwd)/antd-data:/app/data antd-mcp-server
```

## Integration with Cursor IDE

For complete MCP configuration instructions, see [MCP_CONFIGURATION.md](./MCP_CONFIGURATION.md).

### Quick Configuration

Add to `~/.cursor/mcp.json`:

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

See [MCP_CONFIGURATION.md](./MCP_CONFIGURATION.md) for detailed setup instructions and troubleshooting.

## Integration with Claude Desktop

To use these MCP servers with Claude Desktop, configure them in your `claude_desktop_config.json`:

### macOS/Linux
`~/Library/Application Support/Claude/claude_desktop_config.json`

### Windows
`%AppData%\Claude\claude_desktop_config.json`

### Configuration Example

```json
{
  "mcpServers": {
    "Ant Design Components": {
      "command": "docker",
      "args": ["exec", "-i", "antd-mcp-server", "npm", "start"]
    },
    "BullMQ": {
      "command": "docker",
      "args": ["exec", "-i", "bullmq-mcp-server", "npx", "-y", "@adamhancock/bullmq-mcp"]
    }
  }
}
```

## Project Structure

```
MCP-servers/
├── antd-mcp/
│   ├── Dockerfile
│   └── .dockerignore
├── bullmq-mcp/
│   ├── Dockerfile
│   └── .dockerignore
├── docker-compose.yml
├── README.md
├── QUICK_START.md
└── MCP_CONFIGURATION.md
```

## Documentation

- **[README.md](./README.md)** - This file, overview of the project
- **[QUICK_START.md](./QUICK_START.md)** - Quick deployment guide
- **[MCP_CONFIGURATION.md](./MCP_CONFIGURATION.md)** - Complete MCP configuration guide with mcp.json setup

## Notes

- Each MCP server has its own Dockerfile and Docker image
- MCP servers communicate via stdio (standard input/output), so the `-i` flag is important when running with Docker
- The servers don't require exposed ports for stdio communication
- Data directories can be mounted as volumes for persistence
- Images can be built and run independently
