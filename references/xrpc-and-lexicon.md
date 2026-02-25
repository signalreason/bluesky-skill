# XRPC and Lexicon

Use this file when mapping product requirements to exact AT Protocol methods.

## Core Concepts

- Lexicons define schemas and procedures/queries for AT Protocol APIs.
- API methods are named with NSIDs like `app.bsky.feed.getTimeline`.
- HTTP invocation format is `/xrpc/<full.method.nsid>`.
- Method verb matters.
- `GET` usually uses query params.
- `POST` usually sends JSON body (or binary/blob upload for specific endpoints).

## Naming and Invocation Rules

1. Start from the method NSID shown in the API docs.
2. Build the path as `/xrpc/<NSID>`.
3. Use exactly the HTTP method declared by that endpoint.
4. Validate request fields against endpoint schema before sending.
5. Prefer official SDK convenience methods first, then raw XRPC for unsupported/debug paths.

## Minimal Invocation Examples

Timeline query over XRPC:

```bash
curl --request GET \
  --url 'https://public.api.bsky.app/xrpc/app.bsky.feed.getTimeline?limit=20'
```

Create a post record via XRPC:

```bash
curl --request POST \
  --url 'https://bsky.social/xrpc/com.atproto.repo.createRecord' \
  --header "Authorization: Bearer $BLUESKY_ACCESS_TOKEN" \
  --header 'Content-Type: application/json' \
  --data '{
    "repo": "did:plc:example",
    "collection": "app.bsky.feed.post",
    "record": {
      "$type": "app.bsky.feed.post",
      "text": "Hello from XRPC",
      "createdAt": "2026-02-25T00:00:00.000Z"
    }
  }'
```

## SDK Mapping Tips

- For common app actions, use `BskyAgent` helpers (`getTimeline`, `post`, `like`, `repost`, `follow`, `getProfile`, `upsertProfile`).
- For lower-level methods, use generated namespace clients in `@atproto/api` when available instead of constructing raw JSON by hand.

## Preflight Checklist for New Method

1. Confirm NSID, verb, and auth requirement in docs.
2. Confirm host routing (public/appview/PDS).
3. Confirm request shape and required fields.
4. Add a one-call smoke test for that method.
5. Capture and log structured error payloads for failed calls.

## Official Sources

- AT Protocol concepts and Lexicons: https://docs.bsky.app/docs/advanced-guides/atproto
- HTTP API and endpoint reference: https://docs.bsky.app/docs/api/app-bsky-feed-get-timeline
- API directory and host guidance: https://docs.bsky.app/docs/advanced-guides/api-directory
