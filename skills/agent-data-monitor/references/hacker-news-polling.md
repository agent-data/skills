# Hacker News: checkpoint and backlog example

This flow was checked against production using listing `c96b10fb-7db3-4761-8b45-be46c4825d78`. Fetch current docs before reuse:

```bash
agent-data docs c96b10fb-7db3-4761-8b45-be46c4825d78
agent-data call c96b10fb-7db3-4761-8b45-be46c4825d78 stories-newest \
  --input-json '{"query":{"limit":2}}'
```

The sample size of two demonstrates the protocol; it is not a retrieval cap. A real baseline returned story IDs `50082127` and `50082113`, with `checkpoint: "50082127"` and `source_limit: 500`. The newest endpoint's `next_cursor` traverses a moving newest-story list; it is distinct from the polling continuation.

To demonstrate a backlog, the probe deliberately started from the older observed story's numeric `source_id`:

```bash
agent-data call c96b10fb-7db3-4761-8b45-be46c4825d78 stories-poll \
  --input-json '{"query":{"after_id":50082113,"limit":2}}'
```

Actual progression:

| Stage | Returned IDs | `next_poll_after` | `next_poll_cursor` |
|---|---|---|---|
| Initial poll | `50082164`, `50082151` | `"50082113"` | Non-null opaque cursor |
| Follow returned cursor | `50082141`, `50082127` | `"50082164"` | `null` |

Both pages reported `checkpoint_found: true`. Persist records and pending changes, then pass the returned cursor unchanged as `query.cursor` to continue. Once `next_poll_cursor` is null, commit `next_poll_after` for the next poll. Do not advance to the newest returned ID while a backlog remains.

For this API, response IDs/checkpoints are strings; `after_id` requires an integer. Validate and convert the saved checkpoint to the documented numeric type before constructing typed input. A probe passing a string failed CLI validation. `--input-json` accepts JSON text, not an `@filename`; pass a saved JSON file's contents as the argument when using files.

Current docs say `checkpoint_found: false` exposes the recoverable source window after a checkpoint expires. Treat that as a coverage gap, drain the available records, deduplicate against saved state, and report unrecoverable history. That expired-checkpoint branch was documented but was not exercised in this probe. A successful short backlog does not prove lossless long-running monitoring or detection of updates to older stories.
