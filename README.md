# Archil

Archil is the cloud filesystem for AI — it turns an existing object-storage bucket (Amazon S3, GCS, Cloudflare R2, Azure Blob, MinIO, Wasabi, Backblaze B2, DigitalOcean Spaces) into an unlimited, POSIX-compatible local disk that thousands of machines can mount at once. It exposes a REST Control Plane API (OpenAPI 3.1) for managing disks, disk users, API tokens, and serverless execution, plus first-party TypeScript (`disk`) and Python (`archil`) SDKs, a `disk` CLI, a Linux `archil` mount CLI, a Kubernetes CSI driver, a Terraform provider, and an S3-compatible HTTP API.

Backed by: felicis — https://archil.com

Surfaced from the Felicis portfolio and enriched by the API Evangelist pipeline. See `apis.yml` for the full artifact index.
