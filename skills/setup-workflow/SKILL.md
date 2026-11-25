---
name: setup-workflow
description: "Configure a repo for this workflow: issue tracker, labels, conventions, domain docs, environment notes and the branch-name hook, all committed. Run once per repo before the other skills."
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

**A. Issue tracker.** Where issues live for this repo; `/to-spec`, `/to-tickets`, `/implement` and `/code-review` all read and write there. If a remote points at GitHub, propose GitHub and move on. Otherwise offer **local markdown** (files under `.scratch/<feature>/`, good for a solo repo with no remote), or ask the user to describe another tracker in a paragraph, which you'll record as prose.

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

**Create the labels** the tracker is missing (`gh label create "<name>" --description "…" --color <hex>`), keeping any issue-type labels the repo already uses.

**Install the hook.** Copy [hooks/pre-push](./hooks/pre-push) to `.githooks/pre-push`, make it executable, and point git at it: `git config core.hooksPath .githooks`. `core.hooksPath` is local config, so it doesn't travel with a clone — add it to whatever the project already runs on install (an npm `prepare` script, a bootstrap task) so a fresh clone is covered, and say in `docs/agents/environment.md` how to set it by hand.

If a `pre-push` hook already exists, extend it rather than replacing it, and add a test for the branch-name check if the repo has somewhere tests live.

**Fix what step 1 found untracked.** If `AGENTS.md`, `CLAUDE.md`, `docs/agents/` or a repo-local skill under `.agents/skills/` is untracked, add it. If the same skill exists under both `.agents/skills/` and `.claude/skills/`, say so and offer to keep `.agents/skills/` as the one copy, with `.claude/skills/<name>` a symlink or directory junction to it (gitignored). If links aren't available on this machine, don't copy: add a line to the config file pointing Claude Code at `.agents/skills/` instead. Two copies drift; a pointer can't.

## 5. Commit, then verify

Commit everything in one commit, as the user, with no tool credit: `chore: set up agent workflow config`.

Then check your own work and report it in five lines:

- `git status` is clean of the files you wrote.
- The labels exist (`gh label list`).
- The hook fires: on a throwaway branch with a bad name, a dry-run push is rejected (`git push --dry-run origin HEAD:refs/heads/bad-name`), and a well-named one isn't.
- Each `docs/agents/*.md` file has its placeholders filled, with no `<!-- fill in -->` left.

Finish by naming the daily loop the repo is now set up for: `/plan-with-me` → `/to-spec` → `/to-tickets` if advised → `/implement` or `/implement-spec` → `code-review` → `pr`. Mention that `docs/agents/*.md` can be edited directly later, and that re-running this skill is only needed to switch trackers or start over.
