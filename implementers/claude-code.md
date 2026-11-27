# Tool mapping: Claude Code

**Status**: `unverified`. It becomes `verified` once the dispatch check below passes on a real run, with the date and the Claude Code version written beside it.

**Installed when** `~/.claude/` exists.

**Files go to** `~/.claude/agents/`, one per implementer type: `implementer-medium.md` and `implementer-high.md`.

## Template

Fill the template once per effort level and write the result as the whole file.

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

## What Claude Code can't set

Nothing the implementer settings hold. Four facts limit what a setting does, read at https://code.claude.com/docs/en/sub-agents on 2026-11-26:

- A model or effort passed on the dispatch call beats the file. The policy has the orchestrator pass neither.
- The effort field takes `low`, `medium`, `high`, `xhigh` or `max`, and the model decides which of those it supports.
- `opus` resolves to the newest Opus the installed version knows, and the provider can change the answer. When the orchestrating session already runs on an Opus version, a type set to `opus` uses that version.
- The `/tasks` list shows each running sub-agent's model and effort from Claude Code 2.1.242. The dispatch check needs that version or later.

## Dispatch check

Run it after any change to this tool mapping, and before changing its status. Each step names what to observe. A step whose observation differs is a failure: correct the template or the facts above, then run the check again from step 1.

1. **Generate the files.** Follow Job 1 step 6 in [AGENTS.md](../AGENTS.md) for Claude Code alone, and ignore the status, so that a tool can be checked while it is still `unverified`. Observe both files in `~/.claude/agents/`, each starting with its `Installed from` line.
2. **Start a new session.** `claude --version` reports 2.1.242 or later. A session started before step 1 still holds the old implementer types.
3. **Dispatch at each effort level.** In the new session, ask for one background dispatch of `implementer-medium`, naming the type and passing no model or effort, with the brief "Read every `SKILL.md` under `skills/` one file per call, then report how many you read." While it runs, open `/tasks`. Observe the model and effort of the `medium` default row. Expand the dispatch call in the transcript and observe that its input names the type and carries no model or effort. Repeat with `implementer-high` and observe the `high` default row.
4. **Replace one row.** Add `| Claude Code | medium | sonnet | low |` under "Implementer settings" in `~/.agents/implementers.md`, in the shape [settings.md](settings.md) gives and creating the section if it isn't there. Rerun Job 1 step 6, start another new session and repeat the `implementer-medium` dispatch from step 3. Observe Sonnet and `low` in `/tasks`.
5. **Restore the row.** Remove the row you added and rerun Job 1 step 6. Observe `implementer-medium.md` back on the default row.

When every step shows what it names, set the status above to `verified`, with the date and the Claude Code version.
