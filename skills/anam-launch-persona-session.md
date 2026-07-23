---
name: Launch an Anam avatar persona session
description: Create a persona, mint a short-lived session token, and hand it to a client SDK to open a real-time WebRTC avatar session.
api: openapi/anam-openapi-original.json
operations: [createPersona, createSessionToken, getSession]
---

# Launch an Anam avatar persona session

Use this to stand up a real-time interactive AI avatar.

## Auth
All REST calls use `Authorization: Bearer <API_KEY>` (server key from lab.anam.ai). Never ship the raw API key to a browser — mint a session token instead (step 2).

## Steps
1. **Create a persona** — `createPersona` (`POST /v1/personas`) with an `avatarId`, `voiceId`, `llmId`, and `systemPrompt`. Reuse an existing persona id to skip this. (Avatars, voices, and LLMs are managed via `listAvatars`, `listVoices`, `listLlms`.)
2. **Mint a session token** — `createSessionToken` (`POST /v1/auth/session-token`). Pass either `{ personaId }` (stateful) or an inline `personaConfig`. The response `sessionToken` is a signed JWT valid ~1 hour.
3. **Open the session client-side** — hand `sessionToken` to the `@anam-ai/js-sdk` (or Python SDK); the SDK establishes the WebRTC audio/video stream. Do this from the client, not the server.
4. **Inspect the session** — after it ends, `getSession` (`GET /v1/sessions/{id}`) returns the session record including `toolCalls` once the engine delivers its report.

## Conventions
- Pagination on list ops: `page` + `perPage` (max 100), response `{ data, meta }`.
- No idempotency-key support — do not assume safe retry on POSTs.
- Errors are HTTP status + message: 401 (bad/missing key), 403 (enterprise-only), 404 (not found), 400 (invalid body).
