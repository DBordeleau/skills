# skills

My engineering workflow in one place: the skills I use, the policy they share, and the per-repo config each project needs.

Installing it on a machine links these directories into whatever tool is on that machine, so editing a skill here changes how every agent on every machine behaves. Setting up a project writes tracked config into that project, so a fresh clone and a fresh agent find the same rules.

## Before using

I recommend you go through the repo and tailor it to meet your needs and preferences. There are two places you can look first to get a high level understanding of the workflow.

1. [policy/global.md](https://github.com/DBordeleau/skills/blob/master/policy/global.md) prevents agents from authoring commits, PRs, issues, comments. Everything on GitHub will show up as you. In my projects I prefer people take ownership of their PRs even if they were implemented by agents. It also enforces branch naming, `<type>/<issue>-<slug>` with a conventional type and the issue number, and keeps those prefixes off commit subjects.
2. **Read [WORKFLOW.md](WORKFLOW.md) to understand the intended way to use the skills to implement a new feature from scratch.** You may wish to change the extent to which you are involved in the loop. All of the top level skills like /plan-with-me are meant to be invoked by a human. Implementations are handled autonomously by the orchestrator agent, but it's important to understand that this workflow teaches agents to communicate with humans and other agents differently. 

## Getting started

**On a new machine.** Clone this repo, then tell an agent:

> Set up my workflow from this repo.

It reads [AGENTS.md](AGENTS.md) and does the rest. It links the skills into the tools it finds, installs the shared policy, backs up whatever was already there, and reports what it did. It also writes each tool's implementer types from [implementers/](implementers/), so implementers run on the model and reasoning level set there. It works in Claude Code, the ChatGPT app, Pi, or anything else that can read files and run `git`.

**In a project.** From inside that repo, tell an agent:

> Set this repo up for my workflow.

That runs [`setup-workflow`](skills/setup-workflow/SKILL.md), which asks at most three questions and then commits the config.

**Day to day.** See [WORKFLOW.md](WORKFLOW.md).

## The skills

[WORKFLOW.md](WORKFLOW.md) shows how they connect. Each skill's `SKILL.md` says how it runs.

Building a feature:

- `plan-with-me` interviews you about a plan until nothing is assumed, then records the decisions as glossary terms and ADRs.
- `to-spec` writes the conversation up as a spec on the issue tracker.
- `to-tickets` breaks a spec into tickets, one vertical slice each.
- `implement` builds one spec or one ticket through sub-agents.
- `implement-spec` builds a spec's whole ticket graph across parallel sub-agents.
- `code-review` reviews a diff against the repo's standards and against its spec.
- `pr` writes the PR body.
- `improve-codebase-architecture` finds refactors in an existing codebase and hands one to `plan-with-me`.

Fixing a bug:

- `report-bug` files a bug report on the issue tracker. You run it when you see a bug, and agents run it for a defect they find outside their task.
- `diagnose` builds a reproduction, finds the root cause, and waits for you to confirm it. Then it fixes the bug or leaves a fix spec. It runs only when you invoke it.

Setting up and writing:

- `setup-workflow` writes a project's config and labels.
- `tdd`, `domain-modeling`, `codebase-design` and `writing-for-agents` hold the reference the other skills consult.
- `unslop` cuts AI tells from anything written for a person.

## Layout

```
AGENTS.md            what an agent pointed at this repo should do
WORKFLOW.md          the loop these skills form
policy/global.md     machine-level rules: attribution, writing, branches, delegation, effort
implementers/        implementer settings, instruction text, and one tool mapping per tool
skills/<name>/
  SKILL.md           the skill itself
  agents/openai.yaml adapter for tools that want one
  *.md               reference files the skill links to
```

## Changing it

The skills, `AGENTS.md` and `policy/global.md` are written for agents. Before you edit one of them, read [`writing-for-agents`](skills/writing-for-agents/SKILL.md), the house style for documents an agent reads. This README is written for people and follows [`unslop`](skills/unslop/SKILL.md) instead.

- A per-repo convention, such as a label string, or a shell problem specific to one machine, belongs in that repo's `docs/agents/*.md`. 
- A rule that holds everywhere belongs in `policy/global.md`.
- A procedure belongs in a skill.

### Getting a change onto the other machines and repos

`git pull` in the clone is the whole job when you edited a skill's own text, because an installed skill is a link back to this repo. These kinds of change need one more step:

- **A skill added or removed.** Re-link on each machine. `git pull` creates no link, and a skill you delete leaves a dangling one that still shows in the skill list.
- **`policy/global.md`.** Re-copy the block on each machine. It is a copy rather than a link, because the files it goes into hold your own notes too.
- **Anything in `implementers/`, or the machine settings at `~/.agents/implementers.md`.** Generate the implementer types again on each machine. They are copies written from both, and a machine's settings reach only that machine.
- **A fragment under `skills/setup-workflow/fragments/`.** Re-run `/setup-workflow` in **each repo you work in**. A repo's `docs/agents/*.md` are copies, committed so that a clone has them without this repo, so a fragment change here reaches no repo on its own. The re-run merges only what changed upstream and keeps the parts filled in for that repo, such as its real check commands.

Rather than working out which case applies, tell an agent in the clone: "update my workflow from this repo". It reads [AGENTS.md](AGENTS.md#updating-an-installation), which holds every case, and takes the extra step only when there is one.

A running session loaded its skills when it started, so restart it before wondering why an edit had no effect.

## Credits

Most of these skills started as [mattpocock/skills](https://github.com/mattpocock/skills). `unslop` is from [pstack](https://github.com/cursor/plugins/tree/main/pstack) by Lauren Tan. See [CREDITS.md](CREDITS.md).
