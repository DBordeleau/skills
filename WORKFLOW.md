# The workflow

The loop these skills form, and which one to reach for when. Read this if you want the shape of the thing; read a skill's own `SKILL.md` for how to run it.

```
idea ──▶ /plan-with-me ──▶ /to-spec ──┬──▶ /implement ───────────┐
         (interview +       (spec on   │    (one spec, delegated) │
          glossary/ADRs)     tracker,  │                          ├──▶ code-review ──▶ pr ──▶ merge
                             advises)  └──▶ /to-tickets ──▶ /implement-spec
                                            (vertical slices,     (task graph, fanned
                                             blocking edges)       across sub-agents)
```

## The steps

**`/plan-with-me`** — an interview that works a design tree until the frontier is empty, then records what got settled as glossary terms and ADRs. Start here whenever the shape of the thing isn't obvious yet. Facts are the agent's job to find; decisions are yours.

**`/prototype`** — only when a question survived the interview because nobody has seen it run yet. Throwaway code to answer one design question.

**`/to-spec`** — turns the conversation into a spec on the issue tracker. No interview; pure synthesis of what you already discussed. It ends by recommending either tickets or implementing as-is, and says what decided it.

**`/to-tickets`** — breaks a spec into tracer-bullet tickets, each a complete vertical slice with its blocking edges declared. Run it when `/to-spec` says to, or when you disagree with it.

**`/implement`** — one spec or one ticket, built by sub-agents while the orchestrator holds only the plan. Use it when the work fits a single pass.

**`/implement-spec`** — a whole spec's ticket graph, worked frontier-first across concurrent implementer sub-agents on their own worktrees, merged onto one integration branch.

**`code-review`** — two parallel reviews of the diff: Standards (does it follow this repo's documented standards, plus a fixed smell baseline) and Spec (does it do what the issue asked). Reported side by side, never merged, so one axis can't mask the other. Both implement skills call it before finishing.

**`pr`** — the PR body: a diagram or diff-sketch summary, before/after evidence, and a one-way/two-way door risk note.

## Supporting skills

These are references other skills consult, not sessions you run:

- **`tdd`** — what a good test is, where seams go, the anti-patterns, the rules of the red → green loop.
- **`domain-modeling`** — building and sharpening the glossary and ADRs. Reached through `/plan-with-me`.
- **`codebase-design`** — the shared vocabulary for module depth, interfaces, seams and leverage.
- **`writing-for-agents`** — how to write a skill, an `AGENTS.md`, or a `CLAUDE.md`. Read it before changing anything in this repo.

## Per-repo setup

**`/setup-workflow`**, once per repo, writes the tracked config the rest of the loop reads: `docs/agents/{issue-tracker,labels,conventions,domain,environment}.md`, the `## Agent skills` block in `AGENTS.md`, the labels, and the branch-name hook.

## The two rules behind all of it

1. **Decide before you build.** Every skill before `/implement` exists to make the next one cheaper, and the interview is cheaper than the rewrite.
2. **Delegate the work, keep the plan.** The orchestrating context is re-sent every turn. Reading files, running tests and writing code belong in sub-agents; the window that coordinates them holds pointers.
