# Saved public extension request

Create `extension-request.json` with these fields:

| Field | Value |
|---|---|
| `requestId` | UUID generated once and saved before submission. |
| `listingId` | UUID from `parse extension target`. |
| `contractVersion` | Exact version from that target response. |
| `url` | Complete HTTP(S) URL belonging to the target source. |
| `endpoints` | Array of capability requests, using the fields below. |
| `notes` | The overall goal, current workflow, and example uses. An empty string is accepted. |

Each entry in `endpoints` has exactly four fields:

| Field | Value |
|---|---|
| `id` | UUID unique within the request, retained across retries. |
| `purpose` | Nonempty description of the missing capability and desired outcome. |
| `inputs` | Prose describing what callers know or need to choose, such as a search term or a listing selected from existing results. |
| `outputs` | Prose describing the information callers need and what it enables them to do. |

Use the [API brief](../../agent-data-build/references/api-brief.md) to describe the requested capabilities together. `inputs` and `outputs` are strings and can be empty. Generate UUIDs with the environment's UUID implementation, such as Node's `crypto.randomUUID()`.

The submission interface accepts 1–20 entries. Its trimmed string limits are: purpose 2,000 characters; inputs and outputs 4,000 each; notes 8,000; URL 4,096. Retain the full brief and report the constraint if it cannot fit.

Save the complete payload before submission. Reuse it unchanged when recovering an uncertain submission or retrying the same request. A changed proposal needs a new request identity and current target values; retain the previous request and its outcome.
