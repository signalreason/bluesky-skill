# Tool Ideas

- Add a reusable `scripts/smoke_bluesky_api.sh` helper that runs one unauthenticated public read smoke check, one authenticated appview read smoke check, and one optional authenticated write/read-after-write check against a configured test account.
- Add a small resolver utility that maps handle/DID to the correct PDS host before executing `com.atproto.*` calls.
