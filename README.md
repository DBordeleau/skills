# skills

My engineering workflow in one place: the skills I use, the policy they share, and the per-repo config each project needs.

Installing it on a machine links these directories into whatever tool is on that machine, so editing a skill here changes how every agent on every machine behaves. Setting up a project writes tracked config into that project, so a fresh clone and a fresh agent find the same rules.

## Before using

I recommend you go through the repo and tailor it to meet your needs and preferences. There are two places you can look first to get a high level understanding of the workflow.

1. [policy/global.md](https://github.com/DBordeleau/skills/blob/master/policy/global.md) prevents agents from authoring commits, PRs, issues, comments. Everything on GitHub will show up as you. In my projects I prefer people take ownership of their PRs even if they were implemented by agents. It also enforces the use of conventional commits and branch naming.
2. **Read [WORKFLOW.md](WORKFLOW.md) to understand the intended way to use the skills to implement a new feature from scratch.** You may wish to change the extent to which you are involved in the loop. All of the top level skills like /plan-with-me are meant to be invoked by a human. Implementations are handled autonomously by the orchestrator agent, but it's important to understand that this workflow teaches agents to communicate with humans and other agents differently. 

## Getting started

**On a new machine.** Clone this repo, then tell an agent:

> Set up my workflow from this repo.

It reads [AGENTS.md](AGENTS.md) and does the rest. It links the skills into the tools it finds, installs the shared policy, backs up whatever was already there, and reports what it did. It works in Claude Code, the ChatGPT app, Pi, or anything else that can read files and run `git`.

**In a project.** From inside that repo, tell an agent:

> Set this repo up for my workflow.

That runs [`setup-workflow`](skills/setup-workflow/SKILL.md), which asks at most three questions and then commits the config.

**Day to day.** See [WORKFLOW.md](WORKFLOW.md).

## Layout

```
AGENTS.md            what an agent pointed at this repo should do
WORKFLOW.md          the loop these skills form
policy/global.md     machine-level rules: attribution, writing, branches, delegation, effort
skills/<name>/
  SKILL.md           the skill itself
  agents/openai.yaml adapter for tools that want one
  *.md               reference files the skill links to
```

## Changing it

Edit the skill here, then `git pull` on the other machines. Nothing needs reinstalling, because an installed skill is a link back to this repo. Adding a skill, removing one, or changing `policy/global.md` needs one more step per machine, and a repo's own `docs/agents/*.md` are copies that need `/setup-workflow` re-run to pick a change up. [AGENTS.md](AGENTS.md#updating-an-installation) has the four cases and what each costs.

Read [`writing-for-agents`](skills/writing-for-agents/SKILL.md) before editing anything here. It is the house style for this kind of document, and it covers the skills themselves.

Three places take a change, depending on how far it reaches. A per-repo convention, such as a label string, a check command or a shell problem specific to one machine, belongs in that repo's `docs/agents/*.md`. A rule that holds everywhere belongs in `policy/global.md`. A procedure belongs in a skill.

## Credits

Most of these skills started as [mattpocock/skills](https://github.com/mattpocock/skills). `unslop` is from [pstack](https://github.com/cursor/plugins/tree/main/pstack) by Lauren Tan. See [CREDITS.md](CREDITS.md).
