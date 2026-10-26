# Hacker News: checkpoint example

Read current docs before configuring the monitor:

```bash
agent-data docs news-ycombinator-com
agent-data call news-ycombinator-com feeds-by-feed \
  --input-json '{"path":{"feed":"newest"}}'
```

The live newest feed returns one page in source order, a `next_cursor`, and `source_meta` with the source URL, retrieval time, and coverage. A real page returned 30 records; its first story had `id: "50249237"` and `source_id: 50249237`.

For a monitor starting now, save the newest observed story's numeric `source_id` as the initial checkpoint after recording the baseline. Poll from that saved value:

```bash
agent-data call news-ycombinator-com poll \
  --input-json '{"query":{"after_id":50249237,"limit":20}}'
```

Replace the example checkpoint with the monitor's saved value. The real call returned two stories, `50249275` and `50249257`, with:

- `checkpoint_found: true`
- `status: "complete"`
- `next_cursor: null`
- `next_checkpoint: 50249275`
- `unresolved_ids: []`
- `source_window_size: 500`

Persist the records and pending changes before committing `next_checkpoint`. For a paginated backlog, keep the original `after_id` and pass the returned `next_cursor` unchanged. Advance the checkpoint only after the backlog is complete and any unresolved records or coverage gaps have been handled. The poll cursor is separate from the feed cursor.

The source reports a moving newest-story window, not durable history. Retain `status`, `checkpoint_found`, `unresolved_ids`, and `coverage` with every run; surface incomplete coverage rather than claiming a lossless scan. Response story IDs are strings, while `source_id`, `after_id`, and `next_checkpoint` are integers.

This check demonstrated one complete poll from a recently observed checkpoint. Multi-page backlog recovery and expired checkpoints were not exercised. New-story polling also does not establish detection of changes to older stories; refresh those records separately when the monitoring objective requires it.
