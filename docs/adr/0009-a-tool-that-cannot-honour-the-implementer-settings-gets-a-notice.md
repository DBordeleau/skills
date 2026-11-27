# A tool that can't honour the implementer settings gets a notice, not a second way to dispatch

Some tools can't set everything from an implementer type's file. In November 2026 an Antigravity agent file sets a model and no reasoning level, Pi has sub-agents only through an extension, and open Codex issues report sub-agents running on the parent session's model. In each case the tool's command line can start a separate process at a chosen model and reasoning level.

We decided the workflow uses each tool's own sub-agent files and accepts what they can't set. When a setting can't be honoured, the orchestrator says so once and runs implementers on the session's model. Starting each implementer as a separate process changes how the orchestrator dispatches work and collects reports, and that is a second workflow kept for one tool.

A tool mapping is marked verified only after someone ran one dispatch in that tool and read the model and reasoning level from the tool's own record of the run. Whatever that run showed the tool can't set is written into the tool mapping as a limit. Job 1 generates files for verified tool mappings only.
