# Credits

## unslop, from pstack

`skills/unslop` is `pstack/skills/unslop/SKILL.md` from [cursor/plugins](https://github.com/cursor/plugins/tree/main/pstack), by Lauren Tan (`poteto`), taken on 2026-11-25. It is MIT licensed, Copyright (c) 2026 Lauren Tan. The license is kept at [skills/unslop/LICENSE](skills/unslop/LICENSE).

The rule text is byte-identical to upstream, numbering gaps included, so the rule ids other skills cite still point at the same rules. Two things changed, both in the frontmatter:

- This copy drops `disable-model-invocation`, so an agent can invoke the skill itself. Upstream expects a person to run `/unslop` on a finished piece of text. This workflow has agents apply it to everything they write.
- The description names the cases that trigger it: messages to the user, commit subjects, PR bodies, issue and ticket bodies, comments, specs and reports.

[policy/global.md](policy/global.md) says how it is wired in. Only `unslop` comes from pstack. The rest of that plugin is not here.

## mattpocock/skills

Most of this repo began as a subset of [mattpocock/skills](https://github.com/mattpocock/skills) by Matt Pocock, taken on 2026-11-25 and adapted. Thanks to him for the originals.

Carried over close to as written:

- `code-review`, `codebase-design`, `domain-modeling`, `tdd` and `writing-for-agents`
- `to-tickets`, with a changed label vocabulary and an added effort line
- `implement-spec`, with tighter delegation and cleanup rules
- `improve-codebase-architecture`, which now runs the `plan-with-me` interview where it used to call `grilling`

Rewritten or reframed:

- `plan-with-me` is his `grilling` without the "grill" framing, and it runs the `domain-modeling` pass at the end instead of throughout. It replaces his `grill-me` and `grill-with-docs`. It stays model-invokable, as `grilling` was, so `improve-codebase-architecture` can call it; a line in the body tells an agent not to start an interview on its own.
- `to-spec` now ends by recommending either tickets or implementing the spec as it stands.
- `implement` now delegates the build to sub-agents instead of doing it in the orchestrating context.
- `setup-workflow` is his `setup-matt-pocock-skills` with a changed label vocabulary and the unused tracker fragments dropped. It also writes a conventions doc and an environment doc, installs the branch-name hook, and commits its own output.

## The pr skill

`pr` comes from Matt's repo, which credits [show-me](https://github.com/humanlayer/skills/blob/main/plugins/show-me/skills/show-me/SKILL.md) by Dex Horthy at Humanlayer. See [skills/pr/CREDITS.md](skills/pr/CREDITS.md).

Its Summary section is replaced. Upstream offers a menu of structural views, and in practice the one that got picked was a diff sketch of the file tree, which describes the edit rather than the gain and goes stale on the next commit. The section now asks for one sentence and one visual that carries the benefit: a before and after table, a Mermaid flowchart or state diagram, or a numbers table. Structural views are still allowed, below the Summary, under their own heading. The Evidence and Merge Danger sections are unchanged.

## Everything else

The smell baseline in `code-review` condenses the code smells from chapter 3 of Martin Fowler's _Refactoring_.
