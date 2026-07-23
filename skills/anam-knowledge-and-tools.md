---
name: Ground an Anam persona with knowledge and tools
description: Build a RAG knowledge group, upload documents, register function-calling tools, and attach them to a persona.
api: openapi/anam-openapi-original.json
operations: [createKnowledgeGroup, uploadKnowledgeDocument, searchKnowledgeGroup, createTool, updatePersona]
---

# Ground an Anam persona with knowledge and tools

Give a persona a retrieval knowledge base and callable tools.

## Auth
`Authorization: Bearer <API_KEY>`. Document upload also accepts an `X-Upload-Token` header as an alternative.

## Steps
1. **Create a knowledge group** — `createKnowledgeGroup` (`POST /v1/knowledge/groups`).
2. **Upload documents** — `uploadKnowledgeDocument` (`POST /v1/knowledge/groups/{id}/documents`). Accepts PDF, TXT, MD, DOCX, CSV up to 50MB.
3. **Verify retrieval** — `searchKnowledgeGroup` (`POST /v1/knowledge/groups/{id}/search`) does vector-similarity search over the group.
4. **Register a tool** — `createTool` (`POST /v1/tools`) defines a function-calling tool the persona can invoke mid-conversation.
5. **Attach to a persona** — `updatePersona` (`PUT /v1/personas/{id}`) to wire the knowledge group and `toolIds` onto the persona (or pass `toolIds`/`tools` inline in `personaConfig` at session-token time).

## Conventions
- List/browse ops paginate with `page` + `perPage`; response envelope `{ data, meta }`.
- Deletes are soft by default (avatars support `hard=true` for data-erasure).
- No idempotency keys; treat POST retries as potentially duplicating.
