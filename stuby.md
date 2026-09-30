---
description: Implement functions marked with STUB - comments
---

Look for `STUB -` comments in the files listed in $ARGUMENTS. If no files were
specified, assume project-wide scope.

For each stubbed-out function, infer what it should do from:
* The function name
* Function args and type annotations
* The stub comment and other comments in the function
* Usage of the function elsewhere in the codebase

If the intent is truly ambiguous, or you suspect a mistake, use the `question`
tool to clarify before implementing.

Implement each function following the project's AGENTS.md conventions!