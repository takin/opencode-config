---
name: minia-react-dispatch-safety
description: Use when minia-manager dispatches React-based Minia test, implement, or fix slices and must validate parallel safety before spawning minia-test-writer or minia-coder agents.
---

# Minia React Dispatch Safety

Use this skill when coordinating task dispatch for React-based Minia projects. It defines the checks a coordinator must run before sending many task-scoped agents in parallel.

## React Project Detection

Apply this skill when `PROJECT_CONTEXT`, the configured plan, or the active milestone identifies React, React Router, TanStack Router, TanStack Start, dashboard, landing page, web frontend, or full-stack web app work.

If the project is not React-based, ignore this skill.

## Relationship To Base Coordinator

This skill is additive. It extends only React-specific dispatch safety checks for the coordinator. Do not duplicate, replace, or weaken the base coordinator dispatch algorithm, base task-scoped prompt contract, base status parsing, or base blocker shape.

Apply this skill after the base coordinator has computed candidate runnable tasks, then use the React-specific checks to remove unsafe tasks from the parallel batch or mark unclear tasks blocked.

## Dispatch Preflight

Before dispatching any React task-scoped batch, validate every selected task has:

- Exact `TASK_ID`.
- Exact `GROUP_ID`.
- Assigned `AGENT` valid for the phase.
- One focused slice description.
- Exact `TARGET_FILES`.
- Exact `TARGET_TESTS`.
- `DEPENDS_ON_TASKS`.
- `CONFLICTS_WITH`.
- `ON_FAILURE_BLOCKS`.
- `ON_FAILURE_DOES_NOT_BLOCK`.
- For `minia-test-writer`, exact `TEST_LAYER` and `TEST_GROUP_ID`.

If any field is missing, broad, malformed, or uses wildcard directories, do not dispatch that task. Mark it blocked with `code: MISSING_CONTEXT`, `TARGET_FILES_TOO_BROAD`, or `MALFORMED_OUTPUT`.

## Parallel Batch Selection

When multiple React tasks are runnable, prefer this order:

- Independent `ROUTES` tasks with disjoint route modules, tests, and route dependencies.
- Independent `COMPONENTS` tasks with disjoint component subtrees, tests, snapshots, and visual baselines.
- Independent `DATA` tasks with disjoint hooks, query keys, stores, clients, schemas, and mocks.
- One `SHARED` task only when no independent safe task is available or when it unblocks other groups.
- `INTEGRATION` tasks only after their route, component, and data dependencies are complete.

Respect `Max parallel subagents` from `PROJECT_CONTEXT`. If absent, use a conservative default of 3 task-scoped agents per batch.

## Do Not Dispatch In Parallel When

Never dispatch two React tasks in the same batch when any of these overlap or are uncertain:

- Production target files.
- Test target files.
- Parent route files.
- Generated route trees or generated router artifacts.
- Shared app shell files.
- Layout files.
- Provider files.
- Router config.
- Global CSS.
- Theme tokens.
- Package, build, test, or tooling config.
- Shared test setup.
- Snapshots.
- Visual baselines.
- Stories.
- Fixtures, factories, or mocks.
- Query keys.
- Request clients.
- Cache invalidation paths.
- Client state stores.
- Schemas.
- Provider mocks.
- E2E journeys that mutate the same user-visible state.

Queue one task and dispatch the other, or keep both in a serial group.

## Required Blocking Behavior

Block a task instead of dispatching it when:

- `TARGET_FILES` is a directory, wildcard, root source path, or generic phrase.
- `TARGET_TESTS` is missing for test or implementation work.
- The task slice says to implement a broad feature or whole page family.
- The task requires `minia-coder` to write tests, fixtures, mocks, factories, or test helpers.
- The planner omitted failure containment metadata.
- The selected task conflicts with an active task.
- The selected task depends on an incomplete task.
- The task requires project context that is absent from `PROJECT_CONTEXT`.

Use the existing Minia blocker shape with the smallest accurate code: `MISSING_CONTEXT`, `TARGET_FILES_TOO_BROAD`, `MALFORMED_OUTPUT`, `TEST_CHANGE_REQUIRED`, or `NO_PROGRESS`.

## Test Writer Dispatch

For React test tasks:

- Unit test tasks may run in parallel only when target test files and support files do not overlap.
- E2E tasks are often serial because they share browser setup, global fixtures, auth state, database state, or public journeys.
- React Doctor is serial and is a verification gate. Do not ask `minia-test-writer` to create artificial React Doctor tests.
- Accessibility and visual tasks can run in parallel only with disjoint target pages, snapshots, baselines, and fixtures.

## Coder Dispatch

For React implementation tasks:

- Dispatch exactly one `minia-coder` per task slice.
- Pass only the relevant plan excerpt, not the whole milestone when avoidable.
- Include exact tests and production target files in the prompt.
- Never merge multiple route, component, or data slices into one coder prompt.
- Never ask `minia-coder` to discover a target by reading the whole React app.
- Never ask `minia-coder` to update test files or test support files.

## Fix Dispatch

For React fix tasks:

- Map every reviewer or verification finding to the smallest existing task slice when possible.
- Create a new narrow fix slice only when no existing task owns the affected behavior.
- Route test-only issues to `minia-test-writer`.
- Route production behavior issues to `minia-coder`.
- Treat React Doctor findings as production fixes unless the diagnostic is caused by test or tooling configuration.
- Run independent fixes in parallel only when their source, tests, generated files, and hidden runtime/test state do not overlap.

## Batch Safety Checklist

Before emitting multiple task calls in the same response message, confirm:

- All selected tasks are runnable.
- No selected tasks share target files.
- No selected tasks share target tests or test support files.
- No selected tasks touch shared app shell, router, provider, global style, generated, package, build, or test harness files together.
- No selected tasks share hidden runtime or test state.
- No selected tasks depend on each other.
- No selected tasks list each other in `CONFLICTS_WITH`.
- Every selected task has failure containment metadata.

If any item is uncertain, dispatch fewer tasks or choose serial execution.
