---
name: Manage Control Plane API tokens
description: Create, list, and revoke account-scoped Archil Control Plane API tokens.
api: openapi/archil-openapi-original.yml
operations: [createApiToken, listApiTokens, deleteApiToken]
---

# Manage Control Plane API tokens

Account-scoped API tokens authenticate Control Plane requests. They are distinct from
per-disk mount tokens.

## Auth
`Authorization: {API_KEY}` on every request; regional base host; `{success, data|error}`
envelope.

## Steps
1. **Create a token** — `createApiToken` (`POST /api/tokens`) with a `name` (and optional
   `description`). The full token value is returned **once** at creation time in the
   response — store it securely; it cannot be retrieved again. Later reads expose only
   the `tokenSuffix`.
2. **List tokens** — `listApiTokens` (`GET /api/tokens`) returns all tokens for the
   account with `id`, `name`, `tokenSuffix`, `createdAt`, and `lastUsedAt` (never the
   secret).
3. **Revoke a token** — `deleteApiToken` (`DELETE /api/tokens/{id}`) permanently revokes
   and deletes it.

## Errors
`401` invalid key · `400` bad request · `404` token not found · `500` server error.
See `errors/archil-problem-types.yml`.

## Notes
Rotate tokens by creating the replacement first, updating clients, then deleting the old
one. Use separate Dev and Production organizations to keep credentials isolated.
