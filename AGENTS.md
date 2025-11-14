# Repository Guidelines

## Project Structure & Module Organization
`server.js` hosts the entire MCP HTTP server, registering the todo tools and storing transient state in module-level arrays. Widget HTML assets belong in `public/` (currently `todo-widget.html`). Container artifacts live in `docker/server/`, while runtime overrides sit in `.env` and load through `process.env`. There is no client bundler; restart Node after edits.

## Build, Test, and Development Commands
- `npm install` — installs the two runtime dependencies and mirrors the Docker image, so keep the tree lean.
- `npm run dev` — starts `node server.js` on `PORT` (default 8787) for local verification; watch stdout for the MCP URL.
- `npm start` — identical entry point reserved for Fly.io or other production runners.
- `docker build -f docker/server/Dockerfile -t todo-mcp .` then `docker run -p 9000:9000 -e PORT=9000 todo-mcp` — reproducible container workflow if you need parity with staging.

## Coding Style & Naming Conventions
The repo is ESM-first (`"type": "module"`), so always use `import`/`export`. Keep indentation at two spaces, prefer double quotes, and default to `const` for references. Variables and functions stay camelCase, env vars SCREAMING_SNAKE, and MCP tool identifiers snake_case to match the SDK. Run `npx prettier server.js public/*.html` before committing, and define each Zod `inputSchema` adjacent to the tool that uses it.

## Testing Guidelines
No automated suite exists yet, so pair every feature with coverage. Use Node’s built-in `node --test` runner or `vitest` to exercise the `/mcp` transport and widget schema logic. Place tests near their targets (e.g., `server.test.js`) and add an `npm test` script once the harness lands so CI can run it consistently. Name specs after observable behavior (“complete_todo rejects unknown ids”) and keep shared fixtures beside the HTML they exercise.

## Commit & Pull Request Guidelines
With only one historical commit, continue using concise, imperative subject lines (“Add widget validation guard”). Document risky behavior changes in the commit body. PRs should link their issue, summarize user impact, list manual verification (sample curl payloads or widget screenshots), and call out any new env vars or Fly configuration edits.

## Security & Configuration Tips
Only `/mcp` should be reachable; the root handler is plain text for uptime checks. Keep `.env` secrets out of git and rely on Fly secrets or Docker `-e` flags in production. Because CORS is wide open (`*`), validate every new tool input via strict Zod schemas and document port changes in `docker/server/Dockerfile` before deployment.
