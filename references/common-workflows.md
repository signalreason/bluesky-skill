# Common Workflows

Use this file for quick implementation patterns: posting, feeds, profiles, and interactions.

## Posting

Decision rules:

1. Use `agent.post()` for standard text posts.
2. Use explicit record creation when you need full record control or non-standard fields.
3. For images/media, upload blobs first, then reference them in the post embed record.

Minimal examples:

```ts
await agent.post({ text: "Hello Bluesky from API integration" })
```

```ts
await agent.com.atproto.repo.createRecord({
  repo: agent.session?.did!,
  collection: "app.bsky.feed.post",
  record: {
    $type: "app.bsky.feed.post",
    text: "Explicit record post",
    createdAt: new Date().toISOString(),
  },
})
```

## Feeds

Decision rules:

1. Start with timeline/author feed endpoints for canonical read paths.
2. Always implement cursor pagination.
3. Keep page size conservative (`25-50`) unless throughput requires more.

Minimal examples:

```ts
const timeline = await agent.getTimeline({ limit: 25 })
const author = await agent.getAuthorFeed({ actor: "did:plc:example", limit: 25 })
```

## Profiles

Decision rules:

1. Fetch with `getProfile` or `getProfiles`.
2. Update your own profile via `upsertProfile`.
3. For avatar/banner updates, upload blob and then write blob refs in profile record.

Minimal examples:

```ts
const profile = await agent.getProfile({ actor: "bsky.app" })
```

```ts
await agent.upsertProfile(() => ({
  displayName: "Example Dev",
  description: "API integration test profile",
}))
```

## Interactions (Like/Repost/Follow)

Decision rules:

1. Always keep target `uri` and `cid` together for like/repost.
2. Keep follow record URI to support unfollow.
3. Treat interaction writes as rate-limited operations.

Minimal examples:

```ts
await agent.like(postUri, postCid)
await agent.repost(postUri, postCid)
const followRecord = await agent.follow("did:plc:example")
await agent.deleteFollow(followRecord.uri)
```

## Workflow Checklist (Any Feature)

1. Confirm endpoint and auth/host.
2. Implement request + error handling + retries.
3. Add pagination guard (if list endpoint).
4. Add one public and/or authenticated smoke test.
5. Verify behavior against official docs before merging.

## Official Sources

- Posting tutorial: https://docs.bsky.app/docs/tutorials/creating-a-post
- Viewing feeds tutorial: https://docs.bsky.app/docs/tutorials/viewing-feeds
- Viewing/editing profiles tutorials: https://docs.bsky.app/docs/tutorials/viewing-profiles
- Editing profiles tutorial: https://docs.bsky.app/docs/tutorials/editing-profiles
- Liking/reposting tutorial: https://docs.bsky.app/docs/tutorials/liking-and-reposting
- Following users tutorial: https://docs.bsky.app/docs/tutorials/following-users
