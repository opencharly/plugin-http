# AGENTS.md — plugin-http

Standalone plugin repo for the host-coupled `http` check verb (`verb:http`). The
plugin is a Go module at `candy/plugin-http/` (module path
`github.com/opencharly/plugin-http/candy/plugin-http`); the root `charly.yml`
only declares `discover: candy` so the repo is a project and its candy is
scanned.

Canonical files:

- `candy/plugin-http/charly.yml` — the `plugin-http:` candy entity (`plugin:`
  block, `plan:` check).
- `candy/plugin-http/` — the Go source: `plugin.go`, `schema/http.cue` (the
  self-contained `#HttpInput`), `params/cue_types_gen.go`, `cmd/serve/main.go`.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — user overview only; never agent guidance.

## Load these skills first (R0)

- `/charly-internals:plugin` — the plugin authoring reference: the `plugin:`
  block, the unified Provider model, the per-plugin CUE-schema contract,
  placement. Load before touching the provider or schema.
- `/charly-check:check` — the declarative check-step surface the `http:` verb is
  authored through (the check verb catalog).
- `/charly-internals:git-workflow` — before any git/PR action.

## Build / validate / test

- `go build ./...` in `candy/plugin-http/` — compile the plugin module.
- `go test ./...` in `candy/plugin-http/` — the plugin's Go tests.
- `charly box validate` at the repo root — the structural check (the candy +
  `plugin:` block, CUE schema).
- The merge gate is the **org-wide** `charly/pr-validator` (required check
  `validate / validate`, defined in `opencharly/.github`); this repo has **no**
  per-repo candy gate.
- The changed path is exercised by any check bed composing the `http:` verb.

## Modify this repo

- Edit the `plugin-http:` candy entity, the Go source, and `schema/http.cue`
  **together** — the schema is the single source for the verb's `params/`
  struct.
- The host and in-container paths differ by design (`cc.HTTPDo` vs `curl`);
  keep both in step when a field changes.

## Landing

- The authoritative rulebook is the umbrella `AGENTS.md` in
  `opencharly/opencharly` and `charly/AGENTS.md` in the charly repo. Load
  `/charly-internals:git-workflow` before any git/PR action; history lives in
  `CHANGELOG/`. Do not restate its rules here.
