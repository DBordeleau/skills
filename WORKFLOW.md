# The workflow

How the skills fit together, and which one to reach for when. Read a skill's own `SKILL.md` for how to run it.

```
idea ──▶ /plan-with-me ──▶ /to-spec ──┬──▶ /implement ───────────┐
         (interview +       (spec on   │    (one spec, delegated) │
          glossary/ADRs)     tracker,  │                          ├──▶ code-review ──▶ pr ──▶ merge
                             advises)  └──▶ /to-tickets ──▶ /implement-spec
                                            (vertical slices,     (task graph, fanned
                                             blocking edges)       across sub-agents)
```

## The steps

**`/plan-with-me`** runs an interview that works a design tree until the frontier is empty, then records what got settled as glossary terms and ADRs. Start here whenever the shape of the work is still open. The agent finds and presents facts so you make decisions.

**`/prototype`** writes throwaway code to answer one design question. Use it only when a question survived the interview because nobody has seen the thing run yet.

**`/to-spec`** turns the conversation into a spec on the issue tracker. It does not interview you. It synthesizes what you already discussed. It ends by recommending either tickets or implementing the spec as it stands, and explains its reasoning.

**`/to-tickets`** breaks a spec into tracer-bullet tickets, each a complete vertical slice that declares the tickets blocking it. Run it when `/to-spec` says to, or when you disagree with `/to-spec`.

**`/implement`** builds one spec or one ticket. Sub-agents do the work while the orchestrator holds only the plan. Use it when the work fits a single pass.

**`/implement-spec`** builds a whole spec's ticket graph. It works the frontier across concurrent implementer sub-agents, each on its own worktree, and merges them onto one integration branch.

**`code-review`** runs two reviews of the diff in parallel. Standards asks whether the code follows this repo's documented standards plus a fixed smell baseline. Spec asks whether the code does what the issue asked for. The two are reported side by side and never merged, so one cannot mask the other. Both implement skills call it before they finish.

**`pr`** writes the PR body: a diagram or diff-sketch summary, before and after evidence, and a note on whether the merge is a one-way or two-way door.

## Starting from the codebase

**`/improve-codebase-architecture`** scans a repo for deepening opportunities, the refactors that turn a shallow module into a deep one, and writes them up as an HTML report you can read. Pick a candidate and it runs the `/plan-with-me` interview over that one, which puts you back on the loop above at `/to-spec`. Use it when you want the next piece of work to come out of the code rather than out of an idea.

## Supporting skills

Other skills consult these. You do not run them as sessions.

- **`unslop`** cuts AI tells from anything written for a human. Every skill that publishes prose invokes it, and so does any agent about to send a message. See [policy/global.md](policy/global.md).
- **`tdd`** covers what a good test is, where seams go, the anti-patterns, and the rules of the loop from failing test to passing code.
- **`domain-modeling`** builds and sharpens the glossary and the ADRs. `/plan-with-me` reaches it.
- **`codebase-design`** holds the shared vocabulary for module depth, interfaces, seams and leverage.
- **`writing-for-agents`** covers how to write a skill, an `AGENTS.md`, or a `CLAUDE.md`. Read it before changing anything in this repo.

## Per-repo setup

**`/setup-workflow`** sets a repo up, then runs again whenever a fragment in the skills repo changes. It writes the tracked config the rest of the loop reads: `docs/agents/issue-tracker.md`, `labels.md`, `conventions.md`, `domain.md` and `environment.md`, the `## Agent skills` block in `AGENTS.md`, the labels themselves, and the branch-name hook.

Those files are copies, so a change in the skills repo reaches no repo until you re-run it there. A later run merges only what changed upstream and keeps whatever was filled in for that repo. [README.md](README.md#getting-a-change-onto-the-other-machines-and-repos) has the other three update cases.

## Three principles to guide you

1. **Decide before you build.** Every skill before `/implement` makes the next one cheaper. The interview costs less than the rewrite.
2. **Delegate the work, keep the plan.** The orchestrating context is re-sent on every turn, so a session costs its turns times its context size. Reading files, running tests and writing code belong in sub-agents. The window that coordinates them holds pointers.
3. **Write like a person.** Anything a human reads goes through `unslop` first. That covers the messages an agent sends while working, and the artifacts it leaves behind.
