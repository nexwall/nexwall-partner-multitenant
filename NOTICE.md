# NOTICE

This repository is a **direct fork** of [`NethServer/my`](https://github.com/NethServer/my)
(© Nethesis S.r.l.), full commit history preserved. See
`docs/adr/0004-fork-nethserver-my-directly.md` for why, and
`docs/adr/0001-independent-implementation-not-fork.md` for the earlier
decision it supersedes.

## Licensing of inherited code (unchanged from upstream)

- `backend/`, `collect/`, `sync/` — **AGPL-3.0-or-later**
- `frontend/` — **GPL-3.0-or-later**

Per-file SPDX headers and copyright notices are preserved exactly as
upstream wrote them and must not be stripped, including in files this
project later modifies — see `docs/adr/0004` and `CONTRIBUTING.md` for the
modification-notice requirement that applies from this point forward.

## What this means operationally (AGPL §13)

Because this is a network service incorporating AGPL-licensed code, the
complete corresponding source of **what is actually deployed** must be
available to every user who interacts with it over the network. Concretely,
ongoing (not one-time):

- This repository is kept public and kept in sync with production —
  a stale public mirror of an old commit does not satisfy this.
- A "Source code" link is present inside the running application itself,
  pointing at this repository (required before real partner traffic —
  tracked in `docs/phases/phase-0-import-and-compliance.md`).
- This is not legal advice; if you're reading this while scaling the
  Partner Program commercially, a lawyer with GPL/AGPL experience should
  review this posture — see ADR 0001 and ADR 0004's closing notes.

## Relationship to `nexwall-multi-tenant`

This service calls [`nexwall-multi-tenant`](https://github.com/nexwall/nexwall-multi-tenant)'s
Management Plane API to provision real customer infrastructure on a
separate cluster — see `docs/adr/0003-integration-with-management-plane.md`.
That repo's own licensing (GPL-3.0, inherited from `nexwall-controller`/
NethSecurity) is independent of this repo's AGPL obligations; the two
services are not combined into one work, they communicate over a network
API, so each repo's licensing stands on its own.
