# codex-bridge Promotion and Hardening Outcome

Date: 2026-04-01
Repo: `/Volumes/Data/_ai/_mcp/mcp_stuff/codex-bridge`
Rollback: `/Volumes/Data/_ai/_rollback/codex-bridge-promotion-20260401_121952`

## Scope

Promote the already-cloned `codex-bridge` repository from the pending MCP area into the governed MCP estate, install it using canonical `_ai` storage routing, wire it into the correct client, harden its MCP metadata and runtime hygiene, and validate the resulting install.

## Directionality Verdict

`codex-bridge` is a Python MCP stdio server that exposes the local `codex` CLI to another MCP client. It is designed for `Claude/Cursor/VS Code -> Codex`, not for Codex to consume directly.

## Canonical Paths

- Repo: `/Volumes/Data/_ai/_mcp/mcp_stuff/codex-bridge`
- Data: `/Volumes/Data/_ai/_mcp/mcp-data/codex-bridge`
- Runtime: `/Volumes/Data/_ai/_mcp/mcp-runtime/codex-bridge`
- Cache: `/Volumes/Data/_ai/_mcp/mcp-working-cache/codex-bridge`
- Managed venv: `/Volumes/Data/_ai/_mcp/mcp-working-cache/codex-bridge/.venv`
- Python bytecode cache: `/Volumes/Data/_ai/_mcp/mcp-working-cache/codex-bridge/pycache`
- External env: `/Volumes/Data/_ai/_mcp/mcp-data/codex-bridge/config/.env`

## What Changed

- Moved the git worktree from `pending_mcps/codex-bridge` into `mcp_stuff/codex-bridge` using move semantics.
- Created canonical DATA, RUNTIME, and CACHE roots.
- Installed the project into a cache-hosted Python virtualenv using editable install.
- Wired Claude Code to the governed launcher path and external env file.
- Added local capability and metadata artifacts under `docs/` and the external `meta/` bundle.
- Hardened the MCP tool metadata to expose timeout, retry, version, rate limit, error schema, observability, and auth requirements in the live tool definitions.
- Added schema validation constraints and a batch-level timeout.
- Routed Python bytecode writes out of the repo using `PYTHONPYCACHEPREFIX`.

## Files Touched

- `README.md`
- `src/mcp_server.py`
- `docs/local-capability/*`
- `docs/codex-bridge-implementation-outcome-20260401_1824.md`
- `/Volumes/Data/_ai/_mcp/mcp-data/codex-bridge/config/.env`
- `/Volumes/Data/_ai/_mcp/mcp-data/codex-bridge/meta/*`
- `~/.claude.json` via `claude mcp add`

## Validation

- `claude mcp get codex-bridge` reported `Status: Connected`.
- Live `tools/list` payload showed the expected top-level hardening metadata.
- Direct readiness analysis using `mcp-scanner`'s `ReadinessAnalyzer` returned zero findings for:
  - `consult_codex`
  - `consult_codex_with_stdin`
  - `consult_codex_batch`
- YARA scan returned no unsafe findings.
- `pip-audit` returned `No known vulnerabilities found`.
- Repo-local mutable pollution check was clean: no repo-local `.venv`, `node_modules`, `dist`, `build`, or `__pycache__`.

## Safety Verdict

No direct shell-injection path was found. The bridge uses subprocess argument lists with `shell=False`. The main residual risk is trust-boundary related: any trusted MCP client connected to this server can ask it to invoke Codex prompts against local content.

## Caveat

One external-tool caveat remains: the `mcp-scanner` CLI summary still reports a bogus “has no description” finding even though the live tool payload contains descriptions and the scanner’s own readiness analyzer returns zero findings when run directly. That looks like a scanner CLI/reporting bug, not a `codex-bridge` runtime issue.

## Outcome

The governed promotion, install, client wiring, hardening, and direct validation all succeeded. The managed repo is ready under the local `_ai` conventions.
