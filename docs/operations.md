# Operations

## Prerequisites

- A Yacht server reachable from the agent host
- An enrollment token configured on the Yacht server
- Docker Engine on the agent host
- Optional: Docker Compose plugin if compose actions are needed

## Deployment

Example `agent/docker-compose.yaml`:

```yaml
services:
  yacht-agent:
    image: ghcr.io/wickedyoda/yacht-agent:latest
    container_name: yacht-agent
    restart: unless-stopped
    environment:
      YACHT_SERVER_URL: https://yacht.example.com
      YACHT_AGENT_ENROLLMENT_TOKEN: replace-with-your-enrollment-token
      YACHT_AGENT_NAME: remote-docker-host
      YACHT_AGENT_VERIFY_SSL: "true"
      YACHT_AGENT_HEARTBEAT_INTERVAL: "30"
      YACHT_AGENT_JOB_POLL_INTERVAL: "5"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - ./config:/config
```

Start the agent:

```bash
docker compose -f agent/docker-compose.yaml up -d
```

## Logs

```bash
docker logs -f yacht-agent
```

Successful startup shows:
- registration accepted
- heartbeat accepted
- inventory sync accepted

## Upgrading

Rebuild or pull a newer image, then restart the container. The agent preserves state in `/config/agent-state.json`.
