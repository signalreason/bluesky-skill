# 2026-02-25 Learnings

- Bluesky docs explicitly split API usage by host: unauthenticated public appview (`public.api.bsky.app`), authenticated appview (`bsky.social`), and PDS-oriented operations for account/repo methods.
- OAuth is the recommended production auth model; app-password flows still appear in tutorials but should not be the default guidance for production integrations.
- Practical reliability guidance should combine published rate limits with cursor pagination guards and read-after-write consistency handling.
