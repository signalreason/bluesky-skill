# Rate Limits, Pagination, and Operational Defaults

Use this file for high-volume integrations, feed sync loops, and reliability hardening.

## Rate Limit Facts (from official docs)

- `com.atproto.server.createAccount`: 5 per day per email.
- `com.atproto.server.createAccount`: 100 per day per IP.
- `com.atproto.server.createSession`: 30 per 5 minutes.
- `com.atproto.server.createSession`: 300 per day.
- Post creation: 500 per hour and 5000 per day per account.
- Likes: 1500 per day per account.
- Follows: 12000 per day per account.

Treat limits as current published defaults; operators may apply stricter controls.

## Pagination Rules

1. Prefer cursor-based iteration for feed/search/list endpoints.
2. Respect endpoint `limit` max (commonly 100 on app feed endpoints).
3. Stop when cursor is missing or unchanged.
4. Cap total pages/items per job to avoid runaway loops.
5. Dedupe by stable keys (`uri` + `cid`) when merging pages.

Minimal loop pattern:

```ts
let cursor: string | undefined
for (let page = 0; page < 20; page++) {
  const res = await agent.getTimeline({ limit: 50, cursor })
  consume(res.data.feed)
  if (!res.data.cursor || res.data.cursor === cursor) break
  cursor = res.data.cursor
}
```

## Retry and Backoff Defaults

- Retry only transient classes: `429`, `500`, `502`, `503`, `504`.
- Use exponential backoff with jitter (for example: 500ms, 1s, 2s + jitter).
- Cap retries at 3 attempts unless running an offline backfill job.
- Honor `Retry-After` header if present.

## Operational Best Practices

- Write/read consistency: write to PDS first, then poll appview with bounded backoff when immediate read-after-write is required.
- Identity handling: resolve and store DIDs as primary identity.
- Identity handling: cache handle resolution briefly (for example, 5 minutes) and DID docs longer (for example, 24 hours), then re-resolve.
- Localization: for user-facing feed requests, set `Accept-Language` when language ranking matters.

## Official Sources

- Published limits: https://docs.bsky.app/docs/advanced-guides/rate-limits
- Pagination examples and `limit` constraints: https://docs.bsky.app/docs/tutorials/viewing-feeds
- Read-after-write consistency guidance: https://docs.bsky.app/docs/advanced-guides/read-after-write
- Identity resolution best practices: https://docs.bsky.app/docs/advanced-guides/resolving-identities
