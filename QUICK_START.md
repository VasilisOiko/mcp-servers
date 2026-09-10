# Quick Start

## 1. Fill in secrets

Each service that needs one keeps its own gitignored `.env` file. Copy the
`.env.example` in each directory and fill in real values:

```bash
cd /Users/vasilisoikonomou/Projects/MCP-servers

# grafana-mcp/.env      → GRAFANA_API_KEY=
# mongodb-mcp/.env      → MDB_MCP_CONNECTION_STRING=
# sonarqube-mcp/.env    → SONARQUBE_TOKEN=
# playwright-mcp/.env   → TEST_USER_A_EMAIL / _PASSWORD, TEST_USER_B_EMAIL / _PASSWORD (optional)
```

`context7-mcp` needs no secrets.

## 2. Start the services

```bash
docker compose up -d
docker compose ps
```

Or start a subset:

```bash
docker compose up -d context7-mcp grafana-mcp
```

## 3. Verify each one starts and responds

```bash
docker exec -i context7-mcp-server node dist/index.js &
docker exec -i grafana-mcp-server /app/mcp-grafana --transport stdio &
docker exec -i mongodb-mcp-server mongodb-mcp-server &
docker exec -i sonarqube-mcp-server java -jar /app/sonarqube-mcp-server.jar &
docker exec -i playwright-mcp-server node /app/cli.js --headless --browser chromium --no-sandbox &
```

Each should sit there waiting on stdio rather than exiting immediately —
an immediate exit usually means a bad env var or unreachable URL. Kill
them once confirmed (`kill %1 %2 %3 %4 %5`); the real connection happens
through the MCP client, not this manual check.

## 4. Configure Cursor / Claude Code

See [MCP_CONFIGURATION.md](./MCP_CONFIGURATION.md) for the full `mcp.json`
/ `claude mcp add` configuration for all five servers.

## Managing the services

```bash
docker compose up -d               # start everything
docker compose stop                # stop everything
docker compose restart <service>   # restart one service
docker compose logs -f <service>   # tail logs
docker compose pull <service>      # update to the latest pinned digest's image
```

## Troubleshooting

### Container won't start
```bash
docker compose logs <service>
```
Usually a missing/incorrect value in that service's `.env`.

### Cursor / Claude Code can't connect
- Confirm the container is running: `docker compose ps`
- Confirm the container name matches what's in `mcp.json` /
  `claude mcp list` (e.g. `context7-mcp-server`, `grafana-mcp-server`, …)
- Restart Cursor / Claude Code after editing `mcp.json`
