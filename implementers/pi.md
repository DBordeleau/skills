# Tool mapping: Pi

**Status**: `unverified`. Nobody has run the dispatch check below. It becomes `verified` once the check passes on a real run, with the date and the Pi version written beside it, and the implementer settings gain Pi's two rows then.

**Installed when** `~/.pi/` exists.

**Files go to** `~/.pi/agent/agents/`, one per implementer type: `implementer-medium.md` and `implementer-high.md`.

Pi has no sub-agents of its own. The implementer types work only with the example `subagent` extension installed, since that extension's tool starts each one as a child `pi` process. Without the extension, implementers run in the session. Read in `earendil-works/pi` under `packages/coding-agent/docs/` and `examples/extensions/subagent` on 2026-11-26.

The project moved to `earendil-works/pi` from `badlogic/pi-mono`, and the package is now `@earendil-works/pi-coding-agent`. The command is still `pi`. Read in `earendil-works/pi` under `packages/coding-agent/docs/` and `examples/extensions/subagent` on 2026-11-26.

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
| `{{model}}` | the Model cell of the resolved row |
| `{{instructions}}` | the instruction text from [instructions.md](instructions.md) |

The `Installed from` line is a YAML comment inside the frontmatter. Whether the extension's frontmatter parser accepts a comment is unconfirmed, and step 1 of the dispatch check finds out. The template leaves out the tools field. Whether Pi then gives the implementer every tool, and whether the field lists allowed or disallowed tools, is unconfirmed, so the machine settings' "Disallowed tools" have nowhere to go yet.

## What Pi can't set

- With the `subagent` extension, an agent type is a markdown file with YAML frontmatter in `~/.pi/agent/agents/`. Its fields are the name, the description, the tools and the model. Read in `earendil-works/pi` under `packages/coding-agent/docs/` and `examples/extensions/subagent` on 2026-11-26.
- No field sets a reasoning level. Read in `earendil-works/pi` under `packages/coding-agent/docs/` and `examples/extensions/subagent` on 2026-11-26.
- The command line accepts a reasoning level as a suffix on the model, so the same suffix in the model field may set both. Read in `earendil-works/pi` under `packages/coding-agent/docs/` and `examples/extensions/subagent` on 2026-11-26. Nobody has tested the suffix in the model field. Until the dispatch check shows it works, the Reasoning level cell has no place in the file and the orchestrator gives the notice.
- The extension's tool takes no model or reasoning level per call, so the file is the only place to set them. Read in `earendil-works/pi` under `packages/coding-agent/docs/` and `examples/extensions/subagent` on 2026-11-26.

## Dispatch check

Run it before changing the status, and after any change to this tool mapping. Each step names what to observe. A step whose observation differs is a failure: correct the template or the facts above, then run the check again from step 1.

1. **Generate the files.** Install the example `subagent` extension. Fill the template once per effort level, by hand, with `{{model}}` set to a model this Pi installation can run, and write both files to `~/.pi/agent/agents/`. Observe both files there, each starting with its `Installed from` line, and observe that the extension lists both types with no frontmatter error.
2. **Start a new session.** Run `pi --version` and note the version.
3. **Dispatch at each effort level.** Ask for one dispatch of `implementer-medium` through the extension's tool, naming the type and nothing else, with the brief "Read every `SKILL.md` under `skills/` one file per call, then report how many you read." Find where Pi records the model a child process ran on, write that place into this step, and observe the model from the file. Repeat with `implementer-high`.
4. **Try the reasoning-level suffix.** Put the suffix the command line accepts on the model in `implementer-medium.md`, start a new session and dispatch again. Observe in the same record whether the child process ran at that reasoning level. If it did, add a Reasoning level placeholder to the template and move this fact out of "What Pi can't set". If it didn't, write that down as a limit.

When every step shows what it names, set the status above to `verified` with the date and the Pi version, and add Pi's two rows to [settings.md](settings.md).
