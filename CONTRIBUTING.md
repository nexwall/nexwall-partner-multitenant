# Contributing

This repo is currently maintained solely through Claude, by explicit
agreement — see the project owner before making manual changes, to avoid
conflicting edits.

## The one rule that matters most in this repo (this changed — read ADR 0004)

This repository **is** a direct fork of `NethServer/my`
(`docs/adr/0004-fork-nethserver-my-directly.md`). Work in the inherited
code directly — the earlier "don't have their source open" rule (ADR 0001)
no longer applies. What applies instead, because this is now a real AGPL
compliance posture, not a hypothetical one:

1. **Never strip a copyright notice or SPDX header**, including in files
   you're heavily modifying for rebranding or new features.
2. **Every file you modify needs a modification notice** — a comment near
   the top: `// Modified by Nexwall, <date>: <one-line summary>`. Add it in
   the same commit as the change. This is a real AGPL/GPL requirement
   (§5), not a style preference.
3. **Keep this repo in sync with what's actually deployed.** The AGPL
   obligation this project accepted (ADR 0004) is only satisfied if the
   public source here matches production, not an old snapshot.
4. New net-new files (not modifying an inherited file, e.g. the future
   Management Plane integration client) don't need Nethesis's copyright
   header, obviously — but should carry Nexwall's own SPDX header
   (`AGPL-3.0-or-later` for anything living in `backend/`/`collect/`, to
   stay consistent with the license already governing that directory).

## Where things belong

| Change | Goes in |
|---|---|
| Auth / org hierarchy / entitlements (inherited, being adapted) | `backend/`, per upstream's existing structure |
| Inventory/heartbeat collection (inherited, being adapted) | `collect/` |
| Anything calling `nexwall-multi-tenant`'s Management Plane (net-new, no upstream equivalent) | New package under `backend/` or `collect/` — see `docs/adr/0003-integration-with-management-plane.md` for the contract, which lives in *that* repo |
| A new architectural decision | A new numbered ADR in `docs/adr/`, never edit a past one — supersede it (see how ADR 0004 supersedes ADR 0001) |
| API surface change | Upstream's `backend/openapi.yaml` — update it as the source of truth, then the handler |

## Before opening a PR

1. Follow upstream's own per-component `make pre-commit` / `npm run
   pre-commit` conventions (see each component's own `README.md`, inherited
   unchanged so far).
2. `./vuln-check.sh --all` per upstream convention.
3. If the change touches an ADR's "Consequences" section materially, flag
   it in the PR description.
4. If you modified an inherited file, confirm the modification notice (rule
   2 above) is actually present — easy to forget mid-refactor.
