# Troubleshooting Playbook

Use this file when requests fail or behavior diverges from expected docs behavior.

## Fast Triage Checklist

1. Capture: host, NSID, HTTP verb, status code, error payload, request id, and timestamp.
2. Confirm: correct host family (public/appview/PDS) and auth mode.
3. Confirm: NSID path and request schema match official endpoint docs.
4. Reproduce with minimal request (SDK call or `curl`) before deeper changes.

## Auth Failures (`401`, `403`)

Checks:

1. Is the token present and unexpired?
2. Is the endpoint being called on the correct host?
3. Did you refresh token and replay once?
4. Did user revocation/password reset invalidate the session?

Fixes:

- Refresh token and retry once.
- Re-run OAuth flow if refresh fails.
- Route repo/account calls to authenticated entryway (`bsky.social`) or the user's PDS instead of public host.

## Scope Issues (`insufficient_scope`)

Checks:

1. Compare requested OAuth scopes to the API namespaces you call.
2. Verify client metadata and authorization request scopes are current.
3. Confirm user re-consented after scope changes.

Fixes:

- Request the minimal additional scopes required.
- Force a fresh authorization when expanding scopes.
- Remove stale/deprecated scope assumptions from old app-password/session flows.

## Rate Limit Responses (`429`)

Checks:

1. Is retry policy honoring `Retry-After`?
2. Is pagination aggressively requesting large pages?
3. Is the system creating excessive sessions or write bursts?

Fixes:

- Back off with jitter and reduce concurrency.
- Lower page size and add job-level item caps.
- Batch non-urgent writes.

## Malformed XRPC Calls (`400`, schema errors)

Checks:

1. NSID typo in `/xrpc/...` path.
2. Wrong HTTP method (GET vs POST).
3. Missing required fields or wrong field types.
4. Incorrect `$type` on record payload.

Fixes:

- Re-copy endpoint contract from API reference.
- Validate payload with typed SDK models where possible.
- Add request pre-validation in client code/tests.

## Consistency and Identity Issues

- Read-after-write mismatch: read from PDS immediately after write, then poll appview with bounded delay.
- Handle-to-DID mismatch: resolve handle, fetch DID doc, and refresh cache according to TTL guidance.

## Minimal Debug Commands

```bash
curl -i 'https://public.api.bsky.app/xrpc/app.bsky.feed.getTimeline?limit=1'
```

```bash
curl -i \
  -H "Authorization: Bearer $BLUESKY_ACCESS_TOKEN" \
  'https://bsky.social/xrpc/app.bsky.actor.getProfile?actor=bsky.app'
```

## Official Sources

- API hosts and endpoint routing: https://docs.bsky.app/docs/advanced-guides/api-directory
- OAuth client guidance: https://docs.bsky.app/docs/advanced-guides/oauth-client
- OAuth scope migration context: https://docs.bsky.app/blog/oauth-improvements
- Published limits: https://docs.bsky.app/docs/advanced-guides/rate-limits
- API endpoint reference index: https://docs.bsky.app/docs/api/app-bsky-feed-get-timeline
