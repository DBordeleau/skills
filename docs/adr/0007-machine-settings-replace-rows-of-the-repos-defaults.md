# Machine settings replace rows of the repo's defaults

Everything else this repo installs is edited in the repo and never in its installed copy. The implementer settings are the exception. The repo ships default rows, and the machine settings hold only the rows a user wants different on that machine. Job 1 reads both and never writes the machine settings.

Which model a machine runs follows its subscription, and changing one in the repo costs a branch and a pull request. Keeping only the differing rows on the machine means a new default, such as a newer model, still reaches every row the user has not replaced.

The machine settings also list the tools an implementer type may not use, beyond the one default that an implementer starts no sub-agents. A desktop app's tools or one account's connector describe an installation, so they don't belong in a default.

## Considered options

- **Defaults in the repo only.** Rejected. A second machine on a different subscription could not differ without a fork.
- **A machine file seeded once from the defaults.** Rejected. The copy goes stale the day the defaults change.
- **A setting per repo.** Rejected. No repo has needed its own model, and it adds a second place to look.
