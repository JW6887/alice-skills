---
name: feishu-doc-write
title: Write content to a Feishu/Lark document (docx)
description: Create a Feishu doc inside a wiki knowledge base, authorize a user, and insert content block by block with proper delay. Documents the many gotchas in the Feishu docx API.
author: Hermes Agent
created: 2026-04-30
---

# Feishu Doc Write Skill

## When to Use

Create a new Feishu document and write substantial content into it — PRDs, specs, notes, reports, etc.

## Key Constraints (Gotchas)

1. **Must use `curl` via `subprocess.run()`**, NOT Python `urllib/requests` — sandbox returns `invalid param` when using Python HTTP libraries directly.
2. **Insert blocks one at a time** with `sleep(0.8)` delay between calls — batch insertion is unreliable.
3. **Always send `"document_revision_id": -1`** with every block insertion call.
4. **Block type restrictions**:
   - ✅ `2` (text) / `3` (h1) / `4` (h2) / `5` (h3) / `12` (code) — reliable
   - ❌ `10` (unordered list) / `13` (blockquote) / `23` (divider) — unreliable (400 error)
   - **Workaround**: use text blocks with prefixes: `"• "` for lists, `"> "` for quotes

## Credentials

Credentials come from Hermes environment variables — do NOT use hardcoded strings. The correct vars for this workspace:

| Variable | Source | Value |
|----------|--------|-------|
| `FEISHU_APP_ID` | `env:FEISHU_APP_ID` | `cli_a9644452dafadcd2` |
| `FEISHU_APP_SECRET` | `env:FEISHU_APP_SECRET` | `PyqcBudgiBH4ZYchqLermeSmRJfawGn5` |
| `WIKI_PARENT_TOKEN` | constant | `T5uFwRZLgi9NrJkW6X9cR6PDnGb` (wiki knowledge base) |
| `WIKI_PARENT_TYPE` | constant | `14` |
| `USER_OPEN_ID` | constant | `ou_b15d6cf62fd0a726448e676e758b0223` (George) |

> When fetching the token, pass app_id/app_secret as JSON literals in the curl `-d` body, not shell-exported variables.

## Execution Flow

### Step 1 — Get Token
```bash
curl -s -X POST "https://open.feishu.cn/open-apis/auth/v3/tenant_access_token/internal" \
  -H "Content-Type: application/json" \
  -d '{"app_id":"cli_a9644452dafadcd2","app_secret":"PyqcBudgiBH4ZYchqLermeSmRJfawGn5"}'
```

### Step 2 — Create Document in Wiki
```bash
curl -s -X POST "https://open.feishu.cn/open-apis/docx/v1/documents" \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"title":"DOC_TITLE","parent_token":"T5uFwRZLgi9NrJkW6X9cR6PDnGb","parent_type":14}'
```

### Step 3 — Authorize User
```bash
curl -s -X POST \
  "https://open.feishu.cn/open-apis/drive/v1/permissions/${DOC_ID}/members?type=docx" \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"member_type":"openid","member_id":"ou_b15d6cf62fd0a726448e676e758b0223","perm":"full_access"}'
```

### Step 4 — Insert Blocks (one-by-one, 0.8s delay)
```bash
curl -s -X POST \
  "https://open.feishu.cn/open-apis/docx/v1/documents/${DOC_ID}/blocks/${DOC_ID}/children" \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"children":[BLOCK_JSON],"document_revision_id":-1}'
```

### Step 5 — Close the Loop (MANDATORY)

After all blocks are inserted, deliver the result to the user in the same turn:

1. **Reply in the current chat** — do NOT use `send_message` separately. In a Feishu DM, just type the reply directly.
2. **Include the doc link** in the format: `https://feishu.cn/docx/{DOC_ID}`
3. **Wait for acknowledgment** before treating the task as done.

> **Behavioral note**: In a Feishu DM context, you are already in the chat where the user can see your reply. Using `send_message` to the same chat AND typing the same content manually creates a duplicate message. In the current session conversation, reply directly — `send_message` is for other channels.

## Block JSON Reference

- **Text** `{"block_type":2,"text":{"elements":[{"text_run":{"content":"...","text_element_style":{}}}],"style":{}}}`
- **H1** `{"block_type":3,"heading1":{"elements":[{"text_run":{"content":"...","text_element_style":{}}}],"style":{}}}`
- **H2** `{"block_type":4,"heading2":{"elements":[{"text_run":{"content":"...","text_element_style":{}}}],"style":{}}}`
- **H3** `{"block_type":5,"heading3":{"elements":[{"text_run":{"content":"...","text_element_style":{}}}],"style":{}}}`
- **Code** `{"block_type":12,"code":{"elements":[{"text_run":{"content":"..."}}],"style":{"language":1}}}`

## Known Pitfalls

| Issue | Fix |
|-------|-----|
| `batch_create_document=true` returns code=0 but doc is empty | This flag is broken. Always use create-then-children flow |
| `code=1770029` on page block | Use DOC_ID as block parent, not a separate block ID |
| `code=99992402` on import API | Don't use import API; document create + children flow works |
| Lists/quotes/dividers fail (400) | Replace with text blocks using prefix characters |
| `app id not exists` / `Missing access token` | Token fetch failed — verify app_id/app_secret env vars are correct |
| 403 on block insertion | Token expired (7200s); re-fetch token and retry |
| Doc link 404 in Feishu chat | URL must be `https://feishu.cn/docx/{DOC_ID}` — NOT `h-doc` or `doc` |
