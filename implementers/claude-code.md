# Tool mapping: Claude Code

**Status**: `verified` on 2026-11-27 with Claude Code 2.1.332. The dispatch check below passed on a real run at both effort levels.

**Installed when** `~/.claude/` exists.

**Files go to** `~/.claude/agents/`, one per implementer type: `implementer-medium.md` and `implementer-high.md`.

## Template

```markdown
---
# Installed from DBordeleau/skills@{{commit}} (implementers/). Job 1 rewrites this file whole: edit the source there, not here.
name: implementer-{{effort_level}}
description: Implementer for work dispatched at effort level {{effort_level}}. Not for general tasks.
model: {{model}}
effort: {{reasoning_level}}
disallowedTools: {{disallowed_tools}}
---

{{instructions}}
```

| Placeholder | Value |
| --- | --- |
| `{{commit}}` | `git -C "$REPO" rev-parse --short HEAD` |
| `{{effort_level}}` | `medium` or `high` |
| `{{model}}` | the Model cell of the resolved row |
| `{{reasoning_level}}` | the Reasoning level cell of the resolved row |
| `{{disallowed_tools}}` | `Agent`, followed by the Claude Code entry under "Disallowed tools" in the machine settings, joined with `, ` and with no name twice |
| `{{instructions}}` | the instruction text from [instructions.md](instructions.md) |

The `Installed from` line is a YAML comment inside the frontmatter, so it stays out of the implementer's prompt. `Agent` is the one default disallowed tool, because an implementer starts no sub-agents.

## How Claude Code reads an implementer type

Both facts were read at https://code.claude.com/docs/en/sub-agents on 2026-11-26.

- The `effort` field takes `low`, `medium`, `high`, `xhigh` or `max`, and the model decides which of those it supports.
- `opus` resolves to the newest Opus the installed version knows, and the provider can change the answer. When the orchestrating session already runs on an Opus version, a type set to `opus` uses that version.

## What Claude Code can't set

Nothing the implementer settings hold. One fact limits what a setting does, read at https://code.claude.com/docs/en/sub-agents on 2026-11-26:

- A model or reasoning level passed on the dispatch call beats the file. The policy has the orchestrator pass neither.

## Dispatch check

[docs/agents/environment.md](../docs/agents/environment.md#checks) says when to run this check and what to do when a step fails.

Claude Code keeps one record per session, a JSONL file at `~/.claude/projects/<project>/<session id>.jsonl`, where `<project>` is named after the session's working directory. Each `assistant` line holds the `message.model` and `effort` that turn ran on. A sub-agent gets two files of its own under `<session id>/subagents/` beside that record. `agent-<agent id>.meta.json` holds `agentType`, the `model` the sub-agent ran on, and `modelNotNamed`, which is `true` when the dispatch call named no model. `agent-<agent id>.jsonl` holds the sub-agent's own `assistant` lines. Those records are what this check reads, and they give a model as the full model ID its alias resolved to. Observed with Claude Code 2.1.332 on 2026-11-27.

In an interactive session, the `/tasks` list shows a running sub-agent's model and reasoning level too, from Claude Code 2.1.242. Read at https://code.claude.com/docs/en/sub-agents on 2026-11-26.

1. **Generate the files.** Follow Job 1 step 6 in [AGENTS.md](../AGENTS.md) for Claude Code alone, and ignore the status, so that a tool can be checked while it is still `unverified`. Observe both files in `~/.claude/agents/`, each starting with its `Installed from` line.
2. **Start a new session on other values.** `claude --version` reports 2.1.332 or later. Start the session on a model and reasoning level that match neither row, such as `claude -p --model sonnet --effort low --output-format stream-json --verbose "<prompt>"` against the default rows. The output's `init` line gives the session id. A row can equal the session's own values, and then a sub-agent that inherited the session's values would look like one that honoured its type. A session started before step 1 still holds the old implementer types.
3. **Dispatch at each effort level.** In that session, ask for one dispatch of `implementer-medium`, naming the type and passing no model or reasoning level, with the brief "Reply with the single word ok." In the session's own record, observe an `Agent` call whose input holds `subagent_type` set to `implementer-medium` and no `model` or `effort`, and `assistant` lines on the values from step 2. In the sub-agent's records, observe `agentType` set to `implementer-medium`, `modelNotNamed` set to `true`, and the model and `effort` of the `medium` default row. Repeat in another new session with `implementer-high`, and observe the `high` default row.
4. **Replace one row.** Add `| Claude Code | medium | sonnet | high |` under "Implementer settings" in `~/.agents/implementers.md`, in the shape [settings.md](settings.md) gives and creating the section if it isn't there. Generate the files again as step 1 says. Start another new session on the values from step 2 and repeat the `implementer-medium` dispatch from step 3. Observe a Sonnet model and `high` in the sub-agent's records.
5. **Restore the row.** Remove the row you added and generate the files again as step 1 says. Observe `implementer-medium.md` back on the default row.

When every step shows what it names, set the status above to `verified`, with the date and the Claude Code version.
