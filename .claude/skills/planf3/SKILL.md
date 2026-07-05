---
name: planf3
description: Creates a concise engineering implementation plan based on user requirements and saves it to specs directory
argument-hint: "[user-prompt] [questionable]"
---

# Plan F3

## Purpose

Create a detailed, **HTML-first** implementation plan based on the `USER_PROMPT` variable. The plan is authored as a single self-contained `.html` page so it can be opened in a browser, carry a synced visual identity, and be created/updated/consumed by the agent trifecta (engineer, team, AI agents). Analyze the request, think through the implementation approach, follow the `## Instructions`, and work through the `## Workflow` to produce the plan from the `## Plan Template`.

## Variables

USER_PROMPT: $1
QUESTIONABLE: $2 - default false
PLAN_OUTPUT_DIRECTORY: `specs/`
PLAN_FILE: `PLAN_OUTPUT_DIRECTORY/plan-<descriptive-kebab-name>.html`
AI_DOCS: `AI_DOCS/`
APP_DOCS: `APP_DOCS/`
SKILL_DIR: the directory this `SKILL.md` was loaded from (e.g. `~/.claude/skills/planf3/` when installed globally) — resolve `workflows/` against it, never against the working directory
IDE: `code`
BROWSER: `arc`

## Instructions

- IMPORTANT: If no `USER_PROMPT` is provided, stop and ask the user to provide it
- Carefully analyze the user's requirements provided in the `USER_PROMPT` variable
- Think deeply (ultrathink) about the best approach to implement the requested functionality or solve the problem
- Explore the codebase to understand existing patterns, documentation, previous specs and architecture
- Ground the plan in the host project's conventions: read the project's `README.md`, `CLAUDE.md`, and `.claude/rules/` when present (in addition to `AI_DOCS`/`APP_DOCS`), and author tasks that honor them — e.g. when the project mandates TDD, order tasks stub → failing test → implement rather than test-last
- Validation commands must be the project's real quality gates, discovered from `CLAUDE.md` tooling sections, CI config, or existing check tooling — never invented generics
- Other planning formats living in `PLAN_OUTPUT_DIRECTORY` (e.g. `spec-*.md`) are read-only context: reference them freely, never edit or restyle them
- The plan is **HTML-first**: produce a single self-contained `.html` document from the `## Plan Template` below
- The template uses `{{PLACEHOLDER}}` variables — replace EVERY `{{...}}` with real content. Do not leave any `{{}}` token in the final file
- Blocks marked with `<!-- repeat -->` are repeatable: duplicate them as many times as the plan needs (e.g. one block per phase, task, file, or Q&A entry) and delete the comment markers
- Keep the document self-contained: all CSS lives in the single `<style>` block; do not link external stylesheets or scripts
- Maintain a **synced visual identity** in the html styling. We want a professional, focused, minimal theme based on the original `USER_PROMPT` that created the plan. The CSS custom properties in `:root` define the palette/typography.
- Populate the metadata header (`created`, `modified`, `commits`, `branches`, `agent`, `session`, back/forward references) — these are updatable across the plan's lifecycle. Every metadata field except `CREATED_ISO` is a comma-separated list that must only ever be appended to — never overwrite or remove existing entries. `branches` records every git branch the plan was authored on or built from (`git branch --show-current`)
- If `QUESTIONABLE` is true, actively surface open questions/assumptions in the toggleable Q&A section rather than silently deciding
- Ensure the plan is detailed enough that another developer (or agent) could follow it to implement the solution
- Include code examples or pseudo-code where appropriate to clarify complex concepts
- Consider edge cases, error handling, and scalability concerns
- Save the complete plan to `PLAN_FILE` using a descriptive kebab-case filename prefixed with `plan-` (keeps planf3 output distinguishable when `PLAN_OUTPUT_DIRECTORY` is shared with other spec formats)

## Workflow

Based on the `USER_PROMPT`, select the single best-matching workflow below and read its file for the step-by-step instructions before acting.

| Workflow | When to call it | File to read |
| --- | --- | --- |
| Create Plan | The prompt asks to plan, spec, or design new work and no existing plan is referenced | `workflows/create-plan.md` |
| Update Plan | The prompt asks to change, extend, or revise the content of an existing plan | `workflows/update-plan.md` |
| Update References | The prompt asks to refresh plan metadata or back/forward references (created, modified, commits, agent, session) | `workflows/update-references.md` |
| Build Plan | The prompt asks to implement, execute, or carry out the work described in an existing plan | `workflows/build-plan.md` |

### Subworkflow

Called by other workflows, or selected directly when the `USER_PROMPT` explicitly asks for them.

| Subworkflow | When it's called | File to read |
| --- | --- | --- |
| Review Plan | Invoked by Create Plan after authoring to critique the plan and gate on a verdict; also selectable directly when the prompt asks to review, critique, or validate an existing plan | `workflows/review-plan.md` |

## Plan Template

```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Plan: {{PLAN_TITLE}}</title>
</head>
<body>
<main>

  <!-- ===== HEADER + UPDATABLE METADATA ===== -->
  <header>
    <h1>Plan: {{PLAN_TITLE}}</h1>
    <details class="meta">
      <summary>Metadata</summary>
      <dl>
        <dt>created</dt>      <dd>{{CREATED_ISO}}</dd>
        <dt>modified</dt>     <dd>{{MODIFIED_ISO_LIST}}</dd>
        <dt>commits</dt>      <dd>{{COMMIT_SHA_LIST}}</dd>
        <dt>branches</dt>     <dd>{{BRANCH_LIST}}</dd>
        <dt>agent name</dt>        <dd>{{AGENT_NAME_LIST}}</dd>
        <dt>session id</dt>      <dd>{{SESSION_ID_LIST}}</dd>
        <dt>back refs</dt>    <dd>{{BACK_REFERENCES}}</dd>
        <dt>forward refs</dt> <dd>{{FORWARD_REFERENCES}}</dd>
      </dl>
    </details>
  </header>

  <!-- ===== PURPOSE / PROBLEM / SOLUTION ===== -->
  <section id="purpose">
    <h2>Purpose</h2>
    <p>{{PURPOSE}}</p>
  </section>

  <section id="problem">
    <h2>Problem</h2>
    <p>{{PROBLEM}}</p>
  </section>

  <section id="solution">
    <h2>Solution</h2>
    <p>{{SOLUTION}}</p>
  </section>

  <!-- ===== RELEVANT FILES ===== -->
  <section id="files" class="files">
    <h2>Relevant Files</h2>

    <h3>Existing Files</h3>
    <ul>
      <!-- repeat -->
      <li><span class="tag existing">existing</span> <code>{{EXISTING_FILE_PATH}}</code> — {{WHY_RELEVANT}}</li>
    </ul>

    <h3>New Files</h3>
    <ul>
      <!-- repeat -->
      <li><span class="tag new">new</span> <code>{{NEW_FILE_PATH}}</code> — {{WHY_NEEDED}}</li>
    </ul>
  </section>

  <!-- ===== IMPLEMENTATION PHASES ===== -->
  <section id="phases">
    <h2>Implementation Phases</h2>
    <p><strong>IMPORTANT:</strong> Execute every phase and task step by step, in order, top to bottom.</p>
    <p>Status markers: <code>[]</code> idle · <code>[wip]</code> in progress · <code>[x]</code> complete · <code>[f]</code> failed. All start as <code>[]</code>; the Build Plan workflow updates them as it works.</p>

    <!-- repeat: one .phase block per phase -->
    <div class="phase">
      <h3><code class="status">[]</code> Phase {{PHASE_NUMBER}}: {{PHASE_NAME}}</h3>
      <p>{{PHASE_DESCRIPTION}}</p>

      <!-- repeat: one <h4> + checklist per task -->
      <h4>{{TASK_NUMBER}}. {{TASK_NAME}}</h4>
      <ul class="checklist">
        <!-- repeat -->
        <li><code class="status">[]</code> {{SPECIFIC_ACTION}}</li>
      </ul>

      <!-- Final task of every phase: Testing Strategy + validation loop -->
      <h4>{{LAST_TASK_NUMBER}}. Testing Strategy</h4>
      <p>{{TESTING_APPROACH: technology used to test/validate, including edge cases}}</p>
      <ul class="checklist">
        <!-- repeat -->
        <li><code class="status">[]</code> <code>{{VALIDATION_COMMAND}}</code> — {{WHAT_IT_PROVES}}</li>
      </ul>
      <div class="loop">
        🔁 <strong>Do not exit this phase until every box above is checked.</strong>
        If any command fails, fix the cause and re-run — loop until all pass.
      </div>
    </div>
  </section>

  <!-- ===== GLOBAL VALIDATION ===== -->
  <section id="validation">
    <h2>Validation Commands</h2>
    <p>Execute these commands to validate the entire plan is complete:</p>
    <ul class="checklist">
      <!-- repeat -->
      <li><code class="status">[]</code> <code>{{VALIDATION_COMMAND}}</code> — {{WHAT_IT_PROVES}}</li>
    </ul>
    <div class="loop">
      🔁 <strong>The plan is not complete until every box is checked and every command passes. If for some reason a step is not possible to complete, mark it with [f] and move on if possible.</strong>
    </div>
  </section>

  <!-- ===== QUESTIONABLES (only include this section if QUESTIONABLE is true) ===== -->
  <section id="questionables">
    <h2>Questionables</h2>
    <!-- repeat: one <details> per questionable decision / assumption / risk -->
    <details>
      <summary>{{QUESTIONABLE}}</summary>
      <p class="qa-answer">{{ASSUMPTION_OR_RATIONALE}}</p>
    </details>
  </section>

  <!-- ===== NOTES ===== -->
  <!-- Open canvas — the planning agent runs free here. There is no fixed shape:
       use whatever HTML best serves the plan (prose, lists, tables, code blocks,
       diagrams, callouts, decision logs, alternatives considered, open threads,
       links, anything). -->
  <section id="notes">
    <h2>Notes</h2>
    {{NOTES: free-form. Capture anything that helps the trifecta understand, build,
      or extend this plan — context, dependencies (new libraries via `uv add`),
      tradeoffs, rejected approaches, risks, future work, references. Author rich,
      bespoke HTML as needed.}}
  </section>

  <!-- ===== AMENDMENTS ===== -->
  <!-- Running history of changes made AFTER the plan was first executed. Append-only.
       Populated by the Update Plan and Update References workflows — never edited during Create. -->
  <section id="amendments">
    <h2>Amendments</h2>
    <!-- repeat: one entry per amendment, newest at the bottom -->
    <details>
      <summary>{{AMEND_ISO}} — {{AMEND_SUMMARY}}</summary>
      <p>{{AMEND_DETAIL: what changed and why}}</p>
    </details>
  </section>

</main>
</body>
</html>
```