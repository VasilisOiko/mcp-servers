# Quick Start: Deploy Ant Design MCP Server in Docker

## Deploy as Persistent Docker Service

### 1. Build and Start the Container

```bash
cd /Users/vasilisoikonomou/Projects/MCP-servers

# Build the image
docker compose build antd-mcp

# Start the container in detached mode (runs in background)
docker compose up -d antd-mcp
```

### 2. Verify It's Running

```bash
# Check container status
docker ps | grep antd-mcp-server

# View logs
docker compose logs antd-mcp

# Test the server
docker exec -i antd-mcp-server sh -c "test -d /app/data && echo '✅ Server ready'"
```

### 3. Configure Cursor

Edit `~/.cursor/mcp.json` (create if it doesn't exist):

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

### 4. Restart Cursor

Restart Cursor IDE to load the MCP server.

## Managing the Service

```bash
# Start the service
docker compose up -d antd-mcp

# Stop the service
docker compose stop antd-mcp

# Restart the service
docker compose restart antd-mcp

# View logs
docker compose logs -f antd-mcp

# Remove the container (keeps the image)
docker compose down antd-mcp
```

## Important Notes

- **MCP uses stdio**: The MCP protocol communicates via standard input/output, not HTTP
- **Persistent container**: The container runs in the background and Cursor connects to it via `docker exec`
- **Port 10000**: Exposed but not used for MCP communication (stdio is used instead)
- **Auto-restart**: The container will automatically restart if it stops (unless you stop it manually)

## Troubleshooting

### Container won't start
```bash
# Check logs
docker compose logs antd-mcp

# Rebuild if needed
docker compose build --no-cache antd-mcp
```

### Cursor can't connect
- Make sure the container is running: `docker ps | grep antd-mcp`
- Check the container name matches: `antd-mcp-server`
- Verify Docker is accessible from Cursor

### Rebuild after changes
```bash
docker compose down antd-mcp
docker compose build --no-cache antd-mcp
docker compose up -d antd-mcp
```
