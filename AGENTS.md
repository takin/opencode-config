<!-- context7 -->

## Local Code Search

- For any local code search, repository search, codebase discovery, architecture question, dependency tracing, symbol relationship lookup, or “where/how is X used?” question, always use `graphify query "<question>"` first when a repository has Graphify available.
- Do not use `rg`, `grep`, `find`, broad Glob searches, raw file-tree scans, or other conventional search tools as the first search path.
- Use direct file reads only when the exact file path is already known.
- Only fall back to conventional search tools when Graphify is not installed, the graph is missing or stale, `graphify query` errors, `graphify query` returns no useful results, or exact line-level confirmation is required after Graphify identifies relevant files.
- When falling back, explicitly state the Graphify failure or insufficiency reason before using the alternative search path.

<!-- context7 -->
Use Context7 MCP to fetch current documentation whenever the user asks about a library, framework, SDK, API, CLI tool, or cloud service -- even well-known ones like React, Next.js, Prisma, Express, Tailwind, Django, or Spring Boot. This includes API syntax, configuration, version migration, library-specific debugging, setup instructions, and CLI tool usage. Use even when you think you know the answer -- your training data may not reflect recent changes. Prefer this over web search for library docs.

Do not use for: refactoring, writing scripts from scratch, debugging business logic, code review, or general programming concepts.

## Steps

1. Always start with `resolve-library-id` using the library name and the user's question, unless the user provides an exact library ID in `/org/project` format
2. Pick the best match (ID format: `/org/project`) by: exact name match, description relevance, code snippet count, source reputation (High/Medium preferred), and benchmark score (higher is better). If results don't look right, try alternate names or queries (e.g., "next.js" not "nextjs", or rephrase the question). Use version-specific IDs when the user mentions a version
3. `query-docs` with the selected library ID and the user's full question (not single words)
4. If you weren't satisfied with the answer, call `query-docs` again for the same library with `researchMode: true`. This retries with sandboxed agents that git-pull the actual source repos plus a live web search, then synthesizes a fresh answer. More costly than the default
5. Answer using the fetched docs
<!-- context7 -->

## React SSR Hydration Lessons

- Avoid creating hydration mismatches with optional DOM attributes. If a server value can be `undefined`, make the client return `undefined` too, not an empty string. Example: CSP nonce helpers should normalize `el?.content || undefined` so React does not render `nonce=""` on the client when the server omitted `nonce`.
- Do not pass Framer Motion-only animation fields as top-level DOM props. Put `transitionEnd` inside the `animate` target or variant state, not directly on `motion.div`, otherwise React forwards it to the DOM and warns about an unknown prop.
