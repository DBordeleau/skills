---
name: implement
description: "Implement a spec or ticket that doesn't need breaking down, delegating the build so the orchestrating context stays small."
disable-model-invocation: true
---

# Implement

Implement one piece of work: a spec small enough to build in one pass, or a single ticket.

Read `docs/agents/conventions.md` and `docs/agents/issue-tracker.md` before the first write. If they don't exist, tell the user to run `/setup-workflow`.

Call the Skill tool with `unslop` before the first thing you write that a human will read: a commit subject, a PR body, a ticket comment, or a message to the user. Tell each implementer sub-agent to do the same for its commit subjects and its report.

For a spec whose scope spans several tickets, use `/implement-spec` instead.

## Your job is to orchestrate, not to type

You are the **orchestrator**. The red → green loop, the typechecks, the test runs and the file reads all happen in **sub-agents with fresh context windows**. You hold the plan and the pointers; you do not hold the code.

This is the whole point of the skill. An orchestrator that reads the files, writes the code, runs `tsc` nine times and then reviews its own diff ends up carrying every one of those results in one context for hundreds of turns, and that context is re-sent on every turn. The same work split across sub-agents costs a fraction of it.

So:

- **Never** run the test suite, the typechecker or the linter yourself. A sub-agent runs them and reports the result in a line or two.
- **Never** read a source file to decide how to build something. A sub-agent reads it and reports what you need.
- Read only what you must to dispatch: the ticket, the spec, and the exploration notes' headings.
- Talk to sub-agents in **context pointers**: the ticket number, the spec, the notes path, a commit SHA. Don't paste content a pointer can fetch.

## Steps

### 1. Fetch the work

Fetch the ticket or spec from the issue tracker and state its title before starting. If the reference is ambiguous, ask.

A **fix spec** is a spec labelled Bug, or marked `**Type:** bug` on a local tracker, whose body opens with an effort line and has `## Root cause` and `## Regression test`. A ticket whose parent is a fix spec counts as one, and takes those sections from its parent. `/diagnose` writes fix specs. Steps 2, 4, 6, 7 and 9 each say what changes for one, and `## When a fix spec's root cause proves wrong` says when to stop.

### 2. Branch

If you're on the default branch, create a branch named as `docs/agents/conventions.md` requires, from the default branch unless the user says otherwise. A fix spec's branch takes the `fix` type.

### 3. Explore, in a sub-agent

Dispatch one **exploration sub-agent** for whatever the work needs: the relevant modules, the prior art for the tests, the external docs. It writes markdown notes to a path **outside the repo** (so they don't show up in the diff) and reports only that path plus its headings.

The notes are the shared context for every later sub-agent. Skip this step only for work so small that one sub-agent will see all of it anyway.

### 4. Agree the seams

From the notes, propose the **seams** the work will be tested at, one line each on what each catches and what it misses. Prefer existing seams; use the highest seam available; the fewer the better. Confirm them with the user before any test is written.

A fix spec settles its seam already: `## Regression test` names it, and the user confirmed it with the root cause in `/diagnose`. Use that seam and go on to step 5 without asking.

### 5. Plan the slices

Break the work into **vertical slices**, each a tracer bullet: a narrow but complete path that leaves the branch green and demoable. Keep them in dependency order. Most tickets are one or two slices; if you find more than about four, the work wanted `/to-tickets` and `/implement-spec` instead — say so and ask.

### 6. Build each slice in a fresh implementer sub-agent

One slice, one sub-agent, serially (they share the working tree, so they cannot run in parallel). When the spec or ticket opens with an effort line, give each implementer that effort, as `docs/agents/conventions.md` maps it. Each implementer:

- gets pointers only: the ticket, the notes path, the agreed seams, the slice, the branch;
- calls the Skill tool with `tdd` and builds the slice test-first at those seams;
- runs the typechecker and the slice's own tests itself, until green;
- commits to the branch, following the repo's commit conventions;
- puts any defect it finds outside its slice in its report, with its evidence, as `report-bug` describes for a sub-agent, and leaves it unfixed and unfiled;
- reports back **under 200 words**: what it built, what it committed, which seams it tested, anything it hit that changes the plan.

On a fix spec, the brief also tells the implementer to follow `## Fixing a bug` in `tdd`, and to put the output those rules say to keep in its report.

If an implementer reports something that changes the plan, re-plan the remaining slices before dispatching the next one.

### 7. Open the PR

If the issue tracker closes work through PRs, or the user asks for one, push the branch and open a **draft** PR after the first slice lands (a branch with no commits ahead of the default branch can't open one), marked as closing the tickets in scope. It closes the spec too only when the work leaves none of its tickets open; otherwise it references the spec. When the work is a whole fix spec, with no tickets, the PR closes the fix spec's issue.

Call the Skill tool with `pr` for the body. Follow the repo's attribution rule: the PR is written as the user, with no co-author or tool credit.

### 8. Verify once, in a sub-agent

When every slice is green, dispatch one sub-agent to run the **full** suite, the typechecker and the linter, and report pass/fail with only the failing output. If it fails, dispatch an implementer to fix it; don't debug it here.

### 9. Review and fix

Call the Skill tool with `code-review` against the point you branched from. On a fix spec, pass it the implementer's report that holds the regression test's output. Fix everything it raises in a **single fresh implementer sub-agent**, given the review findings and the notes path. Defects it lists as older than the diff are bug reports to file in step 10, not findings to fix.

### 10. Finish

- File the bugs your sub-agents and the review found outside the work: call the Skill tool with `report-bug` once, for all of them together.
- Push the branch.
- If a draft PR exists, mark it ready for review. Otherwise resolve each ticket in scope the way the issue tracker closes work, and report the branch.
- Clean up: remove any worktree you created, prune stale ones, and delete merged local branches, as `docs/agents/conventions.md` requires. You are not done until this is done.

## When a fix spec's root cause proves wrong

An implementer on a fix spec stops and reports when the regression test passes before the fix, or when three attempts have failed, as `## Fixing a bug` in `tdd` says. Either report means the root cause is probably wrong, and the issue goes back to `/diagnose` rather than to another implementer:

1. Dispatch no more slices.
2. Comment on the issue with the implementer's evidence: what each attempt changed and the output it left, or the regression test's passing output from before the fix.
3. Make the label move `docs/agents/labels.md` gives for `/implement` sending back a fix spec.
4. File the bugs and clean up, as the first and last items of step 10 say. Leave any draft PR in draft.
5. Tell the user that `/diagnose` takes the issue from here.

Step 8's rule for a failing suite stays as it is: a full suite that fails after the fix went green is a job for an implementer, not a reason to send the fix spec back.
