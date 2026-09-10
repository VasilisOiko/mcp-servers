# MCP Server Configuration Guide

How to point Cursor / Claude Code at the five containers managed by this
repo's `docker-compose.yml`: `context7-mcp`, `grafana-mcp`, `mongodb-mcp`,
`sonarqube-mcp`, `playwright-mcp`.

## Prerequisites

- Docker installed and running
- Each service's secrets filled in (see [QUICK_START.md](./QUICK_START.md))
- All five containers built and running: `docker compose up -d`

## Exec commands per server

`docker exec` does not honor a container's `ENTRYPOINT`/`CMD`, so the
exact command must be given explicitly:

| Server | Container | Exec command |
|---|---|---|
| context7 | `context7-mcp-server` | `node dist/index.js` |
| grafana | `grafana-mcp-server` | `/app/mcp-grafana --transport stdio` |
| mongodb | `mongodb-mcp-server` | `mongodb-mcp-server` |
| sonarqube | `sonarqube-mcp-server` | `java -jar /app/sonarqube-mcp-server.jar` |
| playwright | `playwright-mcp-server` | `node /app/cli.js --headless --browser chromium --no-sandbox` |

## Cursor MCP Configuration

**Config file location:**
- macOS/Linux: `~/.cursor/mcp.json`
- Windows: `%USERPROFILE%\.cursor\mcp.json`

```json
{
  "mcpServers": {
    "context7": { "command": "docker", "args": ["exec", "-i", "context7-mcp-server", "node", "dist/index.js"] },
    "grafana": { "command": "docker", "args": ["exec", "-i", "grafana-mcp-server", "/app/mcp-grafana", "--transport", "stdio"] },
    "mongodb": { "command": "docker", "args": ["exec", "-i", "mongodb-mcp-server", "mongodb-mcp-server"] },
    "sonarqube": { "command": "docker", "args": ["exec", "-i", "sonarqube-mcp-server", "java", "-jar", "/app/sonarqube-mcp-server.jar"] },
    "playwright": { "command": "docker", "args": ["exec", "-i", "playwright-mcp-server", "node", "/app/cli.js", "--headless", "--browser", "chromium", "--no-sandbox"] }
  }
}
```

`-i` keeps stdin open, which MCP's stdio transport requires.

## Claude Code Configuration

```bash
claude mcp add context7 --scope user -- docker exec -i context7-mcp-server node dist/index.js
claude mcp add grafana --scope user -- docker exec -i grafana-mcp-server /app/mcp-grafana --transport stdio
claude mcp add mongodb --scope user -- docker exec -i mongodb-mcp-server mongodb-mcp-server
claude mcp add sonarqube --scope user -- docker exec -i sonarqube-mcp-server java -jar /app/sonarqube-mcp-server.jar
claude mcp add playwright --scope user -- docker exec -i playwright-mcp-server node /app/cli.js --headless --browser chromium --no-sandbox
```

Check `claude mcp add --help` first — flags have moved between versions.

## After Configuration

1. Restart Cursor / Claude Code completely to load the new MCP servers.
2. Verify each server responds (see the manual `docker exec` check in
   [QUICK_START.md](./QUICK_START.md)) before relying on it.

## Troubleshooting

### Container issues
See [QUICK_START.md](./QUICK_START.md) for start/stop/logs/rebuild
commands.

### Server not found in Cursor / Claude Code
1. Confirm containers are running: `docker compose ps`
2. Confirm container names match exactly (`context7-mcp-server`,
   `grafana-mcp-server`, `mongodb-mcp-server`, `sonarqube-mcp-server`,
   `playwright-mcp-server`)
3. Check `mcp.json` JSON syntax is valid
4. Restart Cursor / Claude Code completely

### A server exits immediately instead of waiting on stdio
Its `.env` is likely missing/wrong (bad API key, unreachable URL, malformed
connection string) — check `docker compose logs <service>`.
