---
name: implement-spec
description: "Implement the result of /to-spec and /to-tickets in code."
disable-model-invocation: true
---

You have been provided a spec. This spec should have tickets associated with it, describing how to implement the spec.

Read `docs/agents/conventions.md` and `docs/agents/issue-tracker.md` before the first write. If they don't exist, tell the user to run `/setup-workflow`.

Call the Skill tool with `unslop` before the first thing you write that a human will read, and tell every sub-agent to do the same for its commit subjects and its report.

The **scope** is the tickets the user names alongside the spec, such as `147 (148, 149, 160)`; with none named, it is every ticket of the spec. Tickets outside the scope stay open and untouched. If a ticket in scope is blocked by an open ticket outside it, stop and ask the user before starting.

The goal is the scope implemented on a single **integration branch**, with every ticket in scope resolved the way the issue tracker closes work. The spec itself is resolved only when the scope leaves none of its tickets open.

The tickets are not a list of steps. They are a **task graph** with blocking relationships between them. This means there is always a **frontier** of tickets which are ready to be grabbed.

Start every subagent with a fresh context window. Pick each one's effort from its ticket's `Effort` line, as `docs/agents/conventions.md` maps it.

Communication to and from subagents should be sparse. Communicate primarily through **context pointers**: to the spec, tickets, research notes, and previous commits. Don't duplicate information already available via pointers. Ask each subagent to report back under 200 words.

You are the orchestrator: you hold the task graph and the pointers, not the code. Don't read source files, run the test suite, or run the typechecker yourself — a subagent does that and reports the result. Your context is re-sent on every turn, so what you pull into it you pay for hundreds of times over.

**Implementer subagents** should be run in the background where possible for maximum concurrency.

## Steps

1. Read the spec and the tickets in scope, with their comments, to understand the task graph.

2. (optional) Use an **exploration subagent** to conduct any exploration required by the tickets - relevant codebase files or external documentation. Ensure the exploration subagent can save files - it should save its markdown notes in a directory outside the repo, accessible by all future subagents. This lets **implementer subagents** focus on implementation rather than exploration.

3. Create the integration branch, named as `docs/agents/conventions.md` requires. If the issue tracker closes work through PRs, or the user asks for one, open a draft PR after the first merge in step 5 (a branch with no commits ahead of main can't open one), marked as closing the tickets in scope. It closes the spec too only when the scope leaves none of its tickets open; otherwise it references the spec.

4. Use a fresh **implementer subagent** for each ticket, each in its own worktree on its own branch. Point each brief at its ticket's testing line, which names the testing seam and seam builder the user confirmed for that slice. Each implementer subagent:
   - confirms its worktree is based on the integration branch before starting, and resets onto it if not;
   - reads its ticket's testing line and tests only at the testing seams it names. When a testing seam it was given can't observe the behaviour, the subagent stops and reports it, and you take its report to the user, who decides the testing seam;
   - calls the Skill tool with `tdd` to build the ticket, following its `## Fixing a bug` rules when the spec is a fix spec, and putting the output those rules say to keep in its report;
   - puts any defect it finds outside its ticket in its report, with its evidence, as `report-bug` describes for a subagent, and leaves it unfixed and unfiled;
   - merges the integration branch tip into its own branch before reporting done

5. Once an **implementer subagent** completes, merge its work to the integration branch with a **merger subagent**. The merger also brings the exploration notes up to date with the merged diff, creating them if step 2 was skipped: under a heading for the ticket, the files it added or moved, the interface it exports, and the test harnesses and fixtures it left; any earlier note the merge contradicts is rewritten in place. The merger is done when every note describes the integration branch as it stands after the merge.

6. If this changes the **frontier** of available tickets, kick off more **implementer subagents** to work on the new tickets, pointing each at the refreshed notes and at the sections for its blockers. This allows for maximum concurrency.

7. Once every ticket in scope is complete, call the Skill tool with `code-review` on the integration branch. On a fix spec, pass it the implementers' reports that hold the regression tests' output. Fix all issues raised by the code review in a single fresh **implementer subagent**. Defects it lists as older than the diff are bug reports to file in step 8, not issues to fix.

8. Call the Skill tool with `report-bug` once, for every bug the subagents and the review found outside their work. Then, if a draft PR exists, mark it ready for review. Otherwise, resolve each ticket in scope the way the issue tracker closes work, and report the integration branch. Write the PR body with the `pr` skill, as the user: no co-author or tool credit.

9. Clean up, as `docs/agents/conventions.md` requires: remove every **implementer subagent** worktree, prune stale worktrees, and delete the merged ticket branches. You are not done until this is done — say so explicitly in your final report.

---

Adapted from `implement-spec` in [mattpocock/skills](https://github.com/mattpocock/skills), by Matt Pocock, MIT. See [LICENSE](./LICENSE).
