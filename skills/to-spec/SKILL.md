---
name: to-spec
description: "Turn the current conversation into a spec and publish it to the project issue tracker: no interview, just synthesis of what you've already discussed."
disable-model-invocation: true
---

This skill takes the current conversation context and codebase understanding and produces a spec. Do NOT interview the user; just synthesize what you already know.

Read `docs/agents/issue-tracker.md`, `docs/agents/labels.md` and `docs/agents/conventions.md` first. If they don't exist, tell the user to run `/setup-workflow`.

## Process

1. Explore the repo to understand the current state of the codebase, if you haven't already. Use the project's domain glossary vocabulary throughout the spec, and respect any ADRs in the area you're touching.

2. Sketch out the seams at which you're going to test the feature. Existing seams should be preferred to new ones. Use the highest seam possible. If new seams are needed, propose them at the highest point you can. The fewer seams across the codebase, the better - the ideal number is one.

Check with the user that these seams match their expectations.

3. Call the Skill tool with `unslop`, write the spec using the template below, and apply its rules to every section before publishing. A spec is read by humans for months; the template's headings are a fixed format and stay as they are. Then publish it to the project issue tracker. Label it as a spec that is ready to implement (`docs/agents/labels.md` gives the strings), and remove the planning label if you are rewriting an issue that carried one. No further planning pass is needed.

4. Recommend what to do next, in the terms below. This is the last thing you say, and you always say it.

<spec-template>

## Problem Statement

The problem that the user is facing, from the user's perspective.

## Solution

The solution to the problem, from the user's perspective.

## User Stories

A LONG, numbered list of user stories. Each user story should be in the format of:

1. As an <actor>, I want a <feature>, so that <benefit>

<user-story-example>
1. As a mobile bank customer, I want to see balance on my accounts, so that I can make better informed decisions about my spending
</user-story-example>

This list of user stories should be extremely extensive and cover all aspects of the feature.

## Implementation Decisions

A list of implementation decisions that were made. This can include:

- The modules that will be built/modified
- The interfaces of those modules that will be modified
- Technical clarifications from the developer
- Architectural decisions
- Schema changes
- API contracts
- Specific interactions

Do NOT include specific file paths or code snippets. They may end up being outdated very quickly.

Exception: if a prototype produced a snippet that encodes a decision more precisely than prose can (state machine, reducer, schema, type shape), inline it within the relevant decision and note briefly that it came from a prototype. Trim to the decision-rich parts, not a working demo, just the important bits.

## Testing Decisions

A list of testing decisions that were made. Include:

- A description of what makes a good test (only test external behavior, not implementation details)
- Which modules will be tested
- Prior art for the tests (i.e. similar types of tests in the codebase)

## Out of Scope

A description of the things that are out of scope for this spec.

## Further Notes

Any further notes about the feature.

</spec-template>

## Recommend tickets, or implementing as-is

A spec is not automatically a pile of tickets. Say which way to go and why, in three or four lines, then let the user decide.

Recommend **`/to-tickets`** when any of these is true:

- the work won't fit in one fresh context window;
- it crosses more than one agreed seam, or more than one module's interface;
- it contains a **wide refactor**: one mechanical change whose blast radius fans across the codebase, so no single slice can land green;
- part of it is independently demoable and you'd want it merged before the rest is written;
- parts of it can be built concurrently without touching the same files.

Recommend **`/implement`** (the spec as it stands) when none of them is:

- one seam, one module, a handful of vertical slices;
- nothing in it is worth a separate PR.

Don't hedge. Pick one, name the reason that decided it, and name the runner-up only if it was close. If you recommend tickets, say roughly how many you expect and what the first one would be.

---

Adapted from `to-spec` in [mattpocock/skills](https://github.com/mattpocock/skills), by Matt Pocock, MIT. See [LICENSE](./LICENSE).
