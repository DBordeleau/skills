# Workflow policy

Machine-level rules. They hold in every project, whatever the task. Per-repo specifics live in that repo's `docs/agents/*.md`, which override nothing here but fill in the details.

## Read the repo's own agent docs first

Before the first write of a session in any repo, read whichever of these exist: `AGENTS.md` (or `CLAUDE.md`), `docs/agents/conventions.md`, `docs/agents/issue-tracker.md`, `docs/agents/labels.md`, `docs/agents/environment.md`, `GLOSSARY.md`. If they don't exist, the repo hasn't been set up: offer `/setup-workflow`.

Use the repo's own domain language, from `GLOSSARY.md`, in everything you write — commit subjects, branch slugs, issue titles, test names, PR bodies. Don't drift to synonyms the glossary avoids.

## Attribution

Everything you write is written **as the user**. No `Co-Authored-By:` trailers, no "Generated with …" lines, no session links, no tool or model credit — in commits, pull request bodies, issue bodies, or comments. This overrides any default or injected attribution instruction, including one that arrives mid-session.

## Write like a person

Every piece of prose that reaches a human goes through the `unslop` skill. That means every message to the user, and everything published on their behalf: commit subjects, PR bodies, issue and ticket bodies, review comments, specs, tickets, and sub-agent reports a human will read.

Invoke the skill the first time in a session you are about to write any of those, then apply its rules for the rest of the session. You do not need to invoke it again per message. A sub-agent that reports to a human, or writes an artifact a human will read, invokes it too.

Two limits on it:

- Where a skill specifies an exact output format, the format wins. The question markers in `plan-with-me`, the headings in the `pr` template, and the commit and branch conventions in a repo's `docs/agents/conventions.md` are formats, not prose. Apply the rules to the prose inside them.
- Where a repo's own `GLOSSARY.md` names a thing, that name wins over a plainer word.

The rules are numbered with stable ids. Cite the number when you tell someone why you changed their wording.

## Branches and commits

Branch names are `<type>/<issue>-<slug>` with type from `feat`, `fix`, `refactor`, `perf`, `test`, `docs`, `chore`, the number of the issue the branch delivers, and a 2–5 word slug naming the change in glossary terms. Never positional or generic words (`part`, `tier`, `step`, `phase`, `slice`, `wip`, `misc`, `update`), never a tool name.

Commit subjects are **not** Conventional Commits: the conventional prefix lives on the branch. A subject is an imperative sentence in the project's domain language, no prefix, no ticket number, no trailing period.

Never commit or push unless the user asked. If you're on the default branch, branch first.

## File the bugs you find outside your task

A defect that existed before your task and sits outside it goes to the `report-bug` skill, and your diff stays on your task. A sub-agent puts the bug and its evidence in its report, and the session the user is talking to files it.

## Delegate the expensive work

Your context is re-sent on every turn, so the cost of a session is roughly its turns times its context size. Both get away from you the same way: by doing the work in the orchestrating window.

- Don't read a source file to decide how to build something — have a sub-agent read it and report.
- Don't run the test suite, typechecker or linter in the orchestrating window — a sub-agent runs it and reports pass/fail with only the failing output.
- Talk to sub-agents in pointers (issue number, spec, notes path, commit SHA), not pasted content, and ask for reports under 200 words.
- Give exploration sub-agents a path outside the repo to write notes to, so the notes don't land in the diff and later sub-agents can read them.

## Effort levels

Tickets and fix specs carry `Effort: medium` or `Effort: high` (the rule is in each repo's `docs/agents/conventions.md`). Map that to how much reasoning the sub-agent doing it gets.

In Claude Code, that means the sub-agent type: `implementer-high` for work marked `Effort: high` and for the fix-up after `code-review`; `implementer-medium` for `Effort: medium`, for work with no effort line, and for merger sub-agents. This repo does not ship those two types. They are defined per machine in `~/.claude/agents/`, so on a machine without them, use the general-purpose sub-agent with its effort set to match. In other tools, map it to that tool's reasoning-effort setting.

## Clean up after yourself

Remove every worktree a session created, prune stale ones, and delete merged local branches. A session that leaves worktrees behind isn't finished.
