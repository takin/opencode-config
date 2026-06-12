---
name: minia-react-parallel-task-planning
description: Use when minia-planner is planning React, React Router, TanStack Router, TanStack Start, dashboard, landing page, web frontend, or full-stack web app milestones and must define parallel-safe minia-coder task slices.
---

# Minia React Parallel Task Planning

Use this skill only during Minia planning for React-based projects. It defines how `minia-planner` should turn a React milestone into concrete, parallel-safe task slices for `minia-coder`.

## React Project Detection

Apply this skill when `PROJECT_CONTEXT`, `HIGH_LEVEL_DESCRIPTION`, `CURRENT_PLAN_MD`, or `USER_CONTEXT` identifies any of these project types or stacks:

- React
- React Router
- TanStack Router
- TanStack Start
- Web frontend
- Dashboard
- Landing page
- Full-stack web app with React UI

If the project is not React-based, ignore this skill.

## Relationship To Base Planner

This skill is additive. It extends only the base planner extension points defined by `minia-planner`:

- Required test groups.
- Allowed test layers.
- Definition of Done items.
- Task slicing heuristics.
- Parallel group naming and conflict rules.
- Framework-specific target file and target test examples.
- Framework-specific blocker conditions.

Do not duplicate, replace, or weaken the generic planner contract. Keep generic state shape, base task fields, base failure cases, base unit/E2E rules, and base output contract in `minia-planner`.

## Planner Output Extensions

When this skill applies, extend the base planner output with these React-only additions.

### Test Groups

Add one required `react-doctor` test group. This group validates that the configured React Doctor command exists in `PROJECT_CONTEXT` and will run during verification.

```text
TEST_GROUPS:
- layer: react-doctor
  required: true
  agent: minia-test-writer
  phase: test
  execution: serial
  target_tests: [<configured React Doctor command or package script from PROJECT_CONTEXT>]
  target_files: [<React source paths covered by the milestone>]
  depends_on: []
  conflicts_with: []
```

### Allowed Test Layers

Add `react-doctor` to the base allowed `test_layer` values only for React-based projects.

```text
test_layer additional value: react-doctor
```

### Definition Of Done

Append this React-only Definition of Done item:

```markdown
- [ ] React Doctor passes with no errors, warnings, notes, diagnostics, or non-clean scores
```

### React Doctor Rules

- `react-doctor` is mandatory for every React-based project.
- `react-doctor` is serial by default.
- `react-doctor` is a verification gate, not an implementation task.
- `react-doctor` does not replace unit, E2E, accessibility, visual, integration, smoke, or public-interface testing.
- Do not ask `minia-test-writer` to create artificial React Doctor test files.
- If the configured React Doctor command is missing from `PROJECT_CONTEXT`, return `STATUS: BLOCKED` with `code: MISSING_CONTEXT` and name the missing command.

## Task Readiness Criteria

A React `minia-coder` task is ready to dispatch only when all of these are true:

- It names exactly one observable behavior.
- It has one owner surface: route/page, component subtree, hook/client-state module, server function, loader/action, form/validation flow, API/client adapter, or isolated accessibility/style behavior.
- `Implementation Slice` is one focused behavior, not an area of the app.
- `Target Files` are exact project-relative production files, not wildcard paths or broad directories.
- `Target Tests` are exact project-relative test files or named configured commands, not wildcard paths or broad directories.
- `Depends On Tasks` lists only real prerequisite task ids.
- `Conflicts With` lists tasks that touch the same files, tests, generated outputs, snapshots, visual baselines, fixtures, mocks, or hidden runtime/test state.
- `On Failure.blocks` lists only dependent task ids.
- `On Failure.does_not_block` lists independent group ids that may continue.
- The task can be completed by one `minia-coder` without modifying tests, fixtures, factories, mocks, or test helpers.

If any required field is missing, broad, or unsafe, mark the task serial or return `STATUS: BLOCKED` with `code: MISSING_CONTEXT` or `TARGET_FILES_TOO_BROAD`. Do not emit a vague parallel task.

## React Task Slicing Rules

- Split implementation by independently editable UI or data-flow surface, not by vague feature name.
- Prefer task slices around one route/page module, one component subtree, one hook/client-state module, one server function/action/loader, one form/validation flow, one API/client adapter, or one isolated accessibility/style behavior.
- Each `minia-coder` task must name one observable React behavior and the exact production files likely to change.
- Each React implementation task should usually target one primary route, component, hook, server function, or adapter plus its directly owned child files.

## Shared File Classification

Treat these files and surfaces as serial by default because they commonly affect many React tasks:

- Root route files.
- Layout and app shell files.
- Router configuration.
- Generated route trees or generated router artifacts.
- App providers.
- Global CSS.
- Theme tokens.
- Package config.
- Build config.
- Test config.
- Shared test setup.
- Shared fixtures, factories, and mocks.
- Shared query clients, request clients, caches, state stores, schemas, and provider mocks.

Only mark shared-file work parallel when `PROJECT_CONTEXT` explicitly documents that the files are isolated and no selected tasks overlap on source, tests, generated outputs, or hidden state.

## Parallelization Rules

- Put independent route/page modules in separate parallel groups when they do not share parent route files, generated route trees, loaders/actions, shared components, shared hooks, or shared tests.
- Put isolated leaf components in parallel only when they do not touch the same parent/container file, shared style module, shared snapshot, visual baseline, story, fixture, or accessibility test.
- Put hook, client state, cache, mutation, and server-function tasks in parallel only when they do not share state stores, query keys, cache invalidation paths, request clients, schemas, or mocked providers.
- If two React tasks write the same `Target Files`, `Target Tests`, snapshots, visual baselines, mocks, fixtures, generated files, or hidden runtime/test state, put them in the same serial group or add explicit `Conflicts With` entries.
- If a shared foundation task is required before independent UI slices, make the shared task a serial dependency and then place independent UI slices in parallel groups after it.

## Recommended Group Shapes

- `Group ROUTES`: parallel route/page slices with disjoint route modules and tests.
- `Group COMPONENTS`: parallel isolated component or component-subtree slices with disjoint source and test files.
- `Group DATA`: parallel loaders, actions, server functions, adapters, or hooks only when data ownership and target files do not overlap.
- `Group SHARED`: serial app shell, provider, router, theme, generated-file, or test-harness changes.
- `Group INTEGRATION`: serial final wiring when earlier route, component, and data groups must converge through shared files.

## Task Templates

### Route Or Page Task

Use when one route/page owns the behavior.

Required fields:

- `Implementation Slice`: one route-visible behavior.
- `Target Files`: the route/page module and directly owned child component files only.
- `Target Tests`: the route/page unit, integration, or E2E test file.
- `Depends On Tasks`: shared loader, provider, or component tasks only when truly required.
- `Conflicts With`: tasks touching the same route, parent route, generated route tree, route test, or shared child component.

### Component Task

Use when one leaf or isolated component subtree owns the behavior.

Required fields:

- `Implementation Slice`: one render, interaction, state, or accessibility behavior.
- `Target Files`: the component file and directly owned style/helper files.
- `Target Tests`: the component test or route test that exercises the component.
- `Depends On Tasks`: parent wiring only when the component cannot be exercised independently.
- `Conflicts With`: tasks touching the same component, parent container, story, snapshot, visual baseline, or shared style module.

### Hook Or Client-State Task

Use when one hook, state module, query, or mutation owns the behavior.

Required fields:

- `Implementation Slice`: one state transition, query behavior, mutation behavior, cache behavior, or error path.
- `Target Files`: the hook/state/query module and direct type/schema dependencies.
- `Target Tests`: the hook/state test or route/component test that proves the behavior.
- `Depends On Tasks`: adapter, schema, or server function tasks only when required.
- `Conflicts With`: tasks touching the same store, query key, cache invalidation path, mocked provider, request client, or schema.

### Loader Action Or Server Function Task

Use when one route loader, action, server function, or endpoint adapter owns the behavior.

Required fields:

- `Implementation Slice`: one request, response, validation, authorization, redirect, mutation, or error-envelope behavior.
- `Target Files`: the loader/action/server-function file and direct adapter/schema files.
- `Target Tests`: unit, integration, or E2E test file proving the behavior.
- `Depends On Tasks`: schema/client/provider tasks only when required.
- `Conflicts With`: tasks touching the same loader/action, route module, adapter, schema, request client, or integration test.

### Form Validation Task

Use when one form flow owns the behavior.

Required fields:

- `Implementation Slice`: one validation, disabled/pending state, submission success, submission error, or field-level feedback behavior.
- `Target Files`: form component, validation schema, and direct submit handler files.
- `Target Tests`: component, integration, or E2E test file for that form behavior.
- `Depends On Tasks`: schema or mutation tasks only when truly required.
- `Conflicts With`: tasks touching the same form, schema, submit handler, test fixture, mock provider, or E2E journey.

## Bad To Good Examples

Bad: `Build dashboard`
Good: `Render dashboard empty state in the dashboard route when the item list is empty`

Bad: `Implement all UI`
Good: `Show invalid email validation message in the profile form before submit`

Bad: `Wire app data`
Good: `Load product detail error state from the product route loader`

Bad: `Make responsive`
Good: `Keep billing summary actions reachable below 640px in the billing summary component`

Bad: `Fix React app`
Good: `Disable invite form submit button while invite mutation is pending`

## React Parallel Safety Checklist

Before marking a React task or group `parallel`, verify:

- Target files are exact.
- Target tests are exact.
- No shared app shell files are touched.
- No generated files are touched.
- No shared fixtures, mocks, factories, or test setup are touched.
- No shared query client, cache, store, provider, schema, or request client is touched by another active or selected task.
- The task does not depend on another active or selected task.
- Conflicting tasks are explicitly listed in `Conflicts With`.
- Failure containment lists only real dependent tasks and independent groups.

If any item is uncertain, choose serial execution instead of guessing.
