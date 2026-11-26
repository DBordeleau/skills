# The user confirms the root cause before any fix is designed

`/diagnose` stops at two gates. It presents no root cause without a reproduction, and it designs no fix until the user has confirmed the root cause. Matt Pocock's `diagnosing-bugs` shows its hypotheses and carries on, which lets it run unattended. We block because what the software should do is the user's call, and a fix built on the wrong root cause costs more than one message.

## Consequences

`/diagnose` cannot run unattended. A bug that cannot be reproduced stops at the first gate and waits for the user.
