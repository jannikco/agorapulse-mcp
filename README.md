# Agorapulse MCP server

Discover the social workspace and profiles, then add an internal calendar note without publishing.

A direct-provider MCP server with useful local execution. No PipMCP account is required for local use. Provider API access and any provider charges are your responsibility.

## Quick start

Requires Node.js22+. Clone this repository, then run:

```sh
npm ci --ignore-scripts
npm test
npm start
```

The default command inspects the tools offline. Configure your own credentials in your environment:

- `AGORAPULSE_API_KEY`



Credential help: https://support.agorapulse.com/en/articles/12417183-how-to-connect-to-agorapulse-s-open-api

## Execute a real read

Replace example record IDs with records you are authorized to access.

```sh
npm start -- --call agorapulse_list_organizations '{}'
```

## Use with an MCP client

Launch `node /absolute/path/agorapulse-mcp/server.mjs` using stdio transport; pass credentials through the client environment. Stdout is reserved for JSON-RPC.

## Tools

| Tool | Effect | Purpose |
| --- | --- | --- |
| agorapulse_list_organizations | Read | agorapulse list organizations |
| agorapulse_list_profiles | Read | agorapulse list profiles |
| agorapulse_create_calendar_note | Explicit approval required | Create an internal calendar note; does not publish a social post. |

## Concrete workflow

Discover the social workspace and profiles, then add an internal calendar note without publishing.

1. agorapulse_list_organizations
2. agorapulse_list_profiles
3. agorapulse_create_calendar_note

## Optional writes

Writes are disabled by default. Set CONNECTOR_ALLOW_WRITES=true and supply `_approval: {approved:true,idempotencyKey:"unique-request-id"}` on the specific call. Do not reuse a key for a different request. Uncertain outcomes are never automatically replayed; inspect the provider before another attempt.

## Capability evidence

Local: **Tested — HTTP fixtures and real stdio tools/call**. Managed: **Documented**. Provider authentication and live operations have not been established by these fixture tests. Evidence applies per operation and execution mode; setup discovery does not imply provider execution.

## Managed production

[Activate with PipMCP](https://pipmcp.com/github/start?app=agorapulse&integration=agorapulse-mcp&utm_source=github&utm_campaign=github-agorapulse-mcp&utm_content=readme) when you want the hosted service.  Local credentials are not uploaded by this code. Review provider permissions in the managed onboarding. [Directory and related projects](https://jannikco.github.io/pipmcp-connectors/integrations/agorapulse/).

## Provenance and maintenance

- https://api.agorapulse.com/docs/open-api.yml

Maintainer:jannikco. MIT license for this code. See connector.json for selected operations and integration.json for evidence. Runtime dependencies retain their own licenses. Report sensitive issues through https://pipmcp.com/contact; do not post credentials or customer data in public issues.
