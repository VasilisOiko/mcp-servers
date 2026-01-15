# MCP Server Docker Setup

This repository contains a Docker setup for running the MCP (Model Context Protocol) server for Ant Design components.

## Included Server

**Ant Design MCP Server** - [hannesj/mcp-antd-components](https://github.com/hannesj/mcp-antd-components)

## Quick Start

### Build Individual Docker Images

#### Ant Design MCP Server

```bash
cd antd-mcp
docker build -t antd-mcp-server .
```

### Run Individual Servers

#### Ant Design MCP Server

```bash
docker run -it -p 10000:3000 antd-mcp-server
```

## Using Docker Compose

### Build and start the server

```bash
docker-compose build
docker-compose up
```

The server will be accessible on:
- **Ant Design MCP**: `localhost:10000`

## Ant Design Documentation Extraction

The Ant Design MCP server requires documentation to be extracted from the Ant Design repository before use. You can do this in two ways:

### Option 1: Extract during build (uncomment in Dockerfile)

Edit `antd-mcp/Dockerfile` and uncomment the extraction step, then rebuild:

```dockerfile
RUN git clone https://github.com/ant-design/ant-design.git /tmp/ant-design && \
    npm run extract /tmp/ant-design
```

Then rebuild:
```bash
cd antd-mcp
docker build -t antd-mcp-server .
```

### Option 2: Extract at runtime

```bash
# Run a temporary container to extract docs
docker run -it --rm -v $(pwd)/antd-data:/app/data antd-mcp-server sh -c "
  git clone https://github.com/ant-design/ant-design.git /tmp/ant-design && \
  npm run extract /tmp/ant-design
"

# Then run with the extracted data mounted
docker run -it -v $(pwd)/antd-data:/app/data antd-mcp-server
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
      "args": ["run", "-i", "antd-mcp-server"]
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
└── README.md
```

## Notes

- Each MCP server has its own Dockerfile and Docker image
- MCP servers communicate via stdio (standard input/output), so the `-i` flag is important when running with Docker
- The servers don't require exposed ports for stdio communication
- Data directories can be mounted as volumes for persistence
- Images can be built and run independently
