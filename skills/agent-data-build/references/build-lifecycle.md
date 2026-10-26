# Follow a build without losing its identity

Use actual IDs returned by agent-data in place of the uppercase arguments below. Save the task, submission response, status responses, and event evidence together.

```bash
agent-data parse status PARSE_JOB_ID
agent-data parse events PARSE_JOB_ID --follow
```

Use the returned status, actions, polling guidance, error, and links to choose the next step. Following events can end when the agent session ends; check status again to determine publication. `--since-seq N` resumes event inspection from a saved sequence; the CLI also supports local event state. Read `parse events --help` for its resume options.

| Observed condition | Action |
|---|---|
| Build still running | Follow events or check status according to returned polling guidance. |
| Actual outstanding question | Read the full question, answer from established requirements/evidence, and ask the user only for a decision you cannot infer. Submit `agent-data parse respond PARSE_JOB_ID --interrupt-id INTERRUPT_ID --answer "ANSWER"`. |
| Blocked with eligible checkpoint | Inspect the blocker and server-advertised retry/resume eligibility. Resolve the prerequisite or establish why continuation can progress, then run `agent-data parse resume PARSE_JOB_ID` while the build is still needed for the task. |
| Failed, cancelled, or ineligible to resume | Retain the error and status. Report the blocker; do not repeatedly resume without changed prerequisites. |
| Public API reported ready/live | Read current docs and call the returned public API to verify publication and task fit. |

`respond` requires an actual interrupt ID. `resume` continues a blocked build from its checkpoint; it is not a general retry for every failed build. Do not resume an old build to overwrite a newer API.

After an ambiguous start or revision response, inspect saved output and `agent-data parse list --help`, then use `parse list` and candidate job statuses to recover the build. Match source, task, and submission time. If identity remains ambiguous, report that uncertainty before launching another build.

Each new `parse` invocation generates a fresh idempotency key. Its internal transport retry reuses that invocation's key; rerunning the shell command does not. A dry-run's printed key is not automatically reused by the next invocation. Saved public extension requests have a different recovery mechanism: retain and reuse their complete payload and `requestId` as described in [agent-data-extend](../../agent-data-extend/SKILL.md).
