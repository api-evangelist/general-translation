---
name: Translate content at runtime
description: Translate content on the fly through the General Translation runtime API for dynamic/unseen strings.
api: openapi/general-translation-openapi-original.yml
operations: [translate]
---

# Translate content at runtime

Use the runtime endpoint to translate content that was not pre-generated (e.g. dynamic UI strings during development).

## Auth
- `Authorization: Bearer <key>`. A development key (`gtx-dev-`) enables on-the-fly runtime translation; production keys serve pre-generated translations.
- Requires the `project:translations:generate` permission.

## Steps
1. **Translate** — `translate` (`POST /v2/translate`, served from `https://runtime2.gtx.dev` — NOT `api2.gtx.dev`). Send the content (plain text, ICU message, or i18next-style), the target locale, and optional metadata (surrounding context) to improve accuracy.

## Rules
- The runtime host is `https://runtime2.gtx.dev`; project/file endpoints live on `https://api2.gtx.dev`.
- Runtime translation is heavy-tier rate-limited (30/60s); cache results and back off on `429`.
- Errors use the JSON `{ "error": "<message>" }` envelope.
