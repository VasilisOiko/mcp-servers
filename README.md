# MCP Server Docker Setup

This repository runs a set of MCP (Model Context Protocol) servers as
plain Docker Compose services, using the same published images that
Docker Desktop's MCP Toolkit runs under the hood — without depending on
the Toolkit itself.

## Included Servers

| Service | Image | Secrets | Purpose |
|---|---|---|---|
| `context7-mcp` | `mcp/context7` | none | Up-to-date library documentation and usage examples |
| `grafana-mcp` | `mcp/grafana` | [grafana-mcp/.env](./grafana-mcp/.env.example) | Dashboards, Loki logs, Prometheus metrics, alerts, profiling |
| `mongodb-mcp` | `mcp/mongodb` | [mongodb-mcp/.env](./mongodb-mcp/.env.example) | Query and inspect a MongoDB deployment |
| `sonarqube-mcp` | `mcp/sonarqube` | [sonarqube-mcp/.env](./sonarqube-mcp/.env.example) | Static code analysis and code quality checks |
| `playwright-mcp` | `mcp/playwright` | [playwright-mcp/.env](./playwright-mcp/.env.example) (optional) | Browser automation / UI testing |

Each service that needs secrets keeps its own gitignored `.env` file
alongside an `.env.example` documenting the required keys — nothing is
shared between services, and `docker-compose.yml` only orchestrates.

## Quick Start

See [QUICK_START.md](./QUICK_START.md) for the fastest way to get started.

```bash
# Fill in each service's real secret values first (see below), then:
docker compose up -d
docker compose ps
```

## Secrets

Real values are **not** stored in this repo. Fill in each `.env` before
starting the corresponding service:

```bash
# grafana-mcp/.env
GRAFANA_API_KEY=...

# mongodb-mcp/.env
MDB_MCP_CONNECTION_STRING=...

# sonarqube-mcp/.env
SONARQUBE_TOKEN=...

# playwright-mcp/.env (optional, disposable test-user credentials)
TEST_USER_A_EMAIL=...
TEST_USER_A_PASSWORD=...
TEST_USER_B_EMAIL=...
TEST_USER_B_PASSWORD=...
```

`context7-mcp` needs no secrets.

## Integration with Cursor IDE / Claude Code

For complete MCP configuration instructions, see
[MCP_CONFIGURATION.md](./MCP_CONFIGURATION.md).

### Quick Configuration

Add to `~/.cursor/mcp.json`:

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

For Claude Code:

```bash
claude mcp add context7 --scope user -- docker exec -i context7-mcp-server node dist/index.js
claude mcp add grafana --scope user -- docker exec -i grafana-mcp-server /app/mcp-grafana --transport stdio
claude mcp add mongodb --scope user -- docker exec -i mongodb-mcp-server mongodb-mcp-server
claude mcp add sonarqube --scope user -- docker exec -i sonarqube-mcp-server java -jar /app/sonarqube-mcp-server.jar
claude mcp add playwright --scope user -- docker exec -i playwright-mcp-server node /app/cli.js --headless --browser chromium --no-sandbox
```

(Check `claude mcp add --help` first — flags have moved between versions.)

## Project Structure

```
MCP-servers/
├── grafana-mcp/
│   ├── .env            # gitignored — real secret
│   └── .env.example
├── mongodb-mcp/
│   ├── .env
│   └── .env.example
├── sonarqube-mcp/
│   ├── .env
│   └── .env.example
├── playwright-mcp/
│   ├── .env             # optional test-user creds
│   └── .env.example
├── docker-compose.yml
├── README.md
├── QUICK_START.md
└── MCP_CONFIGURATION.md
```

## Notes

- MCP servers communicate via stdio, so the `-i` flag is required when
  `docker exec`-ing into a container, and `stdin_open`/`tty` are kept on
  in compose.
- `playwright-mcp` gets a second `extra_hosts` alias
  (`app-user-b.local:host-gateway`) so a second origin durably resolves
  to the host machine — needed for testing two logged-in sessions at
  once. It survives container recreation, unlike a manual `/etc/hosts`
  patch.
- Images are pinned by digest and pulled directly from Docker Hub; there's
  no `build:`/`Dockerfile` for any of these services. Re-pull manually
  (`docker compose pull`) to update.
