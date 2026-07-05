# Create Plan

1. Analyze Requirements - THINK HARD and parse the `USER_PROMPT` to understand the core problem and desired outcome
2. Explore Codebase - Understand existing patterns, architecture, relevant files, and prior specs to back-reference. Read `AI_DOCS` for AI/agent-facing documentation and `APP_DOCS` for application documentation to ground the plan. Also read the host project's own conventions when present: `README.md`, `CLAUDE.md`, and `.claude/rules/`. Treat non-planf3 specs found in `PLAN_OUTPUT_DIRECTORY` as read-only context.
3. Design Solution - Develop technical approach including architecture decisions and implementation strategy, honoring the project conventions found in step 2 (e.g. TDD task ordering where the project mandates it)
4. Author HTML Plan - Fill the `## Plan Template`, replacing every `{{PLACEHOLDER}}` and repeating `<!-- repeat -->` blocks as needed. Source every Validation Command from the project's real quality gates (CLAUDE.md tooling sections, CI config, existing check tooling) — never invent generics. Record the current git branch (`git branch --show-current`) in the `branches` metadata field.
5. Review Plan - Run the sub-workflow in `workflows/review-plan.md` on the authored plan. Apply Minor fixes directly; surface Major issues to the user before proceeding.
6. Surface Questionables - If `QUESTIONABLE` is true, populate the conditional Questionables section with open decisions/assumptions/risks; otherwise omit the section
7. Generate Filename - Create a descriptive kebab-case filename based on the plan's main topic, prefixed with `plan-`
8. Save - Write the plan to `PLAN_FILE` and provide a summary of key components
9. Open in Browser - Open the saved plan in `BROWSER` (e.g. `open -a "Arc" PLAN_FILE`)
