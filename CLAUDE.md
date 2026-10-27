# Malloy model repo

A repo publishes one dataset per directory under `datasets/`, and publishes as a
unit. Connections live in `malloy-config.json`; secrets are only ever
`{ "env": … }` references.

## Two MCP servers, one default

- `malloyyo_instance` — the Malloyyo instance (`$MALLOYYO_URL`, default
  https://shearwater.malloyyo.com). **Use this by default.** The instance
  compiles with its own warehouse credentials, so nothing secret has to be in
  this environment.
- `malloyyo_author` — local `malloyyo mcp --develop`. Compiles and queries in
  this container, so it only works for non-DuckDB connections when the
  warehouse secret (e.g. `BIGQUERY_SHEARWATER_JSON`) is set here.

On the first turn, check the setup before writing anything:

1. Call `malloyyo_instance` `yo_help("authoring/start")` and follow it. If its
   authoring tools (`list_repos`, `preview_changes`) are missing, say so: either
   `MALLOYYO_TOKEN` is unset / lacks the `publish` scope, or the instance does
   not have authoring turned on.
2. `list_repos` must include this repo (`git remote get-url origin`). If it
   doesn't, an admin has to add it, or do a first
   `malloyyo publish --repo <owner/name> --create-datasets`.
3. Check whether local compiling is possible: `test -n "$BIGQUERY_SHEARWATER_JSON"`.

Loop: explore with `list_tables` / `describe_table`, edit files, then
`preview_changes` and query the preview address to check the numbers. Use
`malloyyo lint` and `malloyyo dashboard dev` only when step 3 passed (or the
dataset is DuckDB-only). Commit on a branch and open a PR; never publish or
merge without asking.
