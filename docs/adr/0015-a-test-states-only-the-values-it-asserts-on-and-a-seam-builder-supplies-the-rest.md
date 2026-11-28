# A test states only the values it asserts on, and a seam builder supplies the rest

We measured YOFHL DB in November 2026 to find why one source change edits many test files. Tests at low seams explained little of it: about 6 to 10 of 906 test cases repeated a behaviour across seams. Copied setup explained most of it. Commit `9645aec` changed 15 test files because each wrote its own seed rows and config literals, and `5fea96a` gave seven season import test files the same three-line edit because a new required input had to reach setup copied between them. That repo's own docs told each test to write its own seed SQL.

We decided a test states inline only the values it asserts on. Everything else it needs comes from the seam builder, the setup shared by the tests at that testing seam. The testing seams file names each seam's builder, tdd tells an implementer to use it and extend it, and code-review flags setup copied from another test file.

The other choice was to keep each test's setup whole in the test, which makes a test readable without opening another file. The rule keeps the part of that which matters: the data an assertion depends on is still in the test. A new required column now changes one builder.
