---
name: tdd
description: Test-driven development. Use when the user wants to build features or fix bugs test-first, mentions "red-green-refactor", or wants integration tests.
---

# Test-Driven Development

TDD is the red → green loop. This skill is the reference that makes that loop produce tests worth keeping: what a good test is, where tests go, where their setup comes from, the anti-patterns, the rules of the loop, and the rules for fixing a bug. Every section but `## Fixing a bug` applies on every cycle, and that one applies on every cycle of a bug fix: consult them before and during the loop, not after.

When exploring the codebase, read `GLOSSARY.md` (if it exists) so test names and interface vocabulary match the project's domain language, and respect ADRs in the area you're touching.

## What a good test is

Tests verify behavior through public interfaces, not implementation details. Code can change entirely; tests shouldn't. A good test reads like a specification: "user can checkout with valid cart" tells you exactly what capability exists, and it survives refactors because it doesn't care about internal structure.

See [tests.md](tests.md) for examples and [mocking.md](mocking.md) for mocking guidelines.

## Testing seams: where tests go

A **seam** is the location where a module's interface lives, as codebase-design defines it. A **testing seam** is the seam a test goes through. A test observes behaviour through its testing seam and never reaches into the internals behind it.

**The seam rule.** Each behaviour gets its tests at one testing seam: the highest one where a test can observe the behaviour and still run fast and deterministically. A lower seam gets tests only as a seam exception, for cases the higher seam can't reach at a reasonable cost, such as a large set of input combinations or an error path that is hard to trigger from above.

**Test only at agreed testing seams.** The agreement arrives in writing: a ticket's testing line, a spec's seams list, or a fix spec's `## Regression test`. When one of them names the testing seam, write every test there and don't ask the user again. When none of them names one, write down the testing seams under test and confirm them with the user before any test is written. Ask: "What's the public interface, and which testing seams should we test at?" Give each proposed testing seam a one-line note on what it catches and what it misses.

When the shape of that interface is itself in question (how deep the module is, where the seam belongs, what the interface should expose), call the Skill tool with "codebase-design" for the vocabulary. It is the shared source of the module, interface, depth, seam, adapter, leverage and locality terms, and it is a reference to consult, not a session to run.

## Existing tests and the seam builder

- **Search before you write.** Before writing a test, search the tests at the agreed testing seam for the behaviour: the interface it goes through, and the domain terms a test of it would be named with. When a test of the behaviour exists, change that test to cover the new case. Add a new test only when the search finds none. Two tests of one behaviour make the next change to it edit both.
- **A test states inline only the values it asserts on.** Every other value it needs, such as seed rows, config and required inputs, comes from the seam builder: the shared setup the tests at that testing seam use. The ticket or spec names it beside the testing seam. A reader still sees in the test the data each assertion depends on, and a new required input changes the seam builder and no test file.
- **Extend the seam builder when it lacks something.** Add the missing input to the seam builder, with a default the other tests can share, and use it from there. Setup copied into a test file is a private copy that the next new input has to reach file by file. When the testing seam has no seam builder yet, create one beside its tests and have the first test use it.

## Anti-patterns

- **Implementation-coupled**: mocks internal collaborators, tests private methods, or verifies through a side channel (querying the database instead of using the interface). The tell: the test breaks when you refactor but behavior hasn't changed.
- **Tautological**: the assertion recomputes the expected value the way the code does (`expect(add(a, b)).toBe(a + b)`, a snapshot derived by hand the same way, a constant asserted equal to itself), so it passes by construction and can never disagree with the code. Expected values must come from an independent source of truth: a known-good literal, a worked example, the spec.
- **Horizontal slicing**: writing all tests first, then all implementation. Bulk tests verify _imagined_ behavior: you test the _shape_ of things rather than user-facing behavior, the tests go insensitive to real changes, and you commit to test structure before understanding the implementation. Work in **vertical slices** instead: one test → one implementation → repeat, each test a **tracer bullet** that responds to what the last cycle taught you.

## Rules of the loop

- **Red before green.** Write the failing test first, then only enough code to pass it. Don't anticipate future tests or add speculative features.
- **One slice at a time.** One testing seam, one test, one minimal implementation per cycle.
- **Refactoring is not part of the loop.** It belongs to the review stage (see the `code-review` skill), not the red → green implementation cycle.

## Fixing a bug

These rules hold for every bug fix, on top of the loop above. The sections they name are the fix spec's. Without a fix spec, use the bug report and the root cause you were given.

- **The fix spec settles the seam.** `## Regression test` names it, and the user confirmed it when they confirmed the root cause. Write the test there without asking again.
- **Red on the original symptom, before the fix.** Build the regression test from the reproduction and run it before you change the code. It must fail on the symptom `## Problem` describes, with the same error text or the same wrong value, and not on some other failure nearby. Keep its failing output as it printed: that output is the proof the test can catch the bug. It goes in your report, and from there into the PR body's `## Evidence`. A regression test that passes before the fix cannot catch the bug, and it is evidence against the root cause: stop and report it.
- **The smallest change at the root cause.** Change the code `## Root cause` names, in the places `## Fix` lists. A change at the symptom leaves the root cause to fail somewhere else, and anything beyond the fix belongs in another diff.
- **Green, then the original reproduction.** Run the regression test, see it pass, and keep that output too. Then rerun the original, unminimised reproduction from `## Problem` and see it pass, since a new test passing does not show the symptom is gone. Then run the full suite.
- **Three failed attempts stop the work.** An attempt fails when the regression test or the original reproduction is still red after it. Revert each failed attempt before the next, so every attempt starts from the same code. After the third, stop, and report what each attempt changed and the output it left. The root cause is probably wrong, and the session that dispatched you decides what happens next.
- **When no test can fail on the bug,** the fix spec says why and that the user approved it. The original reproduction is then the proof: keep its failing output from before the fix and its passing output from after.

---

Adapted from `tdd` in [mattpocock/skills](https://github.com/mattpocock/skills), by Matt Pocock, MIT. See [LICENSE](./LICENSE).
