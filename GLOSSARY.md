# Workflow skills

The language of the engineering workflow this repo defines: the skills, the issues they read and write, and the states those issues move through.

## Language

### Bugs

**Bug report**:
An issue that describes a defect as someone observed it, filed before anyone has investigated it. It proposes no fix.
_Avoid_: bug ticket, defect report, QA issue

**Reproduction**:
A check that someone ran and saw fail on the exact symptom a bug report describes. Reading the code is not a reproduction.
_Avoid_: feedback loop, repro

**Root cause**:
The defect that a reproduction's failure traces back to, as distinct from the symptom the bug report describes.
_Avoid_: diagnosis, underlying issue

**Fix spec**:
A bug report rewritten after the user confirms its root cause. It states the root cause, the fix and the regression test.
_Avoid_: fix plan, agent brief, bug spec

### Implementers

**Tool**:
A program an agent session runs in, such as Claude Code, Codex, Pi or Antigravity.
_Avoid_: harness, client, CLI, agent

**Effort level**:
`medium` or `high`, written on the first line of a ticket or fix spec to say how demanding the work is. It names no model.
_Avoid_: tier, difficulty, reasoning effort

**Reasoning level**:
A tool's own setting for how much a model thinks before it acts. Each tool has its own list of values.
_Avoid_: effort, thinking level, reasoning effort

**Implementer type**:
One of the two named sub-agent definitions a tool loads, `implementer-medium` and `implementer-high`, one per effort level.
_Avoid_: agent role, worker, sub-agent type

**Implementer settings**:
The table that gives each tool and effort level a model and a reasoning level.
_Avoid_: implementer config, model config

**Machine settings**:
The optional file on one machine whose entries take the place of the default implementer settings there.
_Avoid_: overrides, local config, user settings

**Tool mapping**:
The description of how one tool takes an implementer type: where the definition goes, what it looks like, what the tool can't set, and whether anyone has checked it on a real run.
_Avoid_: adapter, tool config

### Testing seams

**Seam**:
The location where a module's interface lives.
_Avoid_: boundary

**Testing seam**:
The seam a test goes through. A repo agrees its testing seams with the user, and a behaviour gets its tests at one of them.
_Avoid_: test level, test layer, test boundary

**Testing seams file**:
A repo's list of the testing seams the user has agreed to, kept at the repo's root as `TESTING-SEAMS.md`.
_Avoid_: seam registry, seams document, seam list

**Seam exception**:
A testing seam below a module's main one, listed with the reason it earns tests of its own.
_Avoid_: low-level seam, unit seam

**Seam builder**:
The shared setup that the tests at one testing seam use.
_Avoid_: fixture, factory, test helper

**Seam survey**:
A pass over a repo's existing tests that proposes its testing seams for the user to confirm and reports what to clean up.
_Avoid_: seam audit, test audit

**Seam retirement**:
Ending the use of a testing seam. Its tests move to the testing seam that survives, or are deleted once a break check passes.
_Avoid_: seam removal, test consolidation

**Break check**:
Breaking a behaviour on purpose to see a test at the surviving testing seam fail, done before a lower test of that behaviour is deleted.
_Avoid_: mutation test, coverage check
