# Review Plan

Critique a plan and gate on a verdict. Invoked by Create Plan after authoring; also selectable directly when the `USER_PROMPT` asks to review, critique, or validate an existing plan.

1. Locate the Plan - Resolve the target plan `.html` path from the `USER_PROMPT` or from the invoking workflow
2. Spawn a Reviewer - Launch a fresh subagent (so the critique is independent of the authoring context), giving it the plan path and the checks below
3. Checks - The reviewer verifies:
   - No `{{...}}` placeholder token remains anywhere in the file
   - Phases and tasks are executable top to bottom by a fresh agent with no unstated context
   - Every referenced existing file path actually exists in the repo; new-file paths land in sensible locations
   - Validation commands are the project's real quality gates (cross-check `CLAUDE.md`, CI config, check tooling) and can actually run
   - Every phase ends with a concrete Testing Strategy and validation loop
   - The plan honors the project's documented conventions (`CLAUDE.md`, `.claude/rules/`), e.g. TDD task ordering where mandated
4. Verdict - The reviewer returns exactly one of:
   - `Ready` - no blocking issues
   - `Minor` - small fixable issues, each listed with its location
   - `Major` - structural problems (unexecutable phases, fabricated paths, wrong validation gates), each listed
5. Act on the Verdict:
   - `Ready`: report and continue
   - `Minor`: apply the listed fixes to the plan directly, do NOT re-run the review (avoid loops), report the fixes made
   - `Major`: stop and surface the issues to the user before proceeding
6. Report - State the verdict, the issues found, and any fixes applied
