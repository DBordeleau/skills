# Diagnosis and fix share a session by default

A feature crosses a session boundary between its plan and its build, and the first design for bugs copied that, with the fix spec as the handoff. We made the fresh session one exit the user can pick once the root cause is confirmed. "Fix now" is the default when the fix is one slice with no open design decision.

Three reasons. The session that diagnosed the bug holds a working reproduction, and a fresh session has to rebuild it from prose. The fix turning the reproduction green is the strongest test of the root cause. Most fixes are smaller than the spec that would describe them. Matt Pocock built this handoff as `triage-issue` and replaced it with a loop that diagnoses and fixes in one session.

Both exits write the same fix spec and apply the same labels, so the trail on the issue does not depend on which one ran.

## Considered options

- **Always hand off to a fresh `/implement` session.** Kept as the "spec and stop" exit, for a fix that needs design decisions, needs tickets, or should wait.
- **A separate `/fix` skill.** Rejected. The rules for fixing a bug live in `tdd`, which `/implement` and `/diagnose` both call.
