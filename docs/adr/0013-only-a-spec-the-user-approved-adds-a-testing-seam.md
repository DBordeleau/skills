# Only a spec the user approved adds a testing seam

The testing seams file is useful only while it lists seams someone chose on purpose. An implementer that can add an entry will add one for whatever function it has just written, and the pre-push hook will then pass the low test the file exists to stop.

We decided a testing seam enters the file through a spec or a fix spec the user approved. to-spec and improve-codebase-architecture can propose one. The spec's seams list marks it as new and gives its entry. The ticket that builds the module carries that entry, and its implementer adds it to the testing seams file in the same change, so the entry shows in the pull request beside the tests it permits. code-review rejects a change to the file that no spec names. A seam exception enters the same way, so the hook needs no bypass for one.

An implementer that finds no listed testing seam can observe its behaviour stops and reports that. It does not write the test lower down, and it does not add an entry. This costs a round trip to the user on some tickets. We took that cost because the other choice lets the implementer make the decision the workflow has shown it makes badly.
