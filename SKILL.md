---
name: bluesky-developer-apis
description: Implement, debug, and test Bluesky and AT Protocol integrations using official Bluesky Developer APIs. Use when Codex needs to choose between public and authenticated hosts, implement OAuth token flows, invoke Lexicon/XRPC methods correctly, handle pagination and rate limits, or troubleshoot auth, scope, and malformed request failures.
---

# Fast Start: Choose Auth Mode

1. Decide data sensitivity and operation type.
- Use unauthenticated public reads for public feeds/profiles/posts: host `https://public.api.bsky.app`.
- Use authenticated appview access for user-scoped app APIs: host `https://bsky.social` with OAuth access token.
- Use authenticated repo/account access for `com.atproto.*` write/private endpoints: call via `https://bsky.social` (Bluesky entryway) or direct user PDS routing for PDS-native integrations.
2. Prefer official SDK patterns first (`@atproto/api` and official OAuth helpers), then use raw HTTP XRPC only when needed.
3. Open [references/auth.md](references/auth.md) before implementing auth logic.

# Implementation Workflow (New API Task)

1. Map the product requirement to Lexicon methods.
- Example mapping: timeline read -> `app.bsky.feed.getTimeline`; post write -> `com.atproto.repo.createRecord`.
2. Confirm method contract in the API Directory.
- Verify HTTP verb, params/body schema, output schema, and required auth.
3. Choose host + auth model.
- Re-check decision rules in [references/auth.md](references/auth.md).
4. Implement with safe defaults.
- Timeouts: start with 10s request timeout.
- Retries: retry only `429` and `5xx`, max 3 attempts, exponential backoff with jitter.
- Pagination guards: cap pages/items and stop on missing/repeated cursor.
5. Add deterministic error handling.
- Surface HTTP status, XRPC error code/message, request id, and host used.
- Avoid silent fallback between hosts.
6. Add smoke checks.
- Public smoke: one read endpoint without auth.
- Auth smoke: one OAuth-backed endpoint and one write/read-after-write check when relevant.
7. Document endpoint assumptions in code comments or tests and link the exact docs page used.

# Debugging Workflow

1. Auth failures (`401`/`403`).
- Confirm token exists, is unexpired, and targets the right host/audience.
- Confirm you are not calling a PDS-only endpoint on appview/public host.
- See [references/auth.md](references/auth.md) and [references/troubleshooting.md](references/troubleshooting.md).
2. Scope failures (`insufficient_scope` or equivalent).
- Re-check requested OAuth scopes versus called namespaces.
- Re-authorize if scopes changed.
- See [references/auth.md](references/auth.md) and [references/troubleshooting.md](references/troubleshooting.md).
3. Rate limit responses (`429`).
- Honor backoff, reduce request burst/page size, and resume with cursor.
- See [references/rate-limits-and-pagination.md](references/rate-limits-and-pagination.md).
4. Malformed XRPC calls (`400`, schema mismatch).
- Verify Lexicon NSID, HTTP verb, query/body shape, and required fields.
- See [references/xrpc-and-lexicon.md](references/xrpc-and-lexicon.md).

# Safe Defaults

- Prefer OAuth over app-password-based auth for production apps.
- Never log raw access/refresh tokens.
- Set explicit timeout, retry policy, and pagination limits in every client.
- Use cursor-based loops with dedupe guards (`uri`/`cid`) for feed/search endpoints.
- Handle eventual consistency: write to PDS, then poll appview with bounded backoff.
- Resolve identities with DID-first logic and short-lived handle cache.

# Reference Map

- Auth hosts, OAuth, and token handling: [references/auth.md](references/auth.md)
- XRPC/Lexicon method naming and invocation: [references/xrpc-and-lexicon.md](references/xrpc-and-lexicon.md)
- Rate limits, pagination, and operational defaults: [references/rate-limits-and-pagination.md](references/rate-limits-and-pagination.md)
- Common workflow recipes (posting/feeds/profiles/interactions): [references/common-workflows.md](references/common-workflows.md)
- Failure triage and fixes: [references/troubleshooting.md](references/troubleshooting.md)
