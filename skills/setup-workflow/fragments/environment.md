<!-- /setup-workflow wrote this from DBordeleau/skills@COMMIT, fragments/environment.md. Edit it freely: an update merges only what changed upstream. -->

# Environment

Things about this machine and repo that cost an agent a wasted turn each time it rediscovers them. Add to this file whenever a session loses a turn to the environment rather than to the problem.

## Shell

<!-- Keep whichever applies; delete the rest. -->

On Windows, the shell is PowerShell, with a POSIX `bash` tool also available. They take different syntax and the mistakes are expensive:

- Don't mix them in one command. Pick the shell, then write that shell's syntax throughout.
- In PowerShell, `&&` and `||` don't exist: use `A; if ($?) { B }`. No ternary, no `??`, no `?.`.
- For multi-line strings passed to a native command (commit messages, issue bodies), use a single-quoted here-string with the closing `'@` at column 0, or a `bash` heredoc. Unbalanced quotes in a long `-m` string is the single most common failed call.
- Prefer file-reading and search tools over `Get-Content` / `Select-String` / `cat` pipelines.

## Running the app

<!-- Fill in: the dev server command, the port, and anything that must be running first. -->

If a preview or screenshot step needs a local server, start it the way the project's own launch config does, and expect the first navigation to a new port to need permission. Don't retry a denied navigation; ask once.

## Worktrees

Sessions run in worktrees under a directory the tool chooses. A file outside the session's own worktree may be refused: read and write through the session's own path, and don't reach into another worktree's copy of the repo.

## Checks

<!-- Fill in the exact commands. -->

- Typecheck:
- Unit tests:
- Full suite:
- Lint:

Run the typechecker and the single relevant test file inside the sub-agent doing the work. Run the full suite once, at the end, in a sub-agent of its own.
