<!-- /setup-workflow wrote this from DBordeleau/skills@4687808, fragments/environment.md. Edit it freely: an update merges only what changed upstream. -->

# Environment

Things about this machine and repo that cost an agent a wasted turn each time it rediscovers them. Add to this file whenever a session loses a turn to the environment rather than to the problem.

## Shell

On Windows, the shell is PowerShell, with a POSIX `bash` tool also available. They take different syntax and the mistakes are expensive:

- Don't mix them in one command. Pick the shell, then write that shell's syntax throughout.
- In PowerShell, `&&` and `||` don't exist: use `A; if ($?) { B }`. No ternary, no `??`, no `?.`.
- For multi-line strings passed to a native command (commit messages, issue bodies), use a single-quoted here-string with the closing `'@` at column 0, or a `bash` heredoc. Unbalanced quotes in a long `-m` string is the single most common failed call.
- Prefer file-reading and search tools over `Get-Content` / `Select-String` / `cat` pipelines.

## Running the app

There is nothing to run. The skills are markdown files that each tool reads through a link in its skills directory.

Those links point at the main clone of this repo, not at a worktree. An edit made in a worktree reaches no tool until it is merged and the main clone pulls it. A running session also keeps the skill text it loaded at start, so it sees an edit only after a restart.

## Worktrees

Sessions run in worktrees under a directory the tool chooses. A file outside the session's own worktree may be refused: read and write through the session's own path, and don't reach into another worktree's copy of the repo.

## Checks

- Typecheck: none.
- Unit tests: none.
- Full suite: none.
- Lint: none.

The repo has no code to check. After a change to `skills/setup-workflow/hooks/pre-push`, check it by hand: a dry-run push of a badly named branch is rejected, and a well-named one passes.

```sh
git push --dry-run origin HEAD:refs/heads/bad-name        # rejected
git push --dry-run origin HEAD:refs/heads/fix/1-some-name # passes
```

After a change to `implementers/`, run the dispatch check in the tool mapping of each tool the change reaches. Each tool mapping in `implementers/` holds its own, and a change to `settings.md` or `instructions.md` reaches every tool with rows in the implementer settings. Run a tool mapping's dispatch check before changing its status, too.

Each step of a dispatch check names what to observe. A step whose observation differs is a failure. Correct the template or the facts in that tool mapping, then run the check again from step 1.

## Hooks

`.githooks/pre-push` runs `skills/setup-workflow/hooks/pre-push`, the same hook `/setup-workflow` copies into other repos. Edit that one; the wrapper holds no logic.

The repo has no install step to set `core.hooksPath`, so each clone sets it once by hand:

```sh
git config core.hooksPath .githooks
```
