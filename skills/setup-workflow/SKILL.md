---
name: setup-workflow
description: "Configure a repo for this workflow: issue tracker, labels, conventions, domain docs, environment notes and the branch-name hook, all committed. Run it before the other skills, and again whenever a fragment changes upstream."
disable-model-invocation: true
---

# Setup Workflow

Scaffold the per-repo configuration the other skills read. Works the same on an empty repo and on one with years of history: it edits in place, never duplicates a block, and never overwrites prose a human wrote.

Everything it writes is **tracked and committed**. Agent config that lives only on one machine is the thing this repo exists to prevent.

## What gets written

```
AGENTS.md                      ← the "Agent skills" block (the source of truth)
CLAUDE.md                      ← a pointer to AGENTS.md, if Claude Code is used here
docs/agents/issue-tracker.md   ← where issues live and how to operate on them
docs/agents/labels.md          ← the label vocabulary
docs/agents/conventions.md     ← attribution, branch names, commits, cleanup, effort
docs/agents/domain.md          ← where GLOSSARY.md and ADRs live, and how to read them
docs/agents/environment.md     ← shell, dev server, checks: the per-machine gotchas
.githooks/pre-push             ← branch-name enforcement
.gitattributes                 ← the LF line that keeps the hook's shebang working
```

This is a prompt-driven skill, not a script. Explore, present what you found, confirm, then write.

## 1. Explore

Read the repo's actual starting state. Don't assume:

- `git remote -v`: is this a GitHub repo? Which one? Is there a remote at all?
- `AGENTS.md`, `CLAUDE.md`: does either exist? Is there already an `## Agent skills` section? **Are they tracked?** (`git ls-files --error-unmatch <file>`). An untracked `CLAUDE.md` is a finding, not a detail.
- `docs/agents/`: does this skill's prior output already exist?
- `GLOSSARY.md`, `GLOSSARY-MAP.md`, `docs/adr/`, and any `src/*/docs/adr/`.
- `.agents/skills/` and `.claude/skills/`: repo-local skills. Two copies of the same skill is drift waiting to happen — note it.
- `.githooks/`, `core.hooksPath`, and any existing hook you'd be extending rather than replacing.
- The package manifest: the typecheck, test and lint commands, and the dev server command and port.
- Monorepo signals: `pnpm-workspace.yaml`, a `workspaces` field, or a populated `packages/*` each with its own source tree. These show up only in a genuinely multi-package repo; their absence means single-context, which is almost every repo.

## 2. Present findings, then ask at most three questions

Summarise what's there and what's missing, including anything you found untracked or duplicated.

Then ask only what exploration couldn't settle. Lead with the recommended answer so the user can accept it in a word, and skip any question exploration already answered.

**A. Issue tracker.** Where issues live for this repo; `/to-spec`, `/to-tickets`, `/implement`, `/code-review`, `/report-bug` and `/diagnose` all read and write there. If a remote points at GitHub, propose GitHub and move on. Otherwise offer **local markdown** (files under `.scratch/`, good for a solo repo with no remote), or ask the user to describe another tracker in a paragraph, which you'll record as prose.

A GitHub tracker needs a reachable repo: if the user picks it and no remote points there, get the URL from them and wire it up (`git init`, `git remote add origin <url>`) first. Done when `gh repo view` prints the project from inside this directory.

**B. Labels.** Propose the vocabulary in [fragments/labels.md](./fragments/labels.md) as-is (recommended: yes). Only if the user says no — usually because their tracker already uses other strings — collect the overrides, so skills apply existing labels instead of creating near-duplicates.

**C. Domain docs.** Default to **single-context**: one `GLOSSARY.md` and `docs/adr/` at the root. Write it without asking. Offer **multi-context** (a root `GLOSSARY-MAP.md` pointing at per-context glossaries) only if step 1 found monorepo signals.

## 3. Confirm

Show a draft of the `## Agent skills` block and of each `docs/agents/*.md` file, and say which hook you'll install and which labels you'll create. Let the user edit before anything is written.

## 4. Write

**Pick the config file.** `AGENTS.md` is the source of truth, because every tool reads it. Create it if neither file exists. If only `CLAUDE.md` exists, move its contents into `AGENTS.md` (`git mv`, then edit) and leave `CLAUDE.md` holding one line: `See AGENTS.md.` — Claude Code follows the pointer, and there's one copy to keep current. If both exist with real content, ask which to keep; don't pick for them.

If an `## Agent skills` block already exists, update it in place. Don't touch the prose around it.

The block:

```markdown
## Agent skills

Read these before the first write of a session.

- **Issue tracker**: [one line]. See `docs/agents/issue-tracker.md`.
- **Labels**: [one line]. See `docs/agents/labels.md`.
- **Conventions**: attribution, branch names, commits, cleanup, effort. See `docs/agents/conventions.md`.
- **Domain docs**: [single-context or multi-context]. See `docs/agents/domain.md`.
- **Environment**: shell, dev server, checks. See `docs/agents/environment.md`.
```

**Write the docs** from the seed fragments in this skill folder, filling in what you learned in step 1 (the real check commands, the real dev-server port, the real glossary paths):

- [fragments/issue-tracker-github.md](./fragments/issue-tracker-github.md)
- [fragments/issue-tracker-local.md](./fragments/issue-tracker-local.md)
- [fragments/labels.md](./fragments/labels.md)
- [fragments/conventions.md](./fragments/conventions.md)
- [fragments/domain.md](./fragments/domain.md)
- [fragments/environment.md](./fragments/environment.md)

For another tracker, write `docs/agents/issue-tracker.md` from scratch from the user's description.

Each fragment opens with a provenance comment holding the literal `COMMIT`. Replace it with this skills repo's current short commit (`git -C <skills clone> rev-parse --short HEAD`). That line is what makes a later update able to merge instead of overwrite, so a file written without it can only be updated by hand. A file you wrote from scratch gets no provenance line, because there is no upstream to merge from; say so in your report.

**Create the labels** in the table that the tracker is missing, `bug` included (`gh label create "<name>" --description "…" --color <hex>`, with the table's meaning as the description). Keep any other issue-type labels the repo already uses. A local tracker has no labels, so skip this step there.

Before creating one, check whether a label already fills that role under a different name, and count the issues holding it (`gh issue list --state all --limit 200 --json labels`). Rename those instead of creating a duplicate, as [fragments/labels.md](./fragments/labels.md) says, and report the count you moved. A repo that has been through the older `setup-matt-pocock-skills` will have `needs-triage` for what this vocabulary calls `needs-planning`, and may have `ready-for-agent` for `ready-for-implementation`.

**Install the hook.** Add the line `.githooks/* text eol=lf` to the repo's `.gitattributes` if it isn't there, creating the file if needed, so a CRLF checkout can't break the shebang. Copy [hooks/pre-push](./hooks/pre-push) to `.githooks/pre-push` and stage it with `git add --chmod=+x .githooks/pre-push`. A plain `chmod +x` is not enough: on Windows git records the file as `100644`, and a clone on Linux or macOS then skips the hook because it isn't executable. Point git at the hook directory: `git config core.hooksPath .githooks`. `core.hooksPath` is local config, so it doesn't travel with a clone — add it to whatever the project already runs on install (an npm `prepare` script, a bootstrap task) so a fresh clone is covered, and say in `docs/agents/environment.md` how to set it by hand.

If a `pre-push` hook already exists, extend it rather than replacing it, and add a test for the branch-name check if the repo has somewhere tests live.

**Fix what step 1 found untracked.** If `AGENTS.md`, `CLAUDE.md`, `docs/agents/` or a repo-local skill under `.agents/skills/` is untracked, add it. If the same skill exists under both `.agents/skills/` and `.claude/skills/`, say so and offer to keep `.agents/skills/` as the one copy, with `.claude/skills/<name>` a symlink or directory junction to it (gitignored). If links aren't available on this machine, don't copy: add a line to the config file pointing Claude Code at `.agents/skills/` instead. Two copies drift; a pointer can't.

## 5. Commit, then verify

Commit everything in one commit, as the user, with no tool credit. Follow the commit convention you just wrote: an imperative sentence in the project's own language, with no `chore:` prefix.

If you are on the default branch, branch first, as `docs/agents/conventions.md` requires. Where the branch name needs an issue number and there is no issue for this, commit to a local branch with a placeholder name and stop there. Opening an issue and a pull request publishes to the tracker, so ask the user before doing either, and tell them the branch is waiting.

Then check your own work and report each of these in one line:

- `git status` is clean of the files you wrote.
- The labels exist (`gh label list`).
- The hook is committed executable with LF endings: `git ls-files -s .githooks/pre-push` starts with `100755`, and `git ls-files --eol .githooks/pre-push` shows `i/lf`.
- The hook fires: on a throwaway branch with a bad name, a dry-run push is rejected (`git push --dry-run origin HEAD:refs/heads/bad-name`), and a well-named one isn't.
- Each `docs/agents/*.md` file has its placeholders filled, with no `<!-- fill in -->` and no `@COMMIT` left.

## Re-running on a repo that is already set up

A repo's `docs/agents/*.md` are copies, committed so that a clone has them without this skills repo. That means a fragment changing upstream reaches no repo on its own, and the repo's copy has almost certainly been edited since: real check commands, a shell quirk, a hook path. **Never overwrite them with the current fragment.** Merge only what changed upstream, so local edits survive.

For each `docs/agents/<name>.md` that carries a provenance line:

1. Read the commit out of its provenance line. Call it `BASE`, and call the skills clone `$SKILLS`.
2. Write the fragment as it was then, and as it is now, to two temporary files outside the repo:
   - `git -C $SKILLS show BASE:skills/setup-workflow/fragments/<fragment>.md > base.md`
   - `git -C $SKILLS show HEAD:skills/setup-workflow/fragments/<fragment>.md > other.md`

   Run these in a POSIX shell, or in PowerShell wrap each one in `cmd /c "<command>"`. Windows PowerShell 5.1's own `>` writes UTF-16, and `git merge-file` then refuses the files as binary.
3. If those two are identical, nothing changed upstream. Leave the file alone and move on.
4. Otherwise merge the upstream change into the repo's copy, keeping its local edits:
   - `git merge-file docs/agents/<name>.md base.md other.md`
   - It edits the repo's copy in place. Exit 0 means a clean merge. An exit from 1 to 127 is the number of conflicts, left in the file as `<<<<<<<` markers. A higher exit, such as 255, is an error and the file is unchanged: read the message, fix the cause, and run the merge again.
5. Resolve any conflict by hand, in favour of the local side when the two say the same thing differently, and in favour of upstream when upstream states a new rule. Show the user each conflict you resolved and how.
6. Update the provenance line to the new commit.

Then report, per file: unchanged, merged cleanly, or merged with conflicts you resolved. A file with no provenance line was written from scratch, so say that it needs a human read instead of a merge.

Re-run the label and hook steps too, since both are idempotent: a renamed label role or a changed hook will not have reached the repo either. Ask before renaming a label a second time.

Finish by naming the daily loop the repo is now set up for, as `WORKFLOW.md` in the skills repo draws it. The feature path is `/plan-with-me` → `/to-spec` → `/to-tickets` if advised → `/implement` or `/implement-spec` → `code-review` → `pr`. The bug path is `report-bug` → `/diagnose`, which stops twice for the user and then either fixes the bug now through `code-review` and `pr`, or leaves a fix spec for `/implement` or `/to-tickets`. Mention that `docs/agents/*.md` can be edited directly later, and that re-running this skill brings in later fragment changes without losing those edits.

---

Adapted from `setup-matt-pocock-skills` in [mattpocock/skills](https://github.com/mattpocock/skills), by Matt Pocock, MIT. See [LICENSE](./LICENSE).
