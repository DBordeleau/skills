# Agents file bug reports unprompted, behind an evidence bar

An agent that finds a defect outside its task files a bug report without asking. The alternatives were to fix it in the current diff, which is scope creep, or to mention it in a report that ends with the session. None of the workflows we surveyed does this. Matt Pocock removed his `qa` skill, which filed issues without review, and curl closed its bug bounty in January 2026 over AI-written reports.

So filing carries a bar. Every bug report states whether a reproduction was run. A report without one must name the input, the expected result, the actual result and the location. A duplicate search runs before filing. Sub-agents never file. They pass the bug to the session the user is talking to, which files once at the end of its run and lists what it filed.

## Considered options

- **Agents report and the user files.** This is what Matt Pocock's skills leave you with. Rejected because the finding is lost when the session ends.
- **File only bugs that have a reproduction.** Rejected for now because it drops real bugs found by reading code. It is the fallback if too many filed bug reports get closed as not bugs.
