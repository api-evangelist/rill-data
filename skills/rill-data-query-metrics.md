---
name: Query Rill metrics views
description: >-
  Answer a business question against a Rill Cloud project by discovering its
  governed metrics views and running an aggregated query — through the hosted
  Rill MCP server.
api: mcp/rill-data-mcp.yml
transport: mcp
operations: [list_metrics_views, get_metrics_view, query_metrics_view_summary, query_metrics_view]
---

# Query Rill metrics views

Use Rill's governed metrics layer to answer questions with numbers that match
the dashboards — never query raw warehouse tables directly.

## Connect

Add the hosted Rill MCP server for the target project:

```
https://api.rilldata.com/v1/orgs/{org_name}/projects/{project_name}/runtime/mcp
```

Authenticate with OAuth (recommended — the connector runs the PKCE flow) or a
personal access token (`rill token issue`) sent as `Authorization: Bearer <token>`.
Public demo projects (org `demo`, project `rill-github-analytics`) need no token.

## Steps

1. **Discover** — call `list_metrics_views` to see the metrics views available
   in the project.
2. **Understand the model** — call `get_metrics_view` for the target view to read
   its dimensions and measures. Only use dimensions/measures that exist here.
3. **Get context** — call `query_metrics_view_summary` to learn the available
   time range and sample dimension values/types before querying.
4. **Query** — call `query_metrics_view` with the measures, dimensions, filters,
   and time range needed to answer the question.
5. **Honor guidance** — if any response includes an `ai_instructions` field,
   follow it for subsequent related calls.

## Rules

- Stay inside the metrics view's declared measures/dimensions — the point of
  Rill is governed, consistent numbers.
- Scope every request to the org/project (and optionally `/branch/{branch}`)
  encoded in the MCP URL.
- Errors return the gRPC status envelope (`code`, `message`, `details`) — see
  errors/rill-data-problem-types.yml.
