# The workflow

How the skills fit together, and which one to reach for when. Read a skill's own `SKILL.md` for how to run it.

```
idea ──▶ /plan-with-me ──▶ /to-spec ──┬──▶ /implement ───────────┐
         (interview +       (spec on   │    (one spec, delegated) │
          glossary/ADRs)     tracker,  │                          ├──▶ code-review ──▶ pr ──▶ merge
                             advises)  └──▶ /to-tickets ──▶ /implement-spec
                                            (vertical slices,     (task graph, fanned
                                             blocking edges)       across sub-agents)

bug ──▶ report-bug ──▶ /diagnose ──────────┬──▶ fix now ───────────▶ code-review ──▶ pr ──▶ merge
        (bug report    (reproduction,      │    (fix spec, then one
         on tracker)    root cause, two    │     implementer)
                        gates)             └──▶ spec and stop ──▶ /implement or /to-tickets
                                                (fix spec on tracker)
```

The upper diagram is the feature path and the lower one is the bug path. They meet at `code-review`.

## The steps

**`/plan-with-me`** runs an interview that works a design tree until the frontier is empty, then records what got settled as glossary terms and ADRs. Start here whenever the shape of the work is still open. The agent finds and presents facts so you make decisions.

**`/prototype`** writes throwaway code to answer one design question. Use it only when a question survived the interview because nobody has seen the thing run yet. It is not in this repo: install it from [mattpocock/skills](https://github.com/mattpocock/skills) if you want it.

**`/to-spec`** turns the conversation into a spec on the issue tracker. It does not interview you. It synthesizes what you already discussed. It ends by recommending either tickets or implementing the spec as it stands, and explains its reasoning.

**`/to-tickets`** breaks a spec into tracer-bullet tickets, each a complete vertical slice that declares the tickets blocking it. Run it when `/to-spec` says to, or when you disagree with `/to-spec`.

**`/implement`** builds one spec or one ticket. Sub-agents do the work while the orchestrator holds only the plan. Use it when the work fits a single pass.

**`/implement-spec`** builds a whole spec's ticket graph. It works the frontier across concurrent implementer sub-agents, each on its own worktree, and merges them onto one integration branch.

**`code-review`** runs two reviews of the diff in parallel. Standards asks whether the code follows this repo's documented standards plus a fixed smell baseline, and runs three test checks in every repo: a second test of one behaviour, a test that imports past the interface it tests, and setup copied from another test file. Spec asks whether the code does what the issue asked for, and whether each test sits at the testing seam the spec names. The two are reported side by side and never merged, so one cannot mask the other. Both implement skills call it before they finish.

**`pr`** writes the PR body: a summary of what the change makes possible, before and after evidence, and a note on whether the merge is a one-way or two-way door.

## The bug path

**`report-bug`** files a bug report, which describes a defect as someone observed it, before anyone has investigated it. It proposes no fix. Run `/report-bug` when you see a bug. Agents use it too, for a defect they find outside their task. A sub-agent puts the defect in its report, and the session you are talking to files every bug report once, at the end of its run. Each bug report says whether it has a reproduction, and the session lists what it filed in its closing message and in the PR body.

**`/diagnose`** takes one bug report to a root cause you have confirmed. It runs only when you invoke it. An investigator sub-agent works in a throwaway worktree and builds a reproduction before it forms any theory. The run stops at two gates and waits for your answer at each:

1. A root cause reaches you only with a reproduction beside it. With no reproduction, the run takes the cannot-reproduce exit.
2. `/diagnose` designs no fix until you confirm the root cause, choose which other places with the same mistake are in scope, and confirm where the regression test sits.

Then you pick one exit:

- **Fix now**, the default when the fix is one slice with no open design decision. `/diagnose` rewrites the bug report as a fix spec, builds the fix in one implementer sub-agent by the `## Fixing a bug` rules in `tdd`, then calls `code-review` and `pr`.
- **Spec and stop**, when the fix needs a design decision, needs splitting into tickets, or should wait. `/diagnose` leaves a fix spec on the tracker, ready to implement. Run `/implement` on it in a fresh session, or `/to-tickets` when it needs splitting.
- **Won't fix**, for behaviour that is intended or not worth fixing. The issue closes with the reason.
- **Not a bug**, for a missing feature or a design change. The issue loses its bug label and goes to `/plan-with-me`.
- **Cannot reproduce**, when the investigator found no reproduction. The issue gets a comment saying what is missing and is marked as needing info. Answer on the issue and rerun `/diagnose`.

A fix spec states the root cause, the fix and the regression test. `/implement` builds it on a `fix` branch at the testing seam the fix spec names, without asking about it again. When the regression test passes before the fix, or three attempts fail, the root cause is probably wrong. `/implement` then sends the issue back to `/diagnose` with the evidence. `code-review` checks a bug fix against its fix spec, including that the regression test exists and was seen to fail, and `pr` puts the test's real failing and passing output in the PR body.

## Where the testing seam is decided

A testing seam is the seam a test goes through, and each behaviour gets its tests at one. You agree it once, in `/to-spec` for a feature or in `/diagnose` for a bug. It is then written in the spec's seams list, in each ticket's testing line, or in the fix spec's `## Regression test`. Implementers read it there and don't ask you again. `code-review` checks that every test in the diff sits at it. When a test lands at the wrong seam, check what was agreed, then what was written, then what the review reported.

## Starting from the codebase

**`/improve-codebase-architecture`** scans a repo for deepening opportunities, the refactors that turn a shallow module into a deep one, and writes them up as an HTML report you can read. Pick a candidate and it runs the `/plan-with-me` interview over that one, which puts you back on the loop above at `/to-spec`. Use it when you want the next piece of work to come out of the code rather than out of an idea.

## Supporting skills

Other skills consult these. You do not run them as sessions.

- **`unslop`** cuts AI tells from anything written for a human. Every skill that publishes prose invokes it, and so does any agent about to send a message. See [policy/global.md](policy/global.md).
- **`tdd`** covers what a good test is, where tests go, the anti-patterns, and the rules of the loop from failing test to passing code. Its `## Fixing a bug` section adds the rules for a bug fix.
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
