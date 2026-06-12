---
name: minia-init-ts-start
description: Use ONLY when minia-fullstack initializes or bootstraps a TanStack Start project with Bun, Nitro, Drizzle, rolldown-vite, Oxc tooling, mandatory React Doctor diagnostics, optional Tailwind CSS v4+, optional shadcn/ui, AGENTS.md context, and initial milestone state.
---

# Minia Tanstack Start Bootstrap

Use this skill for `PROJECT_BOOTSTRAP` in `minia-fullstack`: creating a new TanStack Start full-stack app, initializing an empty target folder, applying the approved scaffold cleanup, creating project context, and bootstrapping the initial Minia milestone state.

For ongoing TanStack Start implementation patterns after scaffold, use `tanstack-start-best-practices`. For shadcn component work after bootstrap and preset apply, use `shadcn`.

## Approved Stack Guidance

For TanStack Start initialization, follow this user-approved external tech-stack guidance source:

```text
~/Knowledges/wiki/engineering/tech-stacks/web-tanstack-start.md
```

Use it as the source of truth for scaffold conventions, project structure, scripts, runtime expectations, and stack-specific setup unless the active project's `AGENTS.md` or the user explicitly overrides it.

Because this is an approved external standards source, it may be referenced in `AGENTS.md`. When invoking `minia-fullstack-manager` or lower-level agents later, pass the exact standards path and/or a short relevant excerpt in `PROJECT_CONTEXT` / `ENGINEERING_STANDARDS`; do not pass broad wildcard directories.

## Default Stack Profile

Use this as the default stack only when the user asks for a TanStack Start full-stack web app, the tech-stack guidance above does not specify a conflicting choice, and the user does not specify a conflicting stack.

- Runtime/package manager: Bun.
- Framework: TanStack Start.
- Deployment target: Nitro.
- Toolchain: rolldown-vite + Oxc + React Doctor. Use `vite` commands backed by `rolldown-vite`, `oxlint` for linting, `oxfmt` for formatting, `tsc --noEmit` for type checking, and React Doctor for mandatory React diagnostics.
- Bundler: Rolldown. The `vite` package must be overridden/aliased in `package.json` to `npm:rolldown-vite@latest`; do not use default Rollup-based Vite or add direct `rollup` dependencies.
- Forbidden React toolchains: ESLint and Biome. Do not create, keep, or use ESLint/Biome config, dependencies, or scripts in ReactJS-based projects, including TanStack Start, TanStack Router, and React Router projects.
- TanStack add-ons: Nitro, Table, Store, Form, Compiler, Drizzle.
- UI styling system: Tailwind CSS and shadcn/ui are optional and must be confirmed with the human during bootstrap.
- Tailwind CSS: if used, require Tailwind CSS v4+.
- shadcn/ui: if used, ask for the registry preset, present `b69E9aRWLa` as the recommended default, and apply `b69E9aRWLa` when the human agrees or leaves the preset answer blank.
- No shadcn/ui: always ask for design tokens or a project-relative design-token source path before continuing.
- Tests: unit tests and React Doctor are mandatory; E2E/system tests are mandatory for full-stack web apps unless the user explicitly changes the project type to one where E2E does not apply.
- Database: Drizzle is included by default; treat database/persistence standards as required unless the user explicitly requests a no-persistence app and approves removing or changing the Drizzle preset.

## Bootstrap Preflight

Before running scaffold commands:

1. Confirm the project directory name if unclear.
2. Confirm whether to scaffold into the current directory or a new child directory.
3. Confirm that Bun is available or run `bun --version`.
4. Ask before overwriting any non-empty target directory.
5. Ask for missing product context needed to create `AGENTS.md`:
   - Product name.
   - Project type.
   - PRD path or permission to create a placeholder.
   - TRD path or permission to create a placeholder.
   - UI/UX design path or permission to create a placeholder.
   - Whether to use Tailwind CSS. If yes, require Tailwind CSS v4+.
   - Whether to use shadcn/ui. If yes, ask for the registry preset, present `b69E9aRWLa` as the default, and use `b69E9aRWLa` if the user agrees or leaves the preset blank. If no, always ask the user to enter design tokens or provide the project-relative design-token source path.
   - Database/persistence expectation.
   - Deployment target.
   - Project-specific constraints.
   - Initial milestone definitions.

For TanStack Start stack docs during bootstrap, use `~/Knowledges/wiki/engineering/tech-stacks/web-tanstack-start.md` by default. If the project requires project-local standards docs, create project-relative excerpts or references under the project docs/standards location defined in `AGENTS.md`. Do not pass wildcard external directories to subagents.

## Scaffold Command

Before running the scaffold command, consult `~/Knowledges/wiki/engineering/tech-stacks/web-tanstack-start.md` and prefer its current scaffold recipe when it differs from this fallback command. Do not fetch external TanStack Start or shadcn CLI documentation during Minia bootstrap unless the user explicitly asks. When `web-tanstack-start` conflicts with this skill, this skill wins for Minia bootstrap. Never follow any React project instruction that selects ESLint, Biome, default Rollup-based Vite, or `bunx --bun shadcn@latest init *` after the TanStack Start scaffold already exists.

Fallback command, used only when the guidance source does not define a more specific command. Run from the parent directory when creating a new child project:

```bash
bunx @tanstack/cli@latest create --no-examples \
  --add-ons=nitro,table,store,form,compiler,drizzle \
  --package-manager=bun \
  --deployment=nitro \
  <project_name>
```

If scaffolding into the current directory, adapt only as supported by the current TanStack CLI. If the command fails because CLI flags changed:

1. Run `bunx @tanstack/cli@latest create --help`.
2. Align flags with the current help output.
3. Retry with the same intent: Bun, TanStack Start, Nitro deployment, rolldown-vite + Oxc tooling, TanStack Table/Store/Form/Compiler, and Drizzle unless the user opted out.
4. If the CLI requires a toolchain choice, choose a non-ESLint, non-Biome, and non-default-Rollup option. If no suitable option exists, scaffold without the toolchain flag or use the closest minimal option, then remove generated ESLint/Biome artifacts and force `vite` to `npm:rolldown-vite@latest` during the toolchain normalization step.

## Required Post-Init Steps

After the TanStack CLI scaffold succeeds, run these steps in order from the project root.

### 1. Optional Tailwind CSS v4+ And shadcn Preset Apply

Before running UI setup commands, ask:

1. Should this project use Tailwind CSS? If yes, use Tailwind CSS v4+ only.
2. Should this project use shadcn/ui? If yes, which shadcn registry preset should be used? Present `b69E9aRWLa` as the recommended default and use `b69E9aRWLa` when the user agrees or leaves the answer blank.
3. If the user declines shadcn/ui, ask them to enter design tokens or provide the project-relative design-token source path. Do not continue UI setup or `AGENTS.md` completion without this design-token answer.

If the user chooses shadcn/ui, require Tailwind CSS v4+ as part of UI setup unless the current shadcn CLI/preset explicitly manages the required Tailwind v4+ setup. Use this command from the project root, replacing `<preset>` with the selected preset:

```bash
bunx --bun shadcn@latest apply --preset <preset>
```

Default selected command when the user accepts the default or leaves the preset blank:

```bash
bunx --bun shadcn@latest apply --preset b69E9aRWLa
```

Rules:

- Do not apply a shadcn preset when the user declines shadcn/ui.
- After `bunx @tanstack/cli@latest create` succeeds, the project is already scaffolded. For Minia bootstrap, never run `bunx --bun shadcn@latest init *` in that post-scaffold project.
- For Minia bootstrap shadcn/ui setup, the only approved preset command is `bunx --bun shadcn@latest apply --preset <preset>`.
- Ignore stale post-scaffold guidance from tech-stack docs, shadcn examples, or generic component skills that says to use `init --preset`, `--template start`, or `shadcn init` for this step.
- When the user declines shadcn/ui, require a design-token answer and record it in `AGENTS.md` before continuing.
- Do not add Tailwind CSS when the user declines Tailwind CSS and shadcn/ui.
- If Tailwind CSS is used, ensure the resulting project uses Tailwind CSS v4+; do not install or pin Tailwind v3.
- Do not substitute a non-default shadcn preset unless the user asks.
- If the CLI fails due to flag drift, run `bunx --bun shadcn@latest apply --help` and retry only after understanding the current CLI output.

### 2. Route Trim

Under `src/routes/`, delete every `*.tsx` file that is not one of:

- `src/routes/__root.tsx`
- `src/routes/index.tsx`

Apply recursively. Remove empty directories left under `src/routes/`. Do not delete generated route artifacts such as `routeTree.gen.ts`.

After trimming routes, run the route/codegen path used by the generated project. Prefer generated `package.json` scripts. If unclear, run the dev script with a timeout only after inspecting `package.json`.

### 3. Shell Cleanup

Apply these cleanup rules once after route trim.

In `src/routes/index.tsx`:

- Ensure the route renders a `<main>` element.
- Ensure `<main>` contains a child with exact text: `<div>Hello Agent</div>`.
- Keep existing `<main>` attributes when safe.

Delete these template/demo files if they exist:

- `src/components/Header.tsx`
- `src/components/Footer.tsx`
- `src/components/demo.FormComponent.tsx`
- `src/data/demo-table-data.ts`
- `src/hooks/demo.form-context.ts`
- `src/hooks/demo.form.ts`
- `src/lib/demo-store-devtools.tsx`
- `src/lib/demo-store.ts`

For `src/lib/utils.ts`, delete it only if it is demo-only. Keep it if shadcn/ui is enabled and it contains the shadcn `cn` helper used by UI components.

In `src/routes/__root.tsx`:

- Remove `Header` and `Footer` imports.
- Remove `<Header />` and `<Footer />` JSX usages.
- Remove wrappers that only existed for template chrome.
- Ensure the root route still renders the route outlet pattern used by the template.

Then search for remaining references to `Header`, `Footer`, `demo.FormComponent`, `FormComponent`, and deleted demo store/form/data modules. Remove orphaned imports/usages.

In `src/styles.css`:

- Remove default TanStack/template showcase CSS.
- If Tailwind CSS or shadcn/ui is enabled, keep Tailwind v4+ setup, shadcn/base styles, tokens, layers, utilities, dark-mode variables, and minimal global resets required by shadcn.
- If Tailwind CSS and shadcn/ui are both declined, do not introduce Tailwind/shadcn CSS; keep only minimal app-global CSS required by the generated project and document the user-provided design tokens as the source of truth.

### 4. Rolldown-Vite And Oxc Toolchain Normalization

All ReactJS-based projects must use rolldown-vite + Oxc + TypeScript type checking + React Doctor. This includes TanStack Start, TanStack Router, React Router, and any other React stack profile.

Required tooling:

- Dev/build/preview: `vite` commands backed by `rolldown-vite`.
- Bundler: Rolldown through `rolldown-vite`.
- Lint: `oxlint`.
- Format: `oxfmt`.
- Typecheck: `tsc --noEmit`.
- React diagnostics: `react-doctor` with a full non-interactive scan that fails on warnings.

Forbidden tooling:

- ESLint.
- Biome.
- Default Rollup-based Vite.
- Direct `rollup` dependencies.

After scaffold, inspect `package.json` and normalize tooling before continuing:

1. Install required dev dependencies when missing. Always alias `vite` to `rolldown-vite`:

```bash
bun add -D vite@npm:rolldown-vite@latest oxlint oxfmt typescript react-doctor@latest
```

2. Remove ESLint and Biome config files if they exist:
   - `.eslintrc`
   - `.eslintrc.*`
   - `eslint.config.*`
   - `biome.json`
   - `biome.jsonc`

3. Remove forbidden dependencies from `package.json` when present:
   - `eslint`
   - `@eslint/*`
   - `typescript-eslint`
   - `@typescript-eslint/*`
   - `eslint-plugin-*`
   - `eslint-config-*`
   - `biome`
   - `@biomejs/biome`
   - `rollup`

4. Override Vite in `package.json` so all framework wrappers resolve to `rolldown-vite`, and keep React tooling as mandatory dev dependencies. Required package fields:

```json
{
  "devDependencies": {
    "vite": "npm:rolldown-vite@latest",
    "oxlint": "latest",
    "oxfmt": "latest",
    "typescript": "latest",
    "react-doctor": "latest"
  },
  "overrides": {
    "vite": "npm:rolldown-vite@latest"
  }
}
```

If the generated template puts `vite` in `dependencies` instead of `devDependencies`, replace that entry with `"vite": "npm:rolldown-vite@latest"` in the same dependency section and still keep the top-level `overrides.vite` entry. `oxlint`, `oxfmt`, `typescript`, and `react-doctor` must remain in `devDependencies`.

5. Replace any scripts that invoke ESLint or Biome. Required scripts:

```json
{
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "preview": "vite preview",
    "lint": "oxlint",
    "lint:fix": "oxlint --fix",
    "format": "oxfmt",
    "format:check": "oxfmt --check",
    "typecheck": "tsc --noEmit",
    "doctor": "react-doctor . --yes --full --offline --fail-on warning --json",
    "test:react-doctor": "react-doctor . --yes --full --offline --fail-on warning --json"
  }
}
```

For TanStack Start templates where the generated dev/build scripts wrap Vite through TanStack Start commands, keep the generated framework-required dev/build scripts only when replacing them with raw `vite` would break the app. Even then, package resolution must force `vite` to `npm:rolldown-vite@latest`; lint, format, format check, typecheck, and React Doctor must use `oxlint`, `oxfmt`, `oxfmt --check`, `tsc --noEmit`, and `react-doctor . --yes --full --offline --fail-on warning --json`; and ESLint/Biome/default Rollup-based Vite must remain absent.

### 5. Verify Generated Scripts

Inspect `package.json` before inventing commands.

Prefer project scripts for install, format, lint, typecheck, unit tests, React Doctor, E2E tests, build, and dev. Dev/build must resolve `vite` to `rolldown-vite`; format must use `oxfmt`; lint must use `oxlint`; typecheck must use `tsc --noEmit`; React Doctor must run a full non-interactive scan and fail on warnings. Run the most relevant generated checks in a bounded way. For dev server checks, always use a timeout and do not leave watchers running indefinitely.

React Doctor output must be clean before bootstrap is considered successful: no errors, no warnings, no notes, and no diagnostics. The `--fail-on warning` flag is required, but still inspect text or JSON output for notes/advisory diagnostics and fix them before continuing.

## AGENTS.md For New Apps

Create or update `AGENTS.md` during bootstrap. It must contain a valid `## Project Agent Context` section before normal Minia workflow or `minia-fullstack-manager` invocation.

Required content:

- Project type.
- Product docs source paths: PRD, TRD, UI/UX Design.
- Tech stack docs source paths: programming language, framework/project structure, database, deployment.
- Project-specific constraints.
- Canonical paths for milestone state, plans, source, and tests.
- Test layout: unit tests path, E2E tests path, `E2E required: true | false`, React Doctor layer, integration tests path or `none`, additional test layers or `none`.
- Verification commands for format, lint, typecheck, React Doctor, and test, or explicit `none` for commands that do not apply. For ReactJS-based projects, dev/build must resolve `vite` to `rolldown-vite`, format must use `oxfmt`, lint must use `oxlint`, typecheck must use `tsc --noEmit`, and React Doctor must use a full non-interactive scan that fails on warnings; ESLint, Biome, and default Rollup-based Vite are forbidden.
- TanStack Start stack profile or standards reference.
- Package manager.
- Runtime/deployment target.
- Tailwind CSS decision and version requirement when Tailwind is used.
- shadcn/ui decision and preset/template when shadcn is used.
- Design tokens or a project-relative design-token source path when shadcn/ui is not used.

Treat `AGENTS.md` as malformed when any required field is absent, blank, ambiguous, or uses a disallowed `none`; when required project-local paths are absolute; when React Doctor is missing for a React project; when shadcn/ui is disabled without design tokens; when Tailwind is enabled without Tailwind CSS v4+ requirement; or when E2E is disabled for a full-stack web app without explicit user-approved reason and a stronger alternate public-interface layer.

## Suggested AGENTS.md Defaults

When the user approves defaults during bootstrap, prefer this project-relative shape, adjusting only after inspecting the generated project:

```markdown
## Project Agent Context

### Project Identity
- Project type: full-stack web app
- Product name: <user-provided>
- Repository name: <project-directory-name>
- Organization/team: <user-provided or none>
- Primary domain terms: <user-provided or none>

### Product Docs
- PRD: docs/product/prd.md
- TRD: docs/product/trd.md
- UI/UX Design: docs/product/ui-ux.md

### Tech Stack Docs
- Programming language: docs/standards/typescript.md
- Framework/project structure: ~/Knowledges/wiki/engineering/tech-stacks/web-tanstack-start.md
- Database: docs/standards/drizzle.md
- Deployment: docs/standards/nitro.md

### Project Constraints
- Constraint: Use Bun as package manager/runtime for project scripts.
- Constraint: Use TanStack Start with Nitro deployment.
- Constraint: Use rolldown-vite + Oxc for all ReactJS-based tooling. ESLint, Biome, and default Rollup-based Vite are forbidden.
- Constraint: Override `vite` in package.json to `npm:rolldown-vite@latest`; do not add direct Rollup dependencies.
- Constraint: Use oxlint for linting, oxfmt for formatting, and tsc --noEmit for type checking.
- Constraint: Use React Doctor as a mandatory React diagnostics gate. React Doctor must pass with no errors, warnings, notes, or diagnostics before review, commit, or completion.
- Constraint: Tailwind CSS is optional; if enabled, use Tailwind CSS v4+.
- Constraint: shadcn/ui is optional; if enabled, apply the user-selected preset or default preset b69E9aRWLa. If disabled, use the user-provided design tokens as the design source of truth.
- Constraint: Keep paths project-relative in agent output.
- Constraint: Do not introduce additional frameworks or package managers without user approval.

### Canonical Paths
- Milestone state: docs/minia/milestones.md
- Plans: docs/minia/plans
- Source: src
- Tests: tests
- Test helpers: tests/helpers
- Docs: docs
- Scripts: scripts
- Migrations: drizzle
- Interface entrypoints: src/routes
- UI entrypoints: src/routes
- Config: .
- Architecture notes: docs/architecture

### Test Layout
- Unit tests: tests/unit
- E2E tests: tests/e2e
- E2E required: true
- React Doctor: package script `test:react-doctor`
- Integration tests: tests/integration
- Additional test layers: visual, accessibility, smoke

### Commands
- Install: bun install
- Format: bun run format
- Format check: bun run format:check
- Lint: bun run lint
- Typecheck: bun run typecheck
- React Doctor: bun run test:react-doctor
- Unit tests: <infer from package.json or none>
- Integration tests: <infer from package.json or none>
- E2E tests: <infer from package.json or none>
- Build: <infer from package.json or none>
- Security/dependency audit: <infer from package.json or none>
- Knowledge graph update: none

### Standards And Architecture
- Stack profile: TanStack Start + Bun + Nitro + Drizzle + rolldown-vite + Oxc + tsc --noEmit + React Doctor + optional Tailwind CSS v4+ + optional shadcn/ui
- Standards docs: ~/Knowledges/wiki/engineering/tech-stacks/web-tanstack-start.md
- Tenant model: <user-provided or none>
- Error/response contract: <user-provided or none>
- Provider integrations: <user-provided or none>
- Runtime topology: Nitro server runtime with TanStack Start
- Infrastructure boundaries: <user-provided or none>
- Package manager: Bun
- Toolchain: rolldown-vite + Oxc + React Doctor; ESLint, Biome, and default Rollup-based Vite forbidden
- Vite package override: vite -> npm:rolldown-vite@latest
- Lint: oxlint
- Format: oxfmt
- Typecheck: tsc --noEmit
- React Doctor: react-doctor . --yes --full --offline --fail-on warning --json
- Tailwind CSS: <enabled with v4+ | disabled>
- shadcn/ui: <enabled | disabled>
- shadcn preset: <b69E9aRWLa when enabled by default, user-selected preset when provided, or none>
- shadcn apply command: <bunx --bun shadcn@latest apply --preset b69E9aRWLa when enabled by default, user-selected preset command when provided, or none>
- Design tokens: <project-relative source path or inline token summary when shadcn/ui is disabled; none only when shadcn/ui is enabled>

### Agent Instructions
- Max parallel subagents: 3
- Use only relative paths in user-facing output.
- Do not mention local machine paths.
- Treat this file as the source of truth for project-specific names and locations.
- If a path/name/command is not listed here, ask before introducing a new convention.
```

Do not leave placeholder angle-bracket values in `AGENTS.md`. Ask the user, infer safely from project structure, or write `none` only for fields where `none` is allowed.

## Milestone State Bootstrap

If the configured milestone state file does not exist:

1. Ask the user for milestone definitions if they were not already provided.
2. Create the configured milestone state file.
3. Include an `Orchestrator State` subsection for the active milestone.

Use this template:

```markdown
# Minia Milestones

## Milestone <id>: <title>

- status: active
- summary: <short summary>
- acceptance:
  - <testable acceptance criterion>
- notes:
  - <optional notes>

### Orchestrator State
- phase: plan
- iteration: 0
- last_actor: minia-fullstack
- pending_agents: []
- completed_agents: []
- active_groups: []
- completed_groups: []
- blocked_groups: []
- active_tasks: []
- completed_tasks: []
- blocked_tasks: []
- findings: []
- blockers: []
- verification_output: {}
```

## Bootstrap Completion Summary

After scaffold, summarize:

- Created project path.
- Stack profile.
- Tailwind CSS and shadcn/ui decisions, including selected shadcn preset when enabled or design-token source when shadcn/ui is disabled.
- Post-init cleanup performed.
- Verification commands run.
- React Doctor result, including confirmation that there were no errors, warnings, notes, or diagnostics.
- Next milestone status.
