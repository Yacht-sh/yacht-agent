# Security

## Secrets

- Enrollment tokens are shared secrets. Rotate them separately from agent bearer tokens.
- Agent bearer tokens are stored in `/config/agent-state.json`. Protect that volume/directory.
- If an agent token is suspected compromised, rotate it via `POST /agents/rotate-token` or remove the state file and re-enroll.

## Network

- Prefer TLS for `YACHT_SERVER_URL`.
- Set `YACHT_AGENT_VERIFY_SSL=false` only for testing with self-signed certs.

## Least Privilege

- The agent only needs access to the Docker socket.
- Avoid exposing the Docker API externally; let the agent handle host communication.
