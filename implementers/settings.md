# Implementer settings

The model and reasoning level each tool's implementer types run on. Job 1 step 6 in [AGENTS.md](../AGENTS.md) reads this file, the machine settings and each tool mapping, and writes the implementer types.

## Defaults

One row per tool and effort level. A cell holds the value in the spelling that tool accepts: an alias follows the newest model the tool knows, and a full model ID pins one version. A tool whose tool mapping is `unverified` has no rows.

| Tool | Effort level | Model | Reasoning level |
| --- | --- | --- | --- |
| Claude Code | medium | opus | medium |
| Claude Code | high | opus | high |

## Machine settings

The machine settings are an optional file at `~/.agents/implementers.md`. The user writes it, and Job 1 reads it and leaves it unchanged. It has two sections, and either may be left out:

```markdown
# Machine settings

## Implementer settings

| Tool | Effort level | Model | Reasoning level |
| --- | --- | --- | --- |
| Claude Code | medium | sonnet | high |

## Disallowed tools

| Tool | Disallowed tools |
| --- | --- |
| Claude Code | ToolA, ToolB |
```

- **Implementer settings** holds only the rows the user wants different on this machine. A row replaces the whole default row with the same tool and effort level, so leave the others out and they follow the defaults.
- **Disallowed tools** names, per tool, the tool's own tools an implementer type may not call, comma-separated, in addition to the `Agent` the template already disallows. Tools that come from one installation, such as a desktop app's tools or one account's connector, go here and never in the repo.
