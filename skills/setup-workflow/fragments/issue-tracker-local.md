<!-- /setup-workflow wrote this from DBordeleau/skills@COMMIT, fragments/issue-tracker-local.md. Edit it freely: an update merges only what changed upstream. -->

# Issue tracker: Local Markdown

Issues and specs for this repo live as markdown files in `.scratch/`.

## Conventions

- One feature per directory: `.scratch/<feature-slug>/`
- The spec is `.scratch/<feature-slug>/spec.md`
- Implementation issues are one file per ticket at `.scratch/<feature-slug>/issues/<NN>-<slug>.md`, numbered from `01`, never a single combined tickets file
- Bug reports are one file per bug at `.scratch/bugs/<NN>-<slug>.md`, numbered from `01`. `<NN>` is the bug report's issue number: `/diagnose <NN>` takes it, and so does the `fix/<NN>-<slug>` branch that fixes it
- State is recorded as a bold status line near the top of each issue file, holding one label string from `docs/agents/labels.md`, such as `**Status:** needs-planning`, or `closed`. One status line holds one state
- A bug report has no type label here, so it carries a `**Type:** bug` line directly above its status line. It keeps that line when `/diagnose` rewrites it as a fix spec, whose status is `ready-for-implementation`. A **fix spec** is a spec labelled Bug, or marked `**Type:** bug` on a local tracker, whose body opens with an effort line and has `## Root cause` and `## Regression test`. A ticket whose parent is a fix spec counts as one, and takes those sections from its parent
- Where a GitHub issue would carry `needs-info` beside `needs-planning`, the status line holds `needs-info` alone. A rerun of `/diagnose` sets it back to `needs-planning`
- Comments and conversation history append to the bottom of the file under a `## Comments` heading
- **Close**: append the explanation under `## Comments`, then set `**Status:** closed`. An issue at `**Status:** wontfix` is closed too
- **Search issues**: search the files under `.scratch/` for each phrase, such as a symptom and an error text, and read every matching file. Its status line tells an open issue from a closed one

## When a skill says "publish to the issue tracker"

Create a new file under `.scratch/<feature-slug>/` (creating the directory if needed). A bug report goes under `.scratch/bugs/` instead, with the next free number.

## When a skill says "fetch the relevant ticket"

Read the file at the referenced path. The user will normally pass the path or the issue number directly.
