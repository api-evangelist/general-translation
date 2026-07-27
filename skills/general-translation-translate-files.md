---
name: Translate a project's source files
description: Upload source files to a General Translation project, queue them for translation, poll status, and download the translated files.
api: openapi/general-translation-openapi-original.yml
operations: [uploadSourceFiles, enqueueTranslations, getTranslationStatus, downloadFiles, publishFiles]
---

# Translate a project's source files

Use this to localize a project's source content end to end via the General Translation API.

## Auth
- Send your API key as `Authorization: Bearer <key>` (the docs also accept the `x-gt-api-key` header).
- Use a production project key (`gtx-api-`) or an org key (`gtx-org-`) with the `gt-project-id` header.
- Optionally pin the API version with the `gt-api-version` header (latest: `2026-03-06.v1`).

## Steps
1. **Upload source files** — `uploadSourceFiles` (`POST /v2/project/files/upload-files`). Max 100 files per request. Requires `project:files:write`.
2. **Queue translation** — `enqueueTranslations` (`POST /v2/project/translations/enqueue`) with the target locales. Requires `project:translations:enqueue`.
3. **Poll status** — `getTranslationStatus` (`GET /v2/project/translations/files/status/{fileId}`) until the file+locale reports complete. Respect the light-tier rate limit (300/60s); back off on `429`.
4. **Download results** — `downloadFiles` (`POST /v2/project/files/download`) or `downloadFile` (`GET /v2/project/files/download/{fileId}`) for a single file. Requires `project:files:read`.
5. **(Optional) Publish** — `publishFiles` (`POST /v2/project/files/publish`) to publish translated files.

## Rules
- Errors use a JSON envelope `{ "error": "<message>" }` (not RFC 9457). Handle `400/401/402/403/404/429/503` per errors/general-translation-problem-types.yml.
- No idempotency key is supported; do not blindly retry writes — check status first.
- Prefer webhook `translation_job.completed` / `translated_file.completed` events over tight polling (asyncapi/general-translation-webhooks.yml).
