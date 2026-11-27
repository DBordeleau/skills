# One source generates each tool's implementer types

The implementer types lived only on one machine, written by hand in `~/.claude/agents/` and `~/.codex/agent-roles/`. With two tools and two effort levels, the two medium definitions had already drifted apart. We decided the repo holds one source, made of the implementer settings, one instruction text and a tool mapping per tool, and Job 1 writes each tool's native files from it. Every tool gets the same two implementer types, `implementer-medium` and `implementer-high`, so the policy names them once for all tools.

The orchestrator dispatches an implementer type by name and passes no model or reasoning level on the call. In Claude Code a value passed at dispatch beats the one in the type's file, so passing one would defeat the implementer settings without anyone seeing it.

## Considered options

- **Commit a finished file per tool and effort level.** This is what issue 5 first asked for. Rejected. Changing a model means editing two files per tool, and the instruction text exists once per file.
- **Ship no files and pass the model at dispatch.** Rejected. Of the four tools checked in November 2026, only Claude Code documents a model on the dispatch call. All four load a definition file per type.
- **A type per role, such as merger or investigator.** Rejected. It multiplies the files by the number of roles and changes no model or reasoning level. The brief says what the work is.
