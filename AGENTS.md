# Instructions for agents

This repo holds one person's engineering workflow: a set of skills, the policy they share, and the per-repo config that wires them into a project. It is the single source of truth for that workflow. If you are an agent reading this, you have been pointed here to do one of three jobs. Find yours below and follow it.

Everything here is plain markdown and plain `git`. There is no CLI to install, no package to build, and nothing that assumes a particular model or tool.

- **Job 1 — set this workflow up on a machine.** The user said something like "set up my workflow from this repo".
- **Job 2 — set this workflow up in a project.** The user said something like "set this repo up for my workflow". Run the `setup-workflow` skill; if skills aren't available in your tool, read [skills/setup-workflow/SKILL.md](skills/setup-workflow/SKILL.md) and follow it yourself.
- **Job 3 — change the workflow.** The user wants a skill edited, added or removed. Read [skills/writing-for-agents/SKILL.md](skills/writing-for-agents/SKILL.md) first, then make the change here, in this repo. Never edit an installed copy: installed copies are links back to this one.

If the user's request doesn't match any of those, they probably want the workflow *used*, not installed. See [WORKFLOW.md](WORKFLOW.md) for the daily loop.

## Job 1: set up a machine

### What you're doing

Each skill is a directory under `skills/` holding a `SKILL.md` and, optionally, reference files and an `agents/openai.yaml` adapter. Installing means making those directories visible to the tools on this machine at the paths they look in, **as links back to this repo** so that editing the repo changes the behaviour everywhere, with no copies to drift.

### Steps

**1. Find this repo's path.** The user may have cloned it anywhere. Use the directory this file is in; call it `$REPO`. If it isn't cloned yet, clone it somewhere stable (not a temp directory) and ask the user where they want it if it isn't obvious.

**2. Work out which tools are on this machine,** by checking which of these directories exist. Create the ones for tools the user says they use:

| Tool | Skills directory |
| --- | --- |
| Claude Code | `~/.claude/skills/` |
| Codex, and other agents.md tools | `~/.agents/skills/` |
| Anything else | ask the user where that tool reads skills from |

**3. Back up what's there.** If a directory already holds skills, copy it aside (`skills-backup-<today>`) before touching anything, and tell the user you did. Pay attention to skills this repo supersedes — if `grill-me`, `grill-with-docs`, `grilling`, `to-spec`, `to-tickets`, `implement`, `implement-spec` or `setup-matt-pocock-skills` are already installed from somewhere else, they must be removed, not left beside these: two skills that answer to the same job will both fire.

**4. Link each skill** in `$REPO/skills/` into each tool directory, one link per skill, named after the skill.

POSIX:

```sh
for s in "$REPO"/skills/*/; do
  name=$(basename "$s")
  ln -sfn "$s" "$HOME/.agents/skills/$name"
  ln -sfn "$s" "$HOME/.claude/skills/$name"
done
```

Windows PowerShell (symlinks need Developer Mode or an elevated shell; a directory junction needs neither and behaves the same here):

```powershell
Get-ChildItem "$REPO\skills" -Directory | ForEach-Object {
  foreach ($base in @("$HOME\.agents\skills", "$HOME\.claude\skills")) {
    $link = Join-Path $base $_.Name
    if (Test-Path $link) { Remove-Item $link -Recurse -Force }
    New-Item -ItemType Junction -Path $link -Target $_.FullName | Out-Null
  }
}
```

If linking is genuinely impossible on this machine, copy instead — but then say plainly that the copies will drift and that `git pull` won't update them.

**5. Install the shared policy.** [policy/global.md](policy/global.md) is the machine-level block that applies to every project. Add it to each tool's global instruction file (`~/.claude/CLAUDE.md` for Claude Code, `~/.agents/AGENTS.md` for agents.md tools), under its own `## Workflow policy` heading. If a block with that heading is already there, replace it rather than appending a second one. Don't delete the user's own notes around it.

**6. Verify, and report in four lines.** Every skill in `skills/` resolves through its link at each tool path; the superseded skills from step 3 are gone; the policy block appears exactly once per file; and the backup from step 3 is where you said it is. List the installed skill names so the user can see what they have.

### Updating later

`git pull` in `$REPO`. Links mean nothing else is needed. Re-run step 5 only if `policy/global.md` changed, and step 4 only if a skill was added or removed.

## Reading order, if you are new here

1. [WORKFLOW.md](WORKFLOW.md) — the loop these skills form, and which one to reach for when.
2. [policy/global.md](policy/global.md) — the rules that hold in every project.
3. [skills/setup-workflow/SKILL.md](skills/setup-workflow/SKILL.md) — what a configured project looks like.
