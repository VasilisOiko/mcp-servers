# MCP Server Docker Setup

This repository contains a Docker setup for running the MCP (Model Context Protocol) server for Ant Design components.

## Included Server

**Ant Design MCP Server** - [hannesj/mcp-antd-components](https://github.com/hannesj/mcp-antd-components)

## Quick Start

See [QUICK_START.md](./QUICK_START.md) for the fastest way to get started.

### Build and Start as Persistent Service

```bash
# Build the image
docker compose build antd-mcp

# Start the container in detached mode (runs in background)
docker compose up -d antd-mcp
```

The container runs persistently and can be accessed via Docker exec for MCP communication.

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

To use this MCP server with Cursor, see [QUICK_START.md](./QUICK_START.md) for detailed instructions.

### Quick Configuration

Add to `~/.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "antd-components": {
      "command": "docker",
      "args": ["exec", "-i", "antd-mcp-server", "npm", "start"]
    }
  }
}
```

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
├── docker-compose.yml
├── README.md
└── QUICK_START.md
```

## Notes

- Each MCP server has its own Dockerfile and Docker image
- MCP servers communicate via stdio (standard input/output), so the `-i` flag is important when running with Docker
- The servers don't require exposed ports for stdio communication
- Data directories can be mounted as volumes for persistence
- Images can be built and run independently
