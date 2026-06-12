# OpenCode Global Configuration

This repository is the global OpenCode configuration at `~/.config/opencode`. It defines default instructions, MCP servers, local skills, and the Minia agent suite used for milestone-driven software delivery.

Restart OpenCode after changing `opencode.json`, an agent file, or a skill file. OpenCode loads these files at startup.

## Repository Layout

| Path | Purpose |
|---|---|
| `opencode.json` | Global OpenCode config, MCP servers, inline agents, and global permissions. |
| `AGENTS.md` | Global instructions loaded into sessions. |
| `agents/` | Global agent definitions. |
| `agents/README.md` | Detailed Minia workflow documentation. |
| `skills/` | Global local skill definitions. |

## Global Config

`opencode.json` currently configures:

| Area | Setting |
|---|---|
| Schema | `https://opencode.ai/config.json` |
| Permissions | Allows skill loading globally with `permission.skill.* = allow`. |
| Inline agents | Disables an inherited `README` agent and defines a `brainstorming` primary agent. |
| Experimental | Sets MCP timeout to `15000`. |
| MCP | Enables `context7` remote MCP and `brave-search` local MCP. |

The inline `brainstorming` agent is a primary planning agent. It can use tasks, but cannot edit files or run implementation shell commands.

## Global Instructions

`AGENTS.md` establishes these repository-wide rules:

| Rule | Summary |
|---|---|
| Graphify-first search | For local codebase discovery, run `graphify query "<question>"` before conventional search when Graphify is available. Fall back only when the graph is missing, stale, errors, or exact line confirmation is needed. |
| Context7 docs | For library, framework, SDK, API, CLI, and cloud-service questions, use Context7 docs before answering. |
| React SSR hydration | Avoid hydration mismatches from optional DOM attributes and keep Framer Motion-only fields out of top-level DOM props. |

This repo currently has no `graphify-out/graph.json`, so Graphify queries against this config will fail until a graph is built.

## Agents

The `agents/` directory contains the Minia Multi-Agent Loop Harness. Minia is milestone-driven: plan, write failing tests, implement the smallest production change, verify, run code/security/architecture reviews, fix findings, then hand off commit/push when requested.

| File | Agent | Mode | Role |
|---|---|---|---|
| `agents/minia.md` | `minia` | primary | General user-facing entry point. Validates project context, selects active milestone, delegates all workflow work to `minia-manager`, runs verification, and handles final commit/push handoff. |
| `agents/minia-fullstack.md` | `minia-fullstack` | primary | Full-stack web app entry point. Handles stack selection, bootstrapping, project context setup, verification handoff, and delegates milestone work to `minia-manager`. |
| `agents/minia-manager.md` | `minia-manager` | subagent, hidden | Coordinates phases, state transitions, task dispatch, verification handoff, review routing, fix loops, and escalation. Does not write code, tests, or reviews. |
| `agents/minia-planner.md` | `minia-planner` | subagent, hidden | Produces and refines milestone plans, acceptance criteria, task slices, risks, dependencies, and parallel execution groups. |
| `agents/minia-test-writer.md` | `minia-test-writer` | subagent, hidden | Writes failing tests, fixtures, factories, mocks, and harness updates for assigned test layers. Does not edit production code. |
| `agents/minia-coder.md` | `minia-coder` | subagent | Writes the smallest production implementation needed to satisfy failing tests and acceptance criteria. Does not edit tests. |
| `agents/minia-code-reviewer.md` | `minia-code-reviewer` | subagent | Reviews correctness, regressions, runtime behavior, interface contracts, test gaps, and maintainability. Read-only. |
| `agents/minia-security-reviewer.md` | `minia-security-reviewer` | subagent | Reviews exploitable behavior, privilege escalation, tenant leakage, secrets, provider/webhook boundaries, and audit gaps. Read-only. |
| `agents/minia-architecture-reviewer.md` | `minia-architecture-reviewer` | subagent | Reviews service boundaries, layering, ownership, data flow, runtime topology, scalability, and maintainability. Read-only. |

### Agent Boundaries

| Agent | Must Not Do |
|---|---|
| `minia` | Must not invoke lower-level subagents directly, write production code, write tests, write reviews, or manage the fix loop. |
| `minia-fullstack` | Must not invoke lower-level subagents directly after bootstrap. It delegates workflow execution only to `minia-manager`. |
| `minia-manager` | Must not implement code, write tests, or review code directly. |
| `minia-planner` | Must not implement code, write tests, or review. |
| `minia-test-writer` | Must not write production code. |
| `minia-coder` | Must not create or modify tests, fixtures, mocks, factories, or test helpers. |
| Reviewers | Must not edit files. |

### Minia Workflow

| Phase | Owner | Result |
|---|---|---|
| `plan` | `minia-planner` | Milestone plan, acceptance criteria, task slices, dependencies, and risks. |
| `test` | `minia-test-writer` | Failing tests for required project test layers. |
| `implement` | `minia-coder` | Production code that satisfies tests and acceptance criteria. |
| `verify` | primary agent | Runs project-defined verification commands. |
| `review` | reviewers | Parallel code, security, and architecture review. |
| `fix` | `minia-manager` routing | Targeted fixes routed to planner, test writer, or coder. |
| `commit` | primary agent | Safe commit/push handoff when requested and approved by workflow state. |
| `complete` | `minia-manager` | Milestone is complete. |
| `blocked` | user escalation | Human input is required. |

Reviewers must return `CONFIDENCE_LEVEL` and `CONFIDENCE_RATIONALE`. `STATUS: PASSED` is valid only with `CONFIDENCE_LEVEL: 5`.

## Skills

The `skills/` directory contains local global skills. Skills extend base behavior only when their trigger applies.

| Skill | File | Trigger / Purpose |
|---|---|---|
| `cua-driver` | `skills/cua-driver/SKILL.md` | Drives native macOS apps through the `cua-driver` CLI or MCP. Enforces snapshot-before-action and snapshot-after-action, avoids foreground steals, and prefers element-index actions. |
| `graphify` | `skills/graphify/SKILL.md` | Builds and queries persistent knowledge graphs for code, docs, papers, images, and video. Provides `/graphify`, `graphify query`, path tracing, reports, and exports. |
| `minia-init-ts-start` | `skills/minia-init-ts-start/SKILL.md` | Used by `minia-fullstack` when bootstrapping TanStack Start projects with Bun, Nitro, Drizzle, rolldown-vite, Oxc, React Doctor, optional Tailwind CSS v4+, optional shadcn/ui, project context, and initial milestone state. |
| `minia-react-project-context-contract` | `skills/minia-react-project-context-contract/SKILL.md` | Used by `minia-fullstack` to create, validate, or repair React-based project `AGENTS.md` context before delegation. |
| `minia-react-parallel-task-planning` | `skills/minia-react-parallel-task-planning/SKILL.md` | Used by `minia-planner` for React, React Router, TanStack Router, TanStack Start, dashboard, landing page, web frontend, and full-stack web app task slicing. |
| `minia-react-dispatch-safety` | `skills/minia-react-dispatch-safety/SKILL.md` | Used by `minia-manager` before dispatching React test, implementation, or fix slices in parallel. |

### Skill Relationships

| Skill | Loaded By | Adds |
|---|---|---|
| `minia-init-ts-start` | `minia-fullstack` | TanStack Start bootstrap recipe and post-init cleanup rules. |
| `minia-react-project-context-contract` | `minia-fullstack` | React-specific project context requirements and blocker rules. |
| `minia-react-parallel-task-planning` | `minia-planner` | React-specific task readiness, test layer, and parallel planning rules. |
| `minia-react-dispatch-safety` | `minia-manager` | React-specific dispatch preflight, overlap checks, and fix routing rules. |

## Project Context Contract

Minia expects each target project to define its own `AGENTS.md` with a `## Project Agent Context` section. That project file is the source of truth for product docs, stack docs, canonical paths, test layout, commands, Graphify policy, standards, architecture, and agent instructions.

Primary agents block and enter project-context QA when required fields are missing or malformed. Required fields include PRD, TRD, programming language, framework/project structure, deployment, milestone state path, plan path, source path, test paths, unit test path, E2E required status, verification commands, and Graphify policy.

React projects have additional required context: React Doctor command, route/page modules, components, hooks/client state, generated route artifacts policy, global styles/theme, shared test setup, and parallel planning policy.

## Graphify Policy

Minia agents that inspect active project code or docs use Graphify first unless the target project's context records `Graphify: disabled_by_user`.

Fallback to conventional discovery is allowed only when `graphify query` errors, returns no useful result, the graph is missing or stale, Graphify is not implemented for that project, or exact line-level confirmation is needed after Graphify identifies relevant files.

When a Minia primary agent reaches commit handoff and Graphify is enabled, it runs the project's knowledge graph update before git status, staging, commit, or push.

## Safety Defaults

| Area | Rule |
|---|---|
| Git | Destructive commands such as `git reset --hard`, `git checkout --`, and force-push are denied in Minia agent permissions. |
| Paths | Agents use project-relative paths in prompts, plans, docs, commits, and user-facing output. |
| Secrets | Agents must not hardcode secrets, expose credentials, or stage private files. |
| Parallel work | Planner and manager must use exact target files and tests before parallel dispatch. Broad or overlapping task slices block instead of dispatching. |
| Fix loop | Minia stops after three failed fix iterations and escalates to the user. |

## Maintenance Notes

Use file-based definitions for non-trivial agents and skills. Keep `agents/README.md` aligned with the agent suite when changing workflow contracts, and keep this root README aligned with repository-level config, local skills, and local agents.
