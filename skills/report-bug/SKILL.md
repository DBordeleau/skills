---
name: report-bug
description: "File a bug report on the issue tracker. Use when you find a defect outside your current task, or the user asks to file a bug."
---

# Report bug

File a bug report: an issue that describes a defect as someone observed it, before anyone has investigated it. A defect left in a session's report is lost when the session ends, and a defect fixed in the current diff is scope creep, so it goes to the tracker.

Read `docs/agents/issue-tracker.md` and `docs/agents/labels.md` first. If they don't exist, tell the user to run `/setup-workflow`.

## Who files

**A sub-agent reports the bug and stops there.** Put it in your report to the session that dispatched you: what happens, what should happen, the reproduction or the four facts from the evidence bar below, and the evidence. That session files it. Sub-agents never write to the tracker, so that one session can merge duplicates when parallel sub-agents hit the same bug.

**The session the user is talking to files**, once, at the end of its run. Collect the bugs that you and your sub-agents found, merge the ones that describe the same defect, and file them after the work and before your closing message. When the user ran `/report-bug`, or `/diagnose` called this skill with a description, file now, and ask the user for any of the four facts below that their description lacks.

## What to file

File a defect that existed before the current task and sits outside it. A defect is behaviour that differs from what the code's callers, its docs or its spec expect.

- A defect the current change introduced belongs to the current work: fix it there.
- Style complaints, refactor ideas and missing features are not defects, and this skill files none of them. Mention them in your report if they matter.

## The evidence bar

Every bug report opens `## Reproduction` with `Reproduced: yes` or `Reproduced: no`.

- `Reproduced: yes` means someone ran a check and saw it fail on this exact symptom. Give the command or the steps, the commit SHA it ran at, and its output under `## Evidence`.
- `Reproduced: no` covers everything else, including a defect found by reading the code. Reading the code is not a reproduction. The report must still name four facts. The input goes in `## Reproduction`, the actual result in `## What happens`, the expected result in `## What should happen`, and the location, as a file path and function, in `## Evidence`. Give the commit SHA the code was read at.

Build no reproduction for a bug outside your task. File it with the evidence you already have, as `Reproduced: no` when no check has run. If you cannot name all four facts, you hold a suspicion and not a bug report: mention it in your report or your message to the user, and file nothing.

Redact secrets, tokens and personal data from every command, output and path before it goes in a bug report. Write `<REDACTED>` in their place.

## Search before filing

Search issues, open and closed, for the symptom and for the error text, using **Search issues** in `docs/agents/issue-tracker.md`. Read each match.

- **An open match** is the same bug. Comment on it with the new evidence, and file nothing.
- **A closed match** means the bug came back. File a new bug report, and link the closed issue under `## Evidence`.
- **No match**: file a new bug report.

One bug goes in one issue. Two defects get two bug reports, even when one session found both.

## Writing it

Call the Skill tool with `unslop` before you write the bug report or a comment, and apply its rules to the prose. The template's headings are a fixed format and stay as they are.

The title names the broken behaviour as a user would see it: "Saving a draft with an emoji in its title drops the title", not "Bug in serializeTitle".

The bug report proposes no fix. A guessed fix anchors whoever investigates it. Any cause you suspect goes under `## Suspected cause`, marked as a guess.

<bug-report-template>

## What happens

What someone saw, in observable terms: the output, the error text, the wrong value.

## What should happen

What they expected instead, and what says so: the docs, the spec, a test, a caller.

## Reproduction

Reproduced: yes | no

The command or the steps, and the commit SHA they ran at, or the code was read at.

## Evidence

The output as it printed, trimmed to the lines that show the failure. For `Reproduced: no`, the location as a file path and function. A link to a closed issue this matches.

## Found while

A link to the issue or PR the work was for. For a bug the user reported, what they were doing.

## Suspected cause

Optional. Start with "A guess, not a root cause:". It may name files.

</bug-report-template>

## Publishing it

Publish the bug report to the project issue tracker, and label it a bug that needs planning (`docs/agents/labels.md` gives the strings). On a local tracker, `docs/agents/issue-tracker.md` says where the file goes and how it records the type and the status.

## Listing what you filed

End your run by listing every bug report you filed or commented on, each with its number and title. The list goes in your closing message. When the session has a PR, the same list also goes in the PR body under `## Summary`, after the sentence and the visual. Edit the body if the PR is already open.
