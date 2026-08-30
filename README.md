# Yacht Agent

Lightweight agent for managing remote Docker hosts with [Yacht](https://github.com/Yacht-sh/Yacht).

## Overview

The Yacht Agent is a lightweight Python service that runs on remote Docker hosts and communicates with a central Yacht server via REST API. It enables Yacht to:

- **Register** with a Yacht server using a shared enrollment token
- **Heartbeat** periodically to report status
- **Sync inventory** of containers, images, volumes, networks, and compose projects
- **Execute queued jobs** — container actions (start/stop/restart/kill/remove) and compose actions (up/down/pull)

## Deployment

### Quick Start

```bash
# 1. Copy the env template and configure
cp agent/.env.example agent/.env
# Edit agent/.env with your Yacht server URL and enrollment token

# 2. Run with the provided docker-compose
docker compose -f agent/docker-compose.yaml up -d
```

### Environment Variables

| Variable | Required | Description |
|----------|----------|-------------|
| `YACHT_SERVER_URL` | Yes | Base URL of your Yacht server (e.g., `https://yacht.example.com`) |
| `YACHT_AGENT_ENROLLMENT_TOKEN` | Yes | Enrollment token configured on the Yacht server |
| `YACHT_AGENT_NAME` | No | Display name for this agent host |
| `YACHT_AGENT_VERIFY_SSL` | No | Set to `false` to skip SSL verification (self-signed certs) |
| `YACHT_AGENT_HEARTBEAT_INTERVAL` | No | Heartbeat interval in seconds (default: 30) |
| `YACHT_AGENT_JOB_POLL_INTERVAL` | No | Job polling interval in seconds (default: 5) |
| `YACHT_AGENT_VERSION` | No | Agent version string for tracking |

### Docker Socket

The agent requires access to the Docker socket:
```yaml
volumes:
  - /var/run/docker.sock:/var/run/docker.sock
```

## Architecture

```
Yacht Server ←→ Yacht Agent ←→ Docker Socket
  (REST API)    (registration,
                 heartbeat, inventory,
                 job execution)
```

### Agent Lifecycle

1. **Registration** — On startup, the agent registers with the Yacht server using the enrollment token and receives a `host_id`
2. **Inventory Sync** — The agent sends its Docker inventory (containers, images, volumes, networks, compose projects) to the server
3. **Heartbeat** — The agent periodically sends heartbeats to report its status
4. **Job Execution** — The agent polls for queued jobs and executes them (compose actions via `docker compose` CLI, container actions via Docker API)

### Job Types

| Type | Actions | Description |
|------|---------|-------------|
| `container_action` | `start`, `stop`, `restart`, `kill`, `remove` | Manage individual containers |
| `compose_action` | `up`, `down`, `pull` | Manage docker-compose projects |

## Building

```bash
docker build -t ghcr.io/yacht-sh/yacht-agent:latest .
```

## License

GNU General Public License v3.0
