# agent-data skills

Skills for discovering website APIs, requesting new capabilities, and building recurring data workflows with agent-data.

| Skill | Purpose |
|---|---|
| `agent-data-search` | Find existing APIs and inspect their current contracts. |
| `agent-data-build` | Turn a desired workflow into a request for a new API. |
| `agent-data-extend` | Request additional capabilities for an existing API. |
| `agent-data-monitor` | Combine retrieval, state, scheduling, and delivery into recurring workflows. |
| `agent-data-cli` | Install, authenticate, call APIs, notify, and recover from CLI errors. |

## Install

Requires Node.js 22.20.0 or later. Install through [Vercel's skills CLI](https://github.com/vercel-labs/skills):

```sh
npx skills add agent-data/skills --global --agent codex
```

Select all five skills; they link to one another. Use `--agent claude-code`, `--agent openclaw`, or `--agent hermes-agent` for other hosts. Multiple names can follow `--agent`. Omit `--global` to install into the current project. For a NanoClaw project, run the project-scope installation from its repository with `--agent claude-code`.

Install the agent-data CLI and configure authentication as well with:

```sh
npx --yes agent-data@latest init --codex
```

The CLI's setup command installs all five skills for the selected host. Skill files and references live here; the CLI does not carry a separate copy. Restart the coding agent after installation or an update.

## Refresh

```sh
npx skills update agent-data-cli agent-data-search agent-data-build agent-data-extend agent-data-monitor --global
```

CLI upgrades and skill updates are independent. In Vercel skills 1.7.4, `check` is an alias for `update` and can change installed files.

For project installations, repeat the original explicit-target install from that project. For NanoClaw:

```sh
npx skills add agent-data/skills --agent claude-code --skill agent-data-cli agent-data-search agent-data-build agent-data-extend agent-data-monitor --yes
```


For a legacy standalone `agent-data` skill:

```sh
npx skills list --global
npx skills remove agent-data --global --agent codex
npx skills add agent-data/skills --global --agent codex
```

Use the agent and scope where the old copy is installed. Copied legacy files do not automatically gain update tracking. If Vercel does not recognize an old installation path, remove that specific copy through its original installation method.

## Compatibility

These skills use the Agent Skills format. Codex, Claude Code, OpenClaw, and Hermes installation paths are delegated to Vercel; NanoClaw uses its project's Claude Code skill layout. Installation and asset resolution are distinct from validation inside an agent's runtime. Consult current CLI help and the selected API's docs before executing commands.

Supporting files are inside each skill directory. Keep all five skill directories together so sibling links resolve.
