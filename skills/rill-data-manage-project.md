---
name: Provision a Rill org, project, and members
description: >-
  Create an organization and project in Rill Cloud and grant a teammate access,
  using the Rill Admin REST API.
api: openapi/rill-data-admin-openapi.yaml
transport: https-json
operations:
  - AdminService_CreateOrganization
  - AdminService_CreateProject
  - AdminService_GetProject
  - AdminService_AddProjectMemberUser
  - AdminService_SetProjectMemberUserRole
---

# Provision a Rill org, project, and members

Set up a Rill Cloud workspace and its access via the admin API at
`https://api.rilldata.com`.

## Authenticate

Send `Authorization: Bearer <token>` on every request. Use a service token for
automation (`rill service create`) or a personal access token (`rill token issue`).
The token's org/project role governs what you may do (admin/editor/viewer/guest).

## Steps

1. **Create the organization** — `AdminService_CreateOrganization`.
2. **Create the project** — `AdminService_CreateProject` under that org (supply a
   name and, typically, the git remote to deploy from).
3. **Confirm** — `AdminService_GetProject` to read back status and the prod
   deployment.
4. **Add a member** — `AdminService_AddProjectMemberUser` to invite/grant a user.
5. **Set their role** — `AdminService_SetProjectMemberUserRole` (admin/editor/viewer).

## Rules

- List endpoints paginate with `pageSize` + `pageToken`; follow `nextPageToken`
  until empty (see conventions/rill-data-conventions.yml).
- No idempotency-key header exists; creating a name that already exists returns
  ALREADY_EXISTS (gRPC code 6 / HTTP 409) — treat as "already provisioned".
- Errors use the gRPC status envelope (`code`/`message`/`details`) — see
  errors/rill-data-problem-types.yml.
