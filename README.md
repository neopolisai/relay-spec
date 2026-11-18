# Relay: the open context-package format

**Don't just send a doc. Send a relay.**

Their AI will probably read your doc before they do. With a relay, your agent gives their agent the context behind it, and the questions that need you come back to you.

This repository holds the open parts of [Relay](https://relayto.ai): the `relay.context-package.v1` format, the `/relay` skill your agent follows, the extraction standard, and setup steps for each agent. The Relay service at relayto.ai (links, sign-in, permissions, read receipts, versions, revocation, the question loop and the web app) is a product by [Neopolis](https://neopolis.ai) and isn't in this repository.

```
relay {what} to {who} for {why}
```

```
relay the pilot proposal to Jordan at Northstar for sign-off by Friday
```
```
ask Jordan to relay me the pilot requirements for scoping
```

## What's in a relay

Every relay has a **purpose**, a **doc** for people, and **context** for their agent.

| Part | What it is | Where it lives in the format |
|---|---|---|
| Purpose | Why you're sharing it and what you hope they'll do | `intent`, `interpretation` |
| Doc | What the person reads: a PDF, a page you write, or both | `content`, `attachments` |
| Context | What their agent gets: background, reasoning, decisions, your rules | `context`, `reasoning`, `judgement`, `representation` |

The reader's agent gets rules from the sender (what it may say, what it must check with the sender first, what it must never do). Shared content is data, not instructions.

## Repository layout

| Path | What it is |
|---|---|
| [`schema/relay.context-package.v1.json`](schema/relay.context-package.v1.json) | JSON Schema (draft 2020-12) for a relay |
| [`schema/examples/`](schema/examples) | A complete example package (validates against the schema) |
| [`skill/relay/SKILL.md`](skill/relay/SKILL.md) | The `/relay` skill: how an agent prepares, publishes and follows up on a relay |
| [`docs/EXTRACTION.md`](docs/EXTRACTION.md) | The extraction standard: what to capture, and how not to make things up |
| [`docs/CONNECT.md`](docs/CONNECT.md) | Connect Relay to Claude, ChatGPT, Codex, Cursor and other MCP agents |
| [`adapters/`](adapters) | Recipes for agent frameworks (coming) |

## Try it

- See a live relay in your browser: https://relayto.ai/try
- Connect your agent: add `https://relayto.ai/mcp` as a connector ([steps](docs/CONNECT.md)), then say "relay … to … for …"
- Everything you can say: https://relayto.ai/commands

Receiving a relay is always free and needs nothing installed.

## Validate a package

With Node and [Ajv](https://ajv.js.org) (`npm install ajv ajv-formats`):

```js
const Ajv = require("ajv/dist/2020").default;
const addFormats = require("ajv-formats");
const schema = require("./schema/relay.context-package.v1.json");
const pkg = require("./your-package.json");

const ajv = new Ajv({ allErrors: true, strict: false });
addFormats(ajv);
const validate = ajv.compile(schema);
console.log(validate(pkg) ? "valid" : validate.errors);
```

## Using the format in your app

Apps can read relays shared with them (JSON at the same link a person opens) and can request context from people. If you're building on the format, open an issue and tell us what you're making.

## Contributing

Issues and pull requests on the format, the skill and the docs are welcome. See [CONTRIBUTING.md](CONTRIBUTING.md). Security reports: [SECURITY.md](SECURITY.md).

## License

Apache License 2.0. See [LICENSE](LICENSE). "Relay" and the Relay service are by Neopolis Global, Inc.
