# A regression test sits beside the tests of the behaviour the bug broke

Issue 6 had diagnose place a regression test at the nearest seam where a test built from the minimised reproduction fails on the bug. Nearest means lowest, so each fix added a test at a low seam, which ADR 0010 now rules out.

We decided the two steps of diagnose get different rules. The reproduction can still be anything that fails on the symptom, including a test written right at the broken function, and it is not committed as it stands. The regression test that is committed goes beside the existing tests of the behaviour the bug broke, at that behaviour's testing seam. A bug is a case those tests missed. When the behaviour has no tests yet, ADR 0010 picks the seam. diagnose keeps its guard that the seam must replay the bug as it happens at the call site, and a test below the listed seams needs a seam exception named in the fix spec.

The nearest-seam rule matched a common instinct in test-driven development, and it gives a failing test that names the fault. We kept that for the step where it helps, driving the fix. The fix spec records the root cause, so the committed test doesn't need to.
