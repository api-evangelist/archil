---
name: Run a command and search files on a disk (serverless)
description: Execute bash commands against Archil disk data with no separate sandbox, and run a parallelized regex search across a directory.
api: openapi/archil-openapi-original.yml
operations: [execDisk, grepDisk, exec]
---

# Run a command and search files on a disk (serverless)

Archil can run compute directly against disk data — ideal for giving an agent a
persistent filesystem and a bash tool.

## Auth
`Authorization: {API_KEY}` on every request; regional base host; `{success, data|error}`
envelope.

## Steps
1. **Run a command on one disk** — `execDisk` (`POST /api/disks/{id}/exec`) launches a
   container with the disk mounted, runs the command to completion, and shuts down.
   It returns immediately with container info; poll for completion. A command that
   exceeds the request budget returns `504` (timed out).
2. **Run across multiple disks** — `exec` (`POST /api/exec`) mounts a supplied set of
   disks each at its own path under `/mnt/archil` and runs the command. Activation is
   **atomic**: every disk mounts or none do.
3. **Search files** — `grepDisk` (`POST /api/disks/{id}/grep`) searches files under a
   directory for a regex; listing and matching are fanned out across ephemeral
   containers so it finishes within the time budget regardless of directory size.

## Errors
`401` invalid key · `400` bad request · `404` disk not found · `504` command timed out ·
`500` server error. See `errors/archil-problem-types.yml`.

## Notes
For agent fan-out, run each agent/job against its own writable **branch** so writes stay
isolated and you are billed only for the unique data each fork adds.
