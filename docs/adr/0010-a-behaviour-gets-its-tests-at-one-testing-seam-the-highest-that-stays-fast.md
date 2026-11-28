# A behaviour gets its tests at one testing seam, the highest that stays fast

Implementers kept adding tests below the seam a spec agreed. In YOFHL DB, spec 211 named two seams for a table shape check, the implementer tested it at a third, and the review fix-up put its regression test at two. Until November 2026 no skill said how to choose among seams. to-spec said "highest", diagnose said "nearest", and tdd left the choice to whoever wrote the test.

We decided each behaviour gets its tests at one testing seam: the highest one where a test can observe the behaviour and still run fast and deterministically. A lower seam gets tests only as a seam exception, for cases the higher seam can't reach at a reasonable cost, such as a large set of input combinations or an error path that is hard to trigger from above.

This sides with the classicist reading of test-driven development, where the thing under test is a behaviour seen through a module's interface. It goes against the test pyramid's advice to push each test as far down as it will go. The pyramid's reasons were the speed of a low test and the way its failure names the broken function. A test at a deep module's interface runs in process, so the speed argument is weak here. The loss in localisation is real, and we accept it because a test on a helper has to change whenever the helper's shape does.
