# An agent generates the implementer types, and the repo has no script

Writing a file per tool from a table and a template is work a script usually does. We kept it as a procedure in Job 1 that an agent follows. The repo is plain markdown and plain `git` with nothing to install. A script would need a runtime that exists on Windows, macOS and Linux, plus a parser for the implementer settings. The work is putting two or three values into a short template, and every tool this workflow runs in has an agent to do it.

Job 1 rewrites each generated file whole on every run and stamps it with the commit it came from. A hand edit to a generated file does not survive the next run, and a stale file can be told from a current one.
