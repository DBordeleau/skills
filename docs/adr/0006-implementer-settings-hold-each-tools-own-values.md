# Implementer settings hold each tool's own values

A row of the implementer settings holds the model name and the reasoning level in the spelling the tool itself accepts. We did not define a shared vocabulary for each tool mapping to translate. Model names have no common scale, and the lists of reasoning levels differ per tool: in November 2026 Codex has `ultra`, Pi has `minimal`, and an Antigravity agent file sets no reasoning level at all. A translation table would be a guess that breaks when a tool adds a level.

This is why effort level and reasoning level are two terms. `Effort: high` on a ticket may map to `high` in one tool and `xhigh` in another.

A cell may hold an alias or a full model ID. In Claude Code `opus` resolves to the newest Opus the installed version knows, and a full ID pins one version.
