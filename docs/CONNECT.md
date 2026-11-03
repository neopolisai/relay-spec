# Connect Relay to your agent

Relay is an MCP connector at **https://relayto.ai/mcp**. Add it once. The first time, you sign in to Relay and allow the connection. Then say "relay … to … for …" in your normal conversation.

| Agent | How |
|---|---|
| Claude (web or desktop) | Settings → Connectors → Add custom connector, paste `https://relayto.ai/mcp` |
| Claude Code | `claude mcp add --transport http relay https://relayto.ai/mcp` |
| ChatGPT | Settings → Apps & Connectors → add a custom connector with `https://relayto.ai/mcp` |
| Codex, Cursor and other MCP agents | Add the same address as a remote (HTTP) MCP server |

Custom connectors depend on your plan and workspace settings in each agent. If you can't add one, you can still read any relay in a browser and press **Relay to my agent**.

Ask your agent "relay, what can I do?" or see https://relayto.ai/commands for every command.
