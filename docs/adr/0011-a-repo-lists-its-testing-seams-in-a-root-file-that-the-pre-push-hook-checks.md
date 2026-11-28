# A repo lists its testing seams in a root file that the pre-push hook checks

An agent can only choose an agreed testing seam if something records which ones a repo has. We considered three forms for that record.

- **A prose document.** Matt Pocock floated this in May 2026 as a list of agreed test seams beside the glossary and the ADRs. Nothing stops it going stale, and nothing stops an implementer ignoring it.
- **A lint rule.** Pocock's own app keeps the list as a `TESTED_INTERFACES` array in a dependency-cruiser rule that fails any test file outside those modules. The list and its enforcement can't disagree. The rule only reads JavaScript and TypeScript, and a list of module names tells a planning agent nothing about what each seam covers.
- **A file that a check reads.** This is the one we chose.

We decided each repo keeps a testing seams file, `TESTING-SEAMS.md`, at its root beside `GLOSSARY.md`. Each entry gives the module, the interface a test calls, the path its test files live under, what a test there covers, what it misses, and the seam builder. One line gives the repo's test file pattern. The pre-push hook that setup-workflow already installs rejects a push that adds a test file outside the listed paths. The check needs git and the file, so it works in a repo of any language.

The file sits at the root because it holds the repo's own content, like the glossary. The files under `docs/agents/` are workflow settings that setup-workflow writes from fragments.

A path check sees where a test file sits and not what it imports. A test under a listed path can still reach past the interface, and code-review is what catches that. The coverage notes in each entry are prose and can go stale. The paths can't, because the hook reads them. The file lists no individual behaviours or tests, since that list would be out of date within a week.
