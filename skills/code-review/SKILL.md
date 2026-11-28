---
name: code-review
description: "Review the changes since a fixed point (commit, branch, tag, or merge-base) along two axes: Standards (does the code follow this repo's documented coding standards?) and Spec (does the code match what the originating issue/spec asked for?). Runs both reviews in parallel sub-agents and reports them side by side. Use when the user wants to review a branch, a PR, work-in-progress changes, or asks to \"review since X\"."
---

Two-axis review of the diff between `HEAD` and a fixed point the user supplies:

- **Standards**: does the code conform to this repo's documented coding standards, and do its tests pass the test checks?
- **Spec**: does the code faithfully implement the originating issue / spec, with each test at the testing seam the spec names?

Both axes run as **parallel sub-agents** so they don't pollute each other's context, then this skill aggregates their findings. Start every sub-agent with a fresh context window.

Read `docs/agents/issue-tracker.md` to find the originating spec. If it doesn't exist, tell the user to run `/setup-workflow`.

## Process

### 1. Pin the fixed point

Whatever the user said is the fixed point (a commit SHA, branch name, tag, `main`, `HEAD~5`, etc.). If they didn't specify one, ask for it.

Capture the diff command once: `git diff <fixed-point>...HEAD` (three-dot, so the comparison is against the merge-base). Also note the list of commits via `git log <fixed-point>..HEAD --oneline`.

Before going further, confirm the fixed point resolves (`git rev-parse <fixed-point>`) and the diff is non-empty. A bad ref or empty diff should fail here, not inside two parallel sub-agents.

### 2. Identify the spec source

Look for the originating spec, in this order:

1. Issue references in the commit messages (`#123`, `Closes #45`, GitLab `!67`, etc.), fetched via the workflow in the tracker doc.
2. A path the user passed as an argument.
3. A spec file under `docs/`, `specs/`, or `.scratch/` matching the branch name or feature.
4. If nothing is found, ask the user where the spec is. If they say there isn't one, the **Spec** sub-agent will skip and report "no spec available".

### 3. Identify the standards sources

Search the repo for every file that documents how code should be written. When `CODING_STANDARDS.md` or `CONTRIBUTING.md` exists, it must be on the list.

On top of whatever the repo documents, the Standards axis always carries the **smell baseline** below: a fixed set of Fowler code smells (_Refactoring_, ch.3) that applies even when a repo documents nothing. Two rules bind it:

- **The repo overrides.** A documented repo standard always wins; where it endorses something the baseline would flag, suppress the smell.
- **Always a judgement call.** Each smell is a labelled heuristic ("possible Feature Envy"), never a hard violation. Like any standard here, skip anything tooling already enforces.

Each smell reads *what it is* → *how to fix*; match it against the diff:

- **Mysterious Name**: a function, variable, or type whose name doesn't reveal what it does or holds. → rename it; if no honest name comes, the design's murky.
- **Duplicated Code**: the same logic shape appears in more than one hunk or file in the change. → extract the shared shape, call it from both.
- **Feature Envy**: a method that reaches into another object's data more than its own. → move the method onto the data it envies.
- **Data Clumps**: the same few fields or params keep travelling together (a type wanting to be born). → bundle them into one type, pass that.
- **Primitive Obsession**: a primitive or string standing in for a domain concept that deserves its own type. → give the concept its own small type.
- **Repeated Switches**: the same `switch`/`if`-cascade on the same type recurs across the change. → replace with polymorphism, or one map both sites share.
- **Shotgun Surgery**: one logical change forces scattered edits across many files in the diff. → gather what changes together into one module.
- **Divergent Change**: one file or module is edited for several unrelated reasons. → split so each module changes for one reason.
- **Speculative Generality**: abstraction, parameters, or hooks added for needs the spec doesn't have. → delete it; inline back until a real need shows.
- **Message Chains**: long `a.b().c().d()` navigation the caller shouldn't depend on. → hide the walk behind one method on the first object.
- **Middle Man**: a class or function that mostly just delegates onward. → cut it, call the real target direct.
- **Refused Bequest**: a subclass or implementer that ignores or overrides most of what it inherits. → drop the inheritance, use composition.

The Standards axis also carries the **test checks** below. They apply in every repo, whether or not it documents coding standards, and a repo standard doesn't suppress them. Each one is a finding, not a judgement call. Match each against every test the diff adds or edits:

- **Second test of a behaviour**: a test of a behaviour that already has tests, at a second testing seam with no seam exception named for it, or at the same testing seam beside the existing test, where tdd changes that test instead. → move the case into the existing test, or name the seam exception in the spec.
- **Test past the interface**: a test that imports from behind the interface of the module it tests, such as an internal file, a private helper, or a module the tested one calls. → rewrite it to go through the interface of its testing seam.
- **Copied setup**: setup in a test file that copies setup from another test file. → move it into the seam builder of that testing seam, and have both files use it.

### 4. Spawn both sub-agents in parallel

Issue both sub-agent calls together, in the foreground, and aggregate the reports they return.

**Standards sub-agent prompt** should include:

- The full diff command and commit list.
- The list of standards-source files you found in step 3, **plus the smell baseline and the test checks from step 3** pasted in full (the sub-agent has no other access to them).
- The path of the spec from step 2, if there is one, as the place seam exceptions are named: the ticket's testing line, the spec's seams list, or the fix spec's `## Regression test`.
- The brief: "Report, per file/hunk where relevant, (a) every place the diff violates a documented standard: cite the standard (file + the rule); (b) any baseline smell you spot: name it and quote the hunk; and (c) every test check the diff's tests fail: name the check, quote the test, and for a copied setup or a second test of a behaviour, name the other test file. Distinguish hard violations from judgement calls: documented-standard breaches can be hard, and a failed test check is always hard. Baseline smells are always judgement calls. A documented repo standard overrides the baseline, never the test checks. Skip anything tooling enforces. List separately, under "Older than the diff", any defect you notice that the diff did not introduce, with its evidence, as the `report-bug` skill describes for a sub-agent. Under 400 words."

**Spec sub-agent prompt** should include:

- The diff command and commit list.
- The path or fetched contents of the spec.
- The brief: "Report: (a) requirements the spec asked for that are missing or partial; (b) behaviour in the diff that wasn't asked for (scope creep); (c) requirements that look implemented but where the implementation looks wrong; and (d) every test the diff adds or edits that sits at a testing seam other than the one its ticket's testing line, the spec's seams list, or the fix spec's `## Regression test` names for the behaviour it tests. A seam exception listed there counts as named. Quote the spec line for each finding. List separately, under "Older than the diff", any defect you notice that the diff did not introduce, with its evidence, as the `report-bug` skill describes for a sub-agent. Under 400 words."

A **fix spec** is a spec labelled Bug, or marked `**Type:** bug` on a local tracker, whose body opens with an effort line and has `## Root cause` and `## Regression test`. A ticket whose parent is a fix spec counts as one, and takes those sections from its parent.

When the spec is a fix spec, the Spec sub-agent also gets the PR number when a PR exists, and the implementers' reports when your caller passed them. Add to its brief: "(e) the fix against the fix spec: the change sits at the code `## Root cause` names and in the places `## Fix` lists, and a change at the symptom instead is a finding; the regression test `## Regression test` describes is in the diff, asserting on the symptom `## Problem` describes; and its failing output from before the fix is in the PR body's `## Evidence` or in an implementer's report. A missing regression test or missing failing output is a finding. When the fix spec records that no test can fail on the bug, look for the original reproduction's failing output instead."

If the spec is missing, skip the Spec sub-agent and note this in the final report.

### 5. Aggregate

Call the Skill tool with `unslop` and apply its rules to the findings you present, including any you post as a review comment. Keep each finding's substance and its quoted hunk exactly as the sub-agent reported them.

Present the two reports under `## Standards` and `## Spec` headings, verbatim or lightly cleaned. Do **not** merge or rerank findings, because the two axes are deliberately separate (see _Why two axes_).

End with a one-line summary: total findings per axis, and the worst issue _within each axis_ (if any). Don't pick a single winner across axes: that's the reranking the separation exists to prevent.

After the summary, list the defects either sub-agent found that are older than the diff under `## Older than the diff`. They are bug reports for `report-bug`, not review findings, and the fix-up after review leaves them alone. When another skill called this one, that skill's session files them at the end of its run. When the user ran this review directly, call the Skill tool with `report-bug` for them before you finish.

## Why two axes

A change can pass one axis and fail the other:

- Code that follows every standard but implements the wrong thing → **Standards pass, Spec fail.**
- Code that does exactly what the issue asked but breaks the project's conventions → **Spec pass, Standards fail.**

Reporting them separately stops one axis from masking the other.

---

Adapted from `code-review` in [mattpocock/skills](https://github.com/mattpocock/skills), by Matt Pocock, MIT. See [LICENSE](./LICENSE).
