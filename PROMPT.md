Create a Codex skill named `bluesky-developer-apis` for working with the Bluesky Developer APIs.

Follow these requirements exactly:

1. Goal
- Build a production-usable skill that helps Codex reliably implement, debug, and test integrations with Bluesky/AT Protocol APIs.
- The skill must support both read-only usage (public endpoints) and authenticated usage.

2. Sources and accuracy
- Use the official Bluesky Developer Docs as the primary source of truth: https://docs.bsky.app
- Cover these areas explicitly in the skill:
  - API hosts and auth models (public API, appview, personal data server)
  - OAuth flows and token handling
  - Lexicon/XRPC concepts and how methods are named/invoked
  - Rate limits, pagination, and operational best practices
- If anything is ambiguous, prefer official docs over assumptions.

3. Skill output structure
- Create:
  - `SKILL.md`
  - `references/` with focused reference docs
  - `scripts/` only when they provide clear, repeatable value
  - `agents/openai.yaml`
- Keep `SKILL.md` concise and procedural.
- Put deep API details, endpoint tables, and troubleshooting playbooks in `references/`.

4. `SKILL.md` requirements
- Include YAML frontmatter with:
  - `name: bluesky-developer-apis`
  - A trigger-focused `description` that clearly states when this skill should be used.
- In the body, include:
  - A fast-start workflow for choosing auth mode (public vs authenticated)
  - A step-by-step implementation workflow for new API tasks
  - A debugging workflow (auth failures, scope issues, rate limit responses, malformed XRPC calls)
  - Guidance on safe defaults (timeouts, retries, pagination guards, error handling)
  - Pointers to specific `references/*.md` files for details

5. `references/` requirements
- Add focused files such as:
  - `references/auth.md`
  - `references/xrpc-and-lexicon.md`
  - `references/rate-limits-and-pagination.md`
  - `references/common-workflows.md` (posting, feeds, profiles, interactions)
  - `references/troubleshooting.md`
- Keep each file actionable (decision rules, checklists, minimal examples).

6. Script guidance
- Only add scripts if they reduce repeated work and are likely to be reused.
- If scripts are added, include at least one smoke test command in the skill instructions.

7. Validation and quality gate
- Run the skill validation flow and fix all issues.
- Ensure the skill is directly usable by another Codex instance without extra context.
- Ensure the skill emphasizes official SDK/docs patterns instead of ad hoc API guessing.

8. Deliverables
- Provide:
  - The final `SKILL.md`
  - A short summary of what each reference file contains
  - Any scripts created and why
  - Validation results

Write the skill contents now.
