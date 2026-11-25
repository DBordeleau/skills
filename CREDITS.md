# Credits

## unslop, from pstack

`skills/unslop` is `pstack/skills/unslop/SKILL.md` from [cursor/plugins](https://github.com/cursor/plugins/tree/main/pstack), by Lauren Tan (`poteto`), taken on 2026-11-25. MIT, Copyright (c) 2026 Lauren Tan; the licence is kept at [skills/unslop/LICENSE](skills/unslop/LICENSE).

The rule text is byte-identical to upstream, numbering gaps and all, so the stable rule ids other skills cite still mean what they say. Two local changes, both in the frontmatter:

- `disable-model-invocation` is dropped, so an agent can invoke the skill itself. Upstream expects a person to run `/unslop` on a finished piece of text; this workflow has agents apply it to everything they write.
- The description names the cases that trigger it: messages to the user, commit subjects, PR bodies, issue and ticket bodies, comments, specs, reports.

How it is wired in is in [policy/global.md](policy/global.md). Only `unslop` is taken from pstack; the rest of that plugin is not here.

## mattpocock/skills

Most of this repo began as a subset of [mattpocock/skills](https://github.com/mattpocock/skills) by Matt Pocock, taken on 2026-11-25 and adapted. Thanks to him for the originals.

Carried over close to as-written:

- `code-review`, `codebase-design`, `domain-modeling`, `tdd`, `writing-for-agents`
- `to-tickets` (label vocabulary and effort line changed)
- `implement-spec` (delegation and cleanup rules tightened)

Changed more substantially:

- `plan-with-me` — his `grilling`, with the "grill" framing dropped, and the `domain-modeling` pass moved to the end instead of running throughout. Replaces his `grill-me` and `grill-with-docs`.
- `to-spec` — now ends by recommending tickets or implementing as-is.
- `implement` — rewritten to delegate the build to sub-agents rather than doing it in the orchestrating context.
- `setup-workflow` — his `setup-matt-pocock-skills`, with the label vocabulary changed, the unused tracker fragments dropped, and the addition of a conventions doc, an environment doc, the branch-name hook, and committing its own output.

## pr

The `pr` skill is from Matt's repo, which credits [show-me](https://github.com/humanlayer/skills/blob/main/plugins/show-me/skills/show-me/SKILL.md) by Dex Horthy at Humanlayer. See [skills/pr/CREDITS.md](skills/pr/CREDITS.md).

## Everything else

The `smell baseline` in `code-review` is a condensation of the code smells in chapter 3 of Martin Fowler's _Refactoring_.
