# Tool mapping: Antigravity

**Status**: `unverified`. Nobody has run the dispatch check below, and the field spellings in the template need one check against Antigravity's own help output before the status changes. It becomes `verified` once the check passes on a real run, with the date and the Antigravity version written beside it, and the implementer settings gain Antigravity's two rows then.

**Installed when** `~/.gemini/` exists. Other Gemini tools may create that directory too, so step 1 of the dispatch check confirms it is Antigravity's.

**Files go to** `~/.gemini/config/agents/`, one directory per implementer type, each holding one `agent.md`: `implementer-medium/agent.md` and `implementer-high/agent.md`.

The facts below were read at https://antigravity.google/docs/subagents.md and https://antigravity.google/docs/models.md on 2026-11-26, through a fetch that summarised the pages. Exact field spellings are unconfirmed.

## Template

Fill the template once per effort level and write the result as the whole file.

```markdown
---
# Installed from DBordeleau/skills@{{commit}} (implementers/). Job 1 rewrites this file whole: edit the source there, not here.
name: implementer-{{effort_level}}
description: Implementer for work dispatched at effort level {{effort_level}}. Not for general tasks.
model: {{model}}
---

{{instructions}}
```

| Placeholder | Value |
| --- | --- |
| `{{commit}}` | `git -C "$REPO" rev-parse --short HEAD` |
| `{{effort_level}}` | `medium` or `high` |
| `{{model}}` | the Model cell of the resolved row: `inherit`, `flash` or `pro` |
| `{{instructions}}` | the instruction text from [instructions.md](instructions.md) |

The `Installed from` line is a YAML comment inside the frontmatter. The `name` and `description` field spellings are unconfirmed, and so is whether the file accepts a comment.

## What Antigravity can't set

- An agent can start a sub-agent itself, by role. Read at https://antigravity.google/docs/subagents.md on 2026-11-26.
- An agent type is a markdown file with YAML frontmatter at `~/.gemini/config/agents/<name>/agent.md`. Read at https://antigravity.google/docs/subagents.md on 2026-11-26.
- The model field takes `inherit`, `flash` or `pro`. Read at https://antigravity.google/docs/models.md on 2026-11-26.
- No field sets a reasoning level. A sub-agent runs at the conversation's reasoning level, so a row's Reasoning level cell has no effect in Antigravity and the orchestrator gives the notice. Read at https://antigravity.google/docs/models.md on 2026-11-26.

## Dispatch check

Run it before changing the status, and after any change to this tool mapping. Each step names what to observe. A step whose observation differs is a failure: correct the template or the facts above, then run the check again from step 1.

1. **Check the field spellings.** Read Antigravity's own help output for agent files. Observe the spelling of each field in the template and the values the model field takes, and correct the template where they differ. Observe also that Antigravity, and no other Gemini tool, reads `~/.gemini/config/agents/`.
2. **Generate the files.** Fill the template once per effort level, by hand, with `{{model}}` set to `flash` for `medium` and `pro` for `high`, and write both files. Observe both files, each starting with its `Installed from` line, and observe that Antigravity lists both types with no frontmatter error.
3. **Start a new conversation.** Note the Antigravity version.
4. **Dispatch at each effort level.** Ask for one dispatch of `implementer-medium`, naming the type and nothing else, with the brief "Read every `SKILL.md` under `skills/` one file per call, then report how many you read." Find where Antigravity records the model a sub-agent ran on, write that place into this step, and observe `flash`. Repeat with `implementer-high` and observe `pro`. Observe in the same record that both ran at the conversation's reasoning level.

When every step shows what it names, set the status above to `verified` with the date and the Antigravity version, and add Antigravity's two rows to [settings.md](settings.md).
