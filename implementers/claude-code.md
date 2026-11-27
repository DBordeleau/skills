# Tool mapping: Claude Code

**Status**: `verified` on 2026-11-27 with Claude Code 2.1.332. The dispatch check below passed on a real run at both effort levels. That run was headless, so it read each sub-agent's model and reasoning level from the sub-agent's record under `~/.claude/projects/` and not from `/tasks`.

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

From Claude Code 2.1.242, the `/tasks` list shows each running sub-agent's model and reasoning level. Read at https://code.claude.com/docs/en/sub-agents on 2026-11-26. The check reads both values there, so it needs that version or later.

1. **Generate the files.** Follow Job 1 step 6 in [AGENTS.md](../AGENTS.md) for Claude Code alone, and ignore the status, so that a tool can be checked while it is still `unverified`. Observe both files in `~/.claude/agents/`, each starting with its `Installed from` line.
2. **Start a new session on other values.** `claude --version` reports 2.1.242 or later. Start the session on a model and reasoning level that match neither row, such as `claude --model sonnet --effort low` against the default rows. A row can equal the session's own values, and then a sub-agent that inherited the session's values would look like one that honoured its type. A session started before step 1 still holds the old implementer types.
3. **Dispatch at each effort level.** In that session, ask for one background dispatch of `implementer-medium`, naming the type and passing no model or reasoning level, with the brief "Read every `SKILL.md` under `skills/` one file per call, then report how many you read." While it runs, open `/tasks`. Observe the model and reasoning level of the `medium` default row, not the values from step 2. Expand the dispatch call in the transcript and observe that its input names the type and carries no model or reasoning level. Repeat with `implementer-high` and observe the `high` default row.
4. **Replace one row.** Add `| Claude Code | medium | sonnet | high |` under "Implementer settings" in `~/.agents/implementers.md`, in the shape [settings.md](settings.md) gives and creating the section if it isn't there. Generate the files again as step 1 says. Start another new session on the values from step 2 and repeat the `implementer-medium` dispatch from step 3. Observe Sonnet and `high` in `/tasks`.
5. **Restore the row.** Remove the row you added and generate the files again as step 1 says. Observe `implementer-medium.md` back on the default row.

When every step shows what it names, set the status above to `verified`, with the date and the Claude Code version.
