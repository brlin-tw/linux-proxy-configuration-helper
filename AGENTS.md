# AGENTS.md

This file provides guidance to AI coding agents when working with code in this
repository.

## Project overview

Helper shell scripts that configure/deconfigure forward-proxy settings across
the many independent proxy mechanisms found on a GNU/Linux desktop, from a
single text-terminal command. The scripts are meant to be **`source`d**, not
executed, so that environment-variable changes apply to the caller's shell.

## Commands

* Run all static analysis locally (mirrors CI): `pre-commit run --all-files`
    + This runs ShellCheck, markdownlint, yamllint, REUSE compliance, and
      editorconfig-checker.
* Build a release tarball: `./continuous-integration/generate-build-artifacts.sh`
  (uses `git-archive-all`; version comes from `git describe --tags`).
* There is no test suite. Validation is static analysis plus manual
  source-and-observe testing in a real terminal.

## Architecture

Three sourced files plus a config make up the runtime:

* `config.sh.source` — user-edited settings: `PROXY_HOST`, `PROXY_PORT`,
  `PRVL_ESCL_CMD`, the `NO_PROXY_HOSTS` array, and the `vscode_like_product_*`
  associative arrays. All values use `${VAR:-default}` so they can be
  overridden by the environment.
* `functions.sh.source` — shared helpers: `compose_gsettings_ignorehosts`
  (GSettings `['a','b']` syntax), `compose_kio_ignorehosts` (KIO comma list,
  strips leading `*`), `check_runtime_dependencies` (requires `jq`, `sqlite3`),
  and `query_kde_major_version` (parses `plasmashell --version`).
* `set-proxy.sh.source` / `unset-proxy.sh.source` — the two entry points. Each
  resolves its own dir via `realpath`, sources config + functions, checks deps,
  then walks every proxy target.

Both entry points apply the same set of targets in the same order, so a change
to one almost always needs the mirror change in the other:

1. Shell environment variables (`http_proxy`/`HTTP_PROXY`/… and `no_proxy`).
1. snapd (`snap set/unset system proxy.*`, needs sudo).
1. LXD (`lxc config set/unset core.proxy_*`).
1. GNOME/GSettings (`gsettings ... org.gnome.system.proxy`).
1. KDE/KIO — version-aware: picks `kwriteconfig${major}` based on the detected
   Plasma version, edits `kioslaverc`.
1. VS Code-like apps (Code, Cursor, Antigravity) — edits each app's
   `User/settings.json` via `jq` to a `.tmp` file then `mv -f`.
1. Git (`git config --global http.proxy`). Note `unset` tolerates exit code 5
   from `git config --unset` (key already absent).
1. PackageKit (unset only) — deletes rows from `/var/lib/PackageKit/transactions.db`
   via `sqlite3` and restarts the daemon, because PackageKit retains proxy
   settings even when the global proxy is disabled.

## Conventions

* **`source`d, not executed**: the scripts have no shebang and use `return`
  (never `exit`) so a failure does not close the user's terminal. Preserve this.
* Per-target failures of *desktop/service* settings emit `Warning:` to stderr
  and continue; failures of *core* operations (dependency checks, KDE version
  query, `jq`/`mv` for editor configs) emit `Error:` and `return 1`.
* Every action is announced with an `Info:` line; all diagnostics use `printf`
  (not `echo`) with explicit `1>&2` redirection for warnings/errors.
* Indentation is 4 spaces; `kwriteconfig_opts=(...)` / `*_opts=(...)` arrays are
  built then expanded as `"${opts[@]}"` — follow this pattern for new commands.
* ShellCheck runs with `external-sources=true`; suppress unavoidable findings
  with a scoped `# shellcheck disable=SCxxxx` comment as done in `config.sh.source`.
* **REUSE compliance is enforced**: every file needs an SPDX copyright +
  license header (scripts/configs are `CC-BY-SA-4.0+`; the `.sh.source` runtime
  scripts are `AGPL-3.0-or-later`). New files will fail CI without one.

## Adding a new proxy target

Add it to *both* `set-proxy.sh.source` and `unset-proxy.sh.source`, guard it
with `command -v <tool> >/dev/null` when the tool may be absent, follow the
Info/Warning/Error messaging convention above, and update the README's solution
list and References section.
