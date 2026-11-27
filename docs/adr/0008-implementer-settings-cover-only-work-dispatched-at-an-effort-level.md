# Implementer settings cover only work dispatched at an effort level

The implementer settings apply to every dispatch that names an effort level. Today that means tickets, mergers, the fix-up after `code-review` and the `diagnose` investigator. Exploration and review sub-agents name no effort level, so they run on the orchestrating session's own model and reasoning level, and the implementer settings do not reach them.

Bringing them in means each skill that dispatches one has to name an effort level, and someone has to decide per dispatch whether exploring and reviewing are `medium` or `high`. We left that out so the tool-specific part stays in one line of policy and no skill changes. The cost is known. On an expensive session model, explorers and reviewers are the sub-agents the implementer settings can't change.
