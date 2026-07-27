---
name: Generate translation context for a project
description: Determine whether context generation is needed, generate project glossary terms and translation instructions, and poll the job to completion.
api: openapi/general-translation-openapi-original.yml
operations: [shouldGenerateContext, generateContext, getContextStatus]
---

# Generate translation context for a project

Context (glossary terms + translation instructions) improves AI translation quality. Use this before enqueuing translations for a new or changed project.

## Auth
- `Authorization: Bearer <key>` (project or org key). Requires `project:context:read` / `project:context:write`.

## Steps
1. **Check need** — `shouldGenerateContext` (`GET /v2/project/setup/should-generate`) to decide whether context should be regenerated.
2. **Generate** — `generateContext` (`POST /v2/project/setup/generate`) from the project's source files. This starts a background job and returns a `jobId`.
3. **Poll** — `getContextStatus` (`GET /v2/project/setup/status/{jobId}`) until the job completes. Context generation is heavy-tier (30/60s); back off on `429`.

## Rules
- Errors: JSON `{ "error": "<message>" }`; a `402` means quota/payment validation failed — check plan usage in the dashboard.
- Do not re-trigger `generateContext` while a prior job is still running for the same project.
