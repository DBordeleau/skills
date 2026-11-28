# The fix spec

A fix spec is the bug report rewritten once the user has confirmed its root cause. It replaces the bug report's body, and a fresh session builds the fix from the issue alone, so everything that session needs is in it: the reproduction, the root cause, the places in scope and where the regression test goes.

- The template's headings are a fixed format and stay as they are. Apply `unslop` to the prose inside them.
- Name file paths and functions. Leave out line numbers, which go stale with the next commit.
- The first line is the effort line. Set it by the fix spec line under `## Effort levels` in `docs/agents/conventions.md`, from the notes' effort facts.
- On a local tracker, the file keeps its title, `**Type:** bug` and status lines at the top, with the status set to `ready-for-implementation`, and the effort line opens the body below them. `## Comments` stays at the bottom.
- When the user approved the exception because no test can fail on the bug, `## Regression test` says why, and `## Done when` replaces its first two boxes with one: the original reproduction fails before the fix and passes after, with both outputs captured.

<fix-spec-template>

Effort: <medium|high>

## Problem

What happens, what should happen, and the reproduction, carried over from the bug report and corrected by what the investigation found. Give the original reproduction as a command or steps, the commit it ran at, and its failing output.

## Root cause

The defect, in one or two sentences. Then the chain from it to the symptom, one link per line, each naming its file path and function.

## Fix

The behaviour change, as a user would see it. Every place in scope, each as a file path and function. What is out of scope, naming each place the user left out. Each of those gets its own bug report when the session finishes, and the session then adds its link here.

## Regression test

The testing seam of the behaviour the bug broke, named by module and interface. The existing tests the new case joins, by file and test name, and the seam builder they use, or that the fix creates one. The minimised reproduction the test is built from, and the symptom it asserts on. A test below that testing seam is a seam exception, and this section states its reason.

## Done when

- [ ] The regression test fails on the original symptom before the fix, and its failing output is captured
- [ ] The regression test passes after the fix
- [ ] The diff commits no test of the bug other than the regression test
- [ ] The original reproduction passes after the fix
- [ ] The full suite passes

## Ruled out

Each hypothesis ruled out, with the evidence against it. When none was formed, say that the cause was evident from the reproduction.

</fix-spec-template>
