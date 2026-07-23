---
name: Create an S3-backed disk and authorize a client
description: Create an Archil disk backed by an object-storage bucket, add an authorization principal, and confirm it is ready to mount.
api: openapi/archil-openapi-original.yml
operations: [createDisk, addDiskUser, getDisk]
---

# Create an S3-backed disk and authorize a client

Use the Archil Control Plane API to provision a disk and make it mountable.

## Auth
Send `Authorization: {API_KEY}` on every request (account-scoped API key — NOT a disk
token). Pick the regional base host, e.g. `https://control.green.us-east-1.aws.prod.archil.com`.
Every response uses the `{ "success": true|false, "data"|"error": ... }` envelope.

## Steps
1. **Create the disk** — `createDisk` (`POST /api/disks`) with a `name`, one or more
   `mounts` (the object-storage data source, e.g. an S3 bucket), and `authMethods`.
   A default disk token user is generated and returned in
   `data.authorizedUsers[].token` — this one-time token cannot be retrieved again, so
   capture it now.
   - This call is **idempotent by name + config**: if a disk with the same name and a
     matching configuration already exists you get `200` with the existing disk; a name
     collision with a *different* config returns `409` — do not blindly retry on 409,
     reconcile the config first.
2. **Add another principal (optional)** — `addDiskUser` (`POST /api/disks/{id}/users`)
   to add a `token` user (shared token + nickname) or an `awssts` user (AWS IAM role ARN).
3. **Confirm readiness** — `getDisk` (`GET /api/disks/{id}`) and check `status` /
   `fsHandlerStatus`, plus `authorizedUsers` and `mounts`.

## Errors
`401` invalid API key · `400` malformed body · `403` region not enabled · `409` name
exists with differing config · `500` server error. See `errors/archil-problem-types.yml`.

## Notes
A disk is the unit of access isolation — anyone who can mount it reads everything on it.
For per-tenant isolation give each tenant its own disk; for agent/CI fan-out use
copy-on-write branches. See `conventions/archil-conventions.yml`.
