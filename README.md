# plugin-http

The `http` check verb for [opencharly/charly](https://github.com/opencharly/charly) —
an HTTP request matched against status / body / headers.

The verb issues the request from the charly host's network namespace under
`charly check live` (via `cc.HTTPDo` — in-process the engine dials, out of
process the request crosses the `CheckContextService` reverse channel and the
host dials), or from inside the disposable container via `curl` under
`charly check box` (`cc.Exec`).

## What it provides

| Capability | Surface |
|---|---|
| `verb:http` | the declarative `http:` check step any candy or box can bake into its plan |

## The verb

An authored `http: <url>` step (scalar sugar) or
`http: {http: …, status: …}` (map form). The `http`-exclusive fields live in the
plugin's own `#HttpInput` (`schema/http.cue`); the matcher evaluation reuses the
shared `sdk.MatchAll`.

| Field | Meaning |
|---|---|
| `http` | the URL to request (the verb discriminator) |
| `status` | the expected HTTP status code |
| `body` | matchers applied to the response body |
| `headers` | matchers applied to the response headers |
| `method` | the request method |
| `request_body` | the request body |
| `allow_insecure` | skip TLS verification |
| `no_follow_redirects` | do not follow redirects |
| `ca_file` | a CA bundle to trust (resolved host-side) |

Under `charly check box` only `status` and `body` are checked (the in-container
path uses `curl`); full header matching is host-side.

```yaml
- check: the service answers with 200
  id: http-ok
  http:
      http: http://localhost/health
      status: 200
  context: [runtime]
```

## How to use it

Compose the plugin candy in a box or check bed's `candy:` list:

```yaml
- '@github.com/opencharly/plugin-http/candy/plugin-http:<tag>'
```

## Layout

- `candy/plugin-http/` — the plugin module: `plugin.go`, `schema/http.cue` (the
  self-contained `#HttpInput`), `params/cue_types_gen.go`, `cmd/serve/main.go`.
- `candy/plugin-http/charly.yml` — the `plugin-http:` candy entity.
- `charly.yml` — the root project manifest (`discover: candy`).
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.

## Related

- Owning skill: `/charly-check:check` — the check verb catalog. This candy
  carries no `skill:` entity of its own; the gap is tracked in
  [opencharly/opencharly#291](https://github.com/opencharly/opencharly/issues/291).
- `/charly-internals:plugin` — the plugin/provider model.
- [`opencharly/charly`](https://github.com/opencharly/charly) — the charly CLI.
