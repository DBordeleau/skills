# The testing seams file lists agreed seams, and old tests stay until a seam retirement

Every repo that adopts the testing seams file already has tests, and some of them sit at seams the user would not choose today. A first file that listed every place tests live would record that overlap as if it were agreed.

We decided a seam survey writes a repo's first file in three steps. It inventories every place tests go through, by module, in its report. It proposes one testing seam for each module under ADR 0010 and sorts each other seam into seam exception or seam retirement. The user answers a module at a time, and only the confirmed seams go into the file. With no file, the pre-push hook skips its check.

The hook checks only the test files a change adds. A seam left out of the file keeps its old tests, which still run and can still be edited, and no new test file can join them. The repo is checked from the day the user approves its first file, and nothing fails on that day.

A seam retirement is a ticket. For each test at the retired seam the implementer runs a break check. It breaks the behaviour the test guards and runs the tests at the surviving seam. If one fails, the lower test is deleted. If none fails, the implementer writes the case at the surviving seam, watches it fail on the same break, and then deletes the lower test. We fixed this procedure because the two steps it covers are the ones a model does badly without one. Left to choose, a model keeps the lower seam, and its statement that a higher test already covers a case is an opinion until a test fails.
