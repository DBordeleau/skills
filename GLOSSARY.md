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
