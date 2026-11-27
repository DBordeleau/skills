# Tool mapping: Codex

**Status**: `verified` on 2026-11-26 with Codex 0.172.0. The dispatch check below passed on a real run at both effort levels.

**Installed when** `~/.codex/` exists.

**Files go to** `~/.codex/agents/`, one per implementer type: `implementer-medium.toml` and `implementer-high.toml`.

## Template

```toml
# Installed from DBordeleau/skills@{{commit}} (implementers/). Job 1 rewrites this file whole: edit the source there, not here.
name = "implementer-{{effort_level}}"
description = "Implementer for work dispatched at effort level {{effort_level}}. Not for general tasks."
model = "{{model}}"
model_reasoning_effort = "{{reasoning_level}}"
developer_instructions = '''
{{instructions}}
'''
```

| Placeholder | Value |
| --- | --- |
| `{{commit}}` | `git -C "$REPO" rev-parse --short HEAD` |
| `{{effort_level}}` | `medium` or `high` |
| `{{model}}` | the Model cell of the resolved row |
| `{{reasoning_level}}` | the Reasoning level cell of the resolved row |
| `{{instructions}}` | the instruction text from [instructions.md](instructions.md) |

The `Installed from` line is a TOML comment, so it stays out of the implementer's prompt. The instruction text sits in a literal string, which keeps its backticks and quotes as written and holds anything but three single quotes in a row.

## How Codex reads an implementer type

The first three facts were read at https://learn.chatgpt.com/docs/agent-configuration/subagents on 2026-11-26, where the pages under developers.openai.com/codex now redirect.

- A type in `~/.codex/agents/` needs no entry in the Codex config file, `~/.codex/config.toml`, so Job 1 never edits that file. An `[agents.implementer-medium]` or `[agents.implementer-high]` entry left there by an older install names the type a second time. Remove it by hand after copying the config file aside.
- The file's `model` and `model_reasoning_effort` beat a value passed at dispatch, the config file's `[agents]` defaults and the parent session's values. The policy still has the orchestrator pass neither.
- `model_reasoning_effort` takes `low`, `medium`, `high`, `xhigh`, `max` or `ultra`, and the model decides which of those it supports.
- Open issues 32587, 20077 and 54896 on the Codex tracker report sub-agents running on the parent session's model and reasoning level. Codex 0.172.0 didn't do that on 2026-11-26. From a session on `gpt-6-luna` and `low`, each implementer type ran on its own row.

## What Codex can't set

Two facts limit what a setting does. Both were read at https://learn.chatgpt.com/docs/agent-configuration/subagents on 2026-11-26.

- An agent file can't restrict tools. It has no list of tools an implementer may not call, only feature keys such as `features.shell_tool` to switch off. The template disallows nothing, so a Codex implementer can start sub-agents of its own, and a Codex entry under "Disallowed tools" in the machine settings has no effect.
- Sandbox, approval, provider and MCP settings in an agent file don't override the parent session's. The template sets none of them.

## Dispatch check

[docs/agents/environment.md](../docs/agents/environment.md#checks) says when to run this check and what to do when a step fails.

Codex keeps one record per thread, a JSONL file at `~/.codex/sessions/<yyyy>/<mm>/<dd>/rollout-<time>-<thread id>.jsonl`. A sub-agent gets its own file. Its first line is a `session_meta` whose `payload.source.subagent.thread_spawn` holds `agent_role` and `parent_thread_id`, and a `turn_context` line holds the `model` and `effort` a turn ran on. That record is what this check reads. A sub-agent spawned with `fork_turns` set to `all`, the default, also carries a copy of the parent's history, with the parent's own `session_meta` and `turn_context` lines. Read the sub-agent's values from the last `turn_context` line in its file.

1. **Generate the files.** Follow Job 1 step 6 in [AGENTS.md](../AGENTS.md) for Codex alone, and ignore the status, so that a tool can be checked while it is still `unverified`. Observe both files in `~/.codex/agents/`, each starting with its `Installed from` line, and no `[agents.implementer-medium]` or `[agents.implementer-high]` entry in `~/.codex/config.toml`.
2. **Start a new session on other values.** `codex --version` reports 0.172.0 or later. Start the session on a model and reasoning level that match neither row, such as `codex exec -C "$REPO" -m gpt-6-luna -c model_reasoning_effort=low "<prompt>"`. A row can equal the config file's own default, and then a sub-agent that inherited the session's values would look like one that honoured its type.
3. **Dispatch at each effort level.** In that session, ask for one dispatch of `implementer-medium`, naming the type and passing no model or reasoning level, with the brief "Read `README.md`, change nothing, and report its first heading." In the session's own record, observe a `spawn_agent` call whose arguments hold `agent_type` set to `implementer-medium` and no `model` or `reasoning_effort`, and a `turn_context` on the values from step 2. In the sub-agent's record, observe `agent_role` set to `implementer-medium`, `parent_thread_id` set to the session's thread id, and a last `turn_context` on the `medium` default row. Repeat in another new session with `implementer-high`, and observe the `high` default row.

This check has no step that replaces a row of the implementer settings. Resolving rows is Job 1's work and the same for every tool, and the Claude Code dispatch check covers it. Step 2 is what shows a row's values reach the run here.

When every step shows what it names, set the status above to `verified`, with the date and the Codex version.
