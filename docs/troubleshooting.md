# Troubleshooting

## Registration fails

- Verify `YACHT_SERVER_URL` is reachable from the agent host.
- Verify the enrollment token matches the server-side value.
- Check server logs for enrollment errors.

## Heartbeat rejected

- The agent will automatically re-register if the server rejects the token.
- If this loops, verify the agent host was not deleted/revoked in Yacht.
- If the host was deleted, re-enroll with a new host name.

## Compose jobs fail

- Ensure the agent image has the Docker Compose plugin.
- Ensure `working_dir` contains a valid compose file if provided.
- Check agent logs for `docker compose` command failures.

## State file issues

- If the agent behaves unexpectedly, inspect `/config/agent-state.json`.
- To force re-registration: remove the state file and restart the agent container.
