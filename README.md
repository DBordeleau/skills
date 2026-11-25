# skills

My engineering workflow, in one place: the skills I use, the policy they share, and the per-repo config that wires them into a project.

The point is that the workflow lives here and nowhere else. Installing it on a machine links these directories into whatever tool is on it, so editing a skill here changes how every agent on every machine behaves. Setting up a project writes tracked config into that project, so a fresh clone and a fresh agent find the same rules.

## Getting started

**On a new machine** — clone this repo, then tell an agent:

> Set up my workflow from this repo.

It reads [AGENTS.md](AGENTS.md) and does the rest: links the skills into the tools it finds, installs the shared policy, backs up whatever was there, and reports what it did. Works in Claude Code, the ChatGPT app, or anything else that can read files and run `git`.

**In a project** — from inside that repo:

> Set this repo up for my workflow.

That runs [`setup-workflow`](skills/setup-workflow/SKILL.md), which asks at most three questions and then commits the config.

**Day to day** — see [WORKFLOW.md](WORKFLOW.md).

## Layout

```
AGENTS.md            what an agent pointed at this repo should do
WORKFLOW.md          the loop these skills form
policy/global.md     machine-level rules: attribution, branches, delegation, effort
skills/<name>/
  SKILL.md           the skill itself
  agents/openai.yaml adapter for tools that want one
  *.md               reference files the skill links to
```

## Changing it

Edit the skill here and `git pull` on the other machines; the links do the rest. Read [`writing-for-agents`](skills/writing-for-agents/SKILL.md) before you do — it's the house style for this kind of document, and it applies to the skills themselves.

A per-repo convention (a label string, a check command, a shell gotcha) belongs in that repo's `docs/agents/*.md`, not here. A rule that should hold everywhere belongs in `policy/global.md`. A procedure belongs in a skill.

## Credits

Most of these skills started as [mattpocock/skills](https://github.com/mattpocock/skills). `unslop` is from [pstack](https://github.com/cursor/plugins/tree/main/pstack) by Lauren Tan. See [CREDITS.md](CREDITS.md).
