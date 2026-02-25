# Auth, Hosts, and Token Handling

Use this file when deciding host routing, OAuth behavior, or token lifecycle.

## Host and Auth Decision Rules

| Use case | Host | Auth | Notes |
| --- | --- | --- | --- |
| Public read-only data | `https://public.api.bsky.app` | None | Public appview API host for unauthenticated requests. |
| Authenticated Bluesky app APIs | `https://bsky.social` | OAuth access token | AppView host for authenticated app endpoints. |
| Repo/account-level `com.atproto.*` operations | `https://bsky.social` or user's PDS host | OAuth access token | Bluesky docs recommend `bsky.social` for authenticated requests; direct PDS routing is also relevant for PDS-native integrations. |
| Firehose relay | `wss://bsky.network` | Varies by endpoint | Relay host for event streaming use cases. |

## OAuth Implementation Checklist

1. Use OAuth (recommended by Bluesky docs) for production user auth.
2. Configure client metadata for your client type (public clients: `token_endpoint_auth_method=none` with PKCE + DPoP; confidential clients: pre-register client id and use client auth where supported).
3. Request only scopes needed for called APIs.
4. Exchange authorization code for access token (and refresh token when provided).
5. Persist token set securely and track expiry timestamps.
6. Refresh before expiry (or on first auth failure), then rotate stored refresh token if the server returns a new one.
7. Re-prompt authorization when scope requirements change.

## Token Handling Rules

- Never log access or refresh tokens.
- Store tokens encrypted at rest.
- Keep per-user token state keyed by issuer/client/user identity.
- Apply clock skew buffer (for example, refresh 60 seconds early).
- On `401`, attempt one refresh + replay; if it still fails, require re-auth.

## Minimal Examples

Public read (no auth):

```bash
curl --request GET \
  --url 'https://public.api.bsky.app/xrpc/app.bsky.feed.getTimeline?limit=10'
```

Authenticated call:

```bash
curl --request GET \
  --url 'https://bsky.social/xrpc/app.bsky.feed.getTimeline?limit=10' \
  --header "Authorization: Bearer $BLUESKY_ACCESS_TOKEN"
```

## Official Sources

- API hosts and auth model: https://docs.bsky.app/docs/advanced-guides/api-directory
- OAuth client guide: https://docs.bsky.app/docs/advanced-guides/oauth-client
- OAuth transition/scopes context: https://docs.bsky.app/blog/oauth-improvements
- AT Protocol host roles (PDS/AppView): https://docs.bsky.app/docs/advanced-guides/atproto
