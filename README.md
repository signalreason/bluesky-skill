# bluesky-developer-apis

Codex skill for implementing, debugging, and testing Bluesky and AT Protocol integrations using official Bluesky Developer APIs and docs.

## Purpose

This skill helps Codex:

- choose the correct host and auth mode (public appview, authenticated appview, or repo/PDS paths)
- implement OAuth token handling safely
- map product requirements to Lexicon/XRPC methods
- handle rate limits, pagination, and read-after-write behavior
- troubleshoot auth, scope, and malformed-request failures

## Repository Layout

- `SKILL.md`: primary operational contract and workflows
- `references/`: focused implementation guides and troubleshooting playbooks
- `agents/openai.yaml`: display metadata and default prompt wiring
- `notes/`: captured learnings and future tooling ideas
- `PROMPT.md`: source prompt used to define/generate this skill

## Reference Map

- `references/auth.md`: host routing, OAuth checklist, and token lifecycle rules
- `references/xrpc-and-lexicon.md`: NSID naming, `/xrpc/<nsid>` invocation rules, and preflight checks
- `references/rate-limits-and-pagination.md`: published limits, cursor guards, retries, and operational defaults
- `references/common-workflows.md`: posting, feeds, profiles, and interaction implementation patterns
- `references/troubleshooting.md`: fast triage and fix checklists for common failures

## Quick Start

1. Use this repository as a Codex skill package.
2. Invoke the skill in a task with `$bluesky-developer-apis`.
3. Follow `SKILL.md` first, then open only the needed `references/*.md` file(s) for depth.

## Maintenance Guidelines

- Keep `SKILL.md` concise and procedural.
- Put deep API details in `references/`.
- Prefer official sources at `https://docs.bsky.app` when updating guidance.
