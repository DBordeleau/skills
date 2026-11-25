---
name: plan-with-me
description: "A relentless interview that sharpens a plan, design, or idea until nothing is silently assumed, then records what got decided as glossary terms and ADRs. Use when the user wants to think something through before building it."
---

# Plan with me

Interview the user until you reach a shared understanding, then record what was decided.

Run this only when the user asked for it, or when another skill called it. Do not start an interview on your own.

Two phases, in order:

1. **Interview** (below) — work the design tree to empty.
2. **Record** — once the frontier is empty, call the Skill tool with `domain-modeling` and write up what got settled.

Do not interleave them. Writing docs mid-interview inflates the context you are still interviewing in, and most rounds settle nothing worth recording yet.

## Phase 1: interview

Map the plan as a **design tree**: every decision branches into the decisions that hang off it.

Work the tree in **rounds**. The **frontier** is every decision whose prerequisites are already settled: the questions you can ask _now_ without guessing at answers you haven't heard yet. Ask the whole frontier in one round: number each question and give your recommended answer. Then wait for the user's answers before the next round.

Format a round like so:

```
❓ **Q1** - **<question title>**: <question body, might be multiple paragraphs, including multiple choices>

➡️ <your recommended answer>

---

❓ **Q2** - **<question title>**: <question body, might be multiple paragraphs, including multiple choices>

➡️ <your recommended answer>
```

Word each question so "yes" accepts your recommended answer.

Call the Skill tool with `unslop` before the first round, and apply its rules to the question bodies and your recommended answers. The markers, numbering and separators above are a fixed format and stay as they are.

Each round the user answers reshapes the tree: settled decisions push the frontier outward and unblock questions that depended on them. Recompute the frontier and ask the next round. A question whose answer depends on another question still open in this round belongs to a _later_ round, not this one.

Be relentless. A plan that survives this should have no soft spots left: every branch visited, every assumption either confirmed by the user or found as a fact.

Finding _facts_ is your job, never the user's. When a frontier question needs a fact from the environment (filesystem, tools, the issue tracker, external docs), dispatch a sub-agent with a fresh context window to find it; don't ask the user for anything you could look up yourself. Don't block on it: a running exploration is an unsettled prerequisite, so only the questions downstream of it wait for the sub-agent to report; ask the rest of the frontier now. The _decisions_ are the user's: put each to them and wait.

Keep the interview context lean. Read what you need through sub-agents and summarise; don't pull whole files into this window to answer a single question.

The interview is done when the frontier is empty: every branch of the design tree visited, nothing left silently assumed. Do not act on the plan until the user confirms you have reached a shared understanding.

## Phase 2: record

Once the frontier is empty and the user has confirmed, call the Skill tool with `domain-modeling` and make one pass over what the interview settled:

- **Glossary terms**: every domain concept the interview named or renamed. If the interview invented a word for something, it belongs in `GLOSSARY.md` before it reaches a spec.
- **ADRs**: every decision that closed off an alternative someone could reasonably reopen later. A decision that was obvious doesn't need one; a decision you argued about does.

Write only what the interview actually settled. Don't backfill the whole domain, and don't record a decision the user deferred.

## Next step

Say what the user can do next, in one line: `/to-spec` to turn this conversation into a spec on the issue tracker, or, if a `prototype` skill is installed, `/prototype` first when a question survived the interview only because nobody has seen it run yet.
