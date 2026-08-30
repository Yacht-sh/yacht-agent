# Yacht Agent Protocol

This document describes the protocol between the Yacht server and `yacht-agent`.

## Base URL

The agent uses the Yacht server's `/api` prefix. Set `YACHT_SERVER_URL` to the base server URL; the agent automatically appends `/api` if missing.

## Endpoints

### Register

```http
POST /agents/register
Header: X-Yacht-Agent-Enrollment-Token: <token>
Body:
{
  "name": "host-name",
  "hostname": "hostname",
  "version": "0.1.0",
  "docker_version": "24.0.5",
  "capabilities": {
    "containers": true,
    "images": true,
    "volumes": true,
    "networks": true,
    "compose": true,
    "host_stats": true
  }
}
```

Response:
```json
{
  "host_id": 1,
  "agent_id": "uuid",
  "agent_token": "hex-token",
  "heartbeat_interval": 30
}
```

Store `agent_token` securely on the agent host in `/config/agent-state.json`.

### Heartbeat

```http
POST /agents/heartbeat
Header: X-Yacht-Agent-Token: <agent_token>
Body:
{
  "version": "0.1.0",
  "docker_version": "24.0.5",
  "capabilities": { ... },
  "containers_running": 3,
  "containers_total": 5,
  "host_stats": { ... }
}
```

### Inventory Sync

```http
POST /agents/sync
Header: X-Yacht-Agent-Token: <agent_token>
Body:
{
  "containers": [ ... ],
  "images": [ ... ],
  "volumes": [ ... ],
  "networks": [ ... ],
  "compose_projects": { ... }
}
```

### Job Polling

```http
GET /agents/jobs/next
Header: X-Yacht-Agent-Token: <agent_token>
```

Response:
```json
{
  "job_id": "uuid",
  "job_type": "compose_action",
  "payload": {
    "project": "myproject",
    "action": "up",
    "working_dir": "/path/to/project"
  }
}
```

### Job Result

```http
POST /agents/jobs/{job_id}/result
Header: X-Yacht-Agent-Token: <agent_token>
Body:
{
  "status": "succeeded",
  "result": { ... },
  "error": null
}
```

## Job Types

- `container_action`
  - `start`
  - `stop`
  - `restart`
  - `kill`
  - `remove`
- `compose_action`
  - `up`
  - `down`
  - `pull`

## State Management

The agent stores its issued token and identifiers in `/config/agent-state.json`. If the state file is lost or compromised, remove it and restart the agent to force re-registration.

## Token Rotation

Use `POST /agents/rotate-token` with the current agent bearer token to rotate credentials without re-enrolling.

## Error Handling

On HTTP 401/403/404 from protected endpoints, the agent clears its stored registration and re-enrolls automatically on the next loop.
