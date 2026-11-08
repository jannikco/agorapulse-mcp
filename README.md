# Agorapulse social publishing workflows for AI assistants

Discover the social workspace and profiles, then add an internal calendar note without publishing.

[Start locally](#get-started) · [Connect your assistant](#connect-your-assistant) · [Available tools](#available-tools) · [Hosted PipMCP setup](#use-pipmcp-for-production)

## Built for useful workflows

- **Build a Agorapulse brief.** List organizations. Make that provider response available to your assistant before it proposes a next action.
- **Investigate a chosen Agorapulse result.** List profiles. Use an account or record returned by your provider rather than guessing an identifier.
- **Add a reviewed Agorapulse action.** Create an internal calendar note; does not publish a social post. Review the input first; execution requires operator opt-in and per-call approval.

## Why use this package?

Get a focused Agorapulse MCP integration without assembling request mappings and a stdio transport yourself. The package provides documented input schemas, provider authentication handling and an example CLI, with the same tools available to your assistant.

Reads are available independently of writes. Approved changes use operator opt-in and a per-call idempotency key; uncertain writes are not silently replayed.

## Get started

**Requirements:** Node.js 22 or newer, npm, and access to the provider account you intend to use.

```sh
git clone https://github.com/jannikco/agorapulse-mcp.git
cd agorapulse-mcp
npm ci --ignore-scripts
npm test
npm start
```

`npm start` shows the tool inventory and workflow offline. It does not authenticate or call the provider. The tests use provider fixtures and stdio transport; they do not modify your account.

### Configure your provider account

The current release connects directly to the provider with **your own credentials**. It does not require a PipMCP key for local execution. Configure the environment of the process or MCP client that runs the server; `.env` files are not loaded automatically.

| Environment variable | What to supply |
| --- | --- |
| `AGORAPULSE_API_KEY` | Your own provider API key or access token. |

In Agorapulse open your personal settings, go to the API keys sub-menu under Personal and generate a key. Click Copy and store it right away - it is not shown again. The key acts with your own user permissions.

[Provider credential/reference documentation](https://support.agorapulse.com/en/articles/12417183-how-to-connect-to-agorapulse-s-open-api). Keep credentials out of commits, issue reports and shared client configuration.

### Run your first provider read

After configuring the environment, run `agorapulse_list_organizations`:

```sh
npm start -- --call agorapulse_list_organizations '{}'
```

Replace sample account, record, file and project IDs with values from your authorized account. This command makes a real provider request and returns its result for you to inspect.

## Available tools

| Capability | MCP tool | Effect | Required inputs |
| --- | --- | --- | --- |
| List organizations. | `agorapulse_list_organizations` | Read | None |
| List profiles. | `agorapulse_list_profiles` | Read | `organizationId`, `workspaceId` |
| Create an internal calendar note; does not publish a social post. | `agorapulse_create_calendar_note` | Approved write | `organizationId`, `workspaceId`, `title`, `color`, `startDate`, `endDate` |

Tool schemas and bindings are in [connector.json](./connector.json). These are the selected operations this package ships.

### A practical tool sequence

1. Use `agorapulse_list_organizations` — List organizations.
2. Use `agorapulse_list_profiles` — List profiles.
3. Use `agorapulse_create_calendar_note` — Create an internal calendar note; does not publish a social post.

## Connect your assistant

Add this stdio server to an MCP-compatible client. Replace the absolute path and credential placeholders. Use your client’s secret/environment controls instead of committing this configuration.

```json
{
  "mcpServers": {
    "agorapulse-mcp": {
      "command": "node",
      "args": [
        "/absolute/path/agorapulse-mcp/server.mjs"
      ],
      "env": {
        "AGORAPULSE_API_KEY": "YOUR_AGORAPULSE_API_KEY"
      }
    }
  }
}
```

`node server.mjs` starts the MCP server; `npm start` runs the example CLI. The server reserves stdout for MCP messages. If the client does not inherit your shell environment, pass the required variables explicitly.

## Review changes before running writes

The write tool is disabled by default. Enable `CONNECTOR_ALLOW_WRITES=true` in the server process and include this approval object in the **specific** write call after reviewing its arguments:

```json
{
  "_approval": {
    "approved": true,
    "idempotencyKey": "unique-reviewed-request-id"
  }
}
```

This object is added alongside the tool’s own required fields; it is not a complete write request. Use a new idempotency key for a different action. If an outcome is uncertain, inspect the provider before trying again. Creating a record may trigger existing provider automations even when this package does not send a message itself.

## Use PipMCP for production

The local package is useful when you want to run these selected Agorapulse operations in your own process. PipMCP provides a hosted place to configure provider connections, publish tools, issue endpoint access tokens, apply server-side policy and approvals, and inspect execution/usage records. Provider credentials and hosted execution are configured in that service.

**[Set up Agorapulse in PipMCP](https://pipmcp.com/github/start?app=agorapulse&integration=agorapulse-mcp&utm_source=github&utm_campaign=github-agorapulse-mcp&utm_content=readme)**

A managed catalog template is listed, but that is not evidence that its tools match this repository or that your account is connected. Complete provider setup and inspect the endpoint’s actual tool inventory and permissions before use.

Local credentials are not uploaded by this repository. A hosted endpoint uses its own PipMCP endpoint access token and published tool inventory; the local and hosted credentials are separate.

## Scope and validation

- The installed package has HTTP request fixtures and MCP stdio `tools/call` tests.
- Fixture success checks client behavior; it does not establish provider authentication, paid-plan access or live account execution.
- No live provider execution is claimed for this package by these tests.
- Hosted tool names, schemas, permissions and availability can differ from this local package. A queued or accepted job/message is not proof of completion or delivery. Provider API plans, account rules and limits apply.
- See [integration.json](./integration.json) for operation-scoped evidence and the provider-specific limits above.

## FAQ

**Do I need a Agorapulse account?**

You need the provider account, token and permissions required by these operations. Provider charges and API limits remain with that account.

**Does this version require a PipMCP API key?**

This package uses your provider credentials for local execution. To use the hosted service, configure the provider connection in PipMCP and use the endpoint access token issued there.

**Why is a tool missing or a request rejected?**

This repository exposes the selected tools above. Check the required environment variables, provider scopes, account/region requirements and input IDs first. Hosted endpoints have their own published inventory and may exclude tools by role or policy. Do not retry a failed write without checking its outcome.

**Is this an official provider SDK or a hosted-service guarantee?**

This is an independently maintained MCP integration. Its fixtures and source references do not imply provider endorsement, unrestricted API access, or identical hosted behavior.

## Guides and related workflows

- [Agorapulse integration guide](https://jannikco.github.io/pipmcp-connectors/integrations/agorapulse/)
- [Agorapulse workflow and tool sequence](https://jannikco.github.io/pipmcp-connectors/workflows/agorapulse/)
- [More integration workflows](https://jannikco.github.io/pipmcp-connectors/workflows/)
- [Typed LeadAdapter API client](https://github.com/jannikco/linkedin-typescript-sdk)
- [LeadAdapter Python API client](https://github.com/jannikco/linkedin-python-sdk)
- [LinkedIn API quickstart](https://github.com/jannikco/linkedin-api)
- [LinkedIn people and post search](https://github.com/jannikco/linkedin-search-api)
- [LinkedIn post engagers](https://github.com/jannikco/linkedin-post-engagers)
- [LeadAdapter LinkedIn MCP server](https://github.com/jannikco/linkedin-mcp)
- [OpenAPI to MCP import starter](https://github.com/jannikco/openapi-to-mcp)
- [Swagger to MCP import starter](https://github.com/jannikco/swagger-to-mcp)
- [Agorapulse API and credential reference](https://support.agorapulse.com/en/articles/12417183-how-to-connect-to-agorapulse-s-open-api)
- [Additional official source 2](https://api.agorapulse.com/docs/open-api.yml)

Maintained by [jannikco](https://github.com/jannikco). MIT for this repository’s code; dependencies retain their licenses. [Report a reproducible issue](https://github.com/jannikco/agorapulse-mcp/issues) with redacted input and error details. For credentials or security issues, use [private contact](https://pipmcp.com/contact).
