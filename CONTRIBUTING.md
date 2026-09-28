# Contributing

This repo is currently maintained solely through Claude, by explicit
agreement — see the project owner before making manual changes, to avoid
conflicting edits.

## The one rule that matters most in this repo

**Do not have `NethServer/my`'s source open while writing code here.**
Read `docs/adr/0001-independent-implementation-not-fork.md`,
`dev-nethsec-reference/multi-tenant-design/my-nethesis-reference.md`, and
this repo's own phase docs instead, and implement from those. This isn't
bureaucracy — `my`'s backend is AGPL-3.0-or-later, this repo is GPL-3.0-only
and a network service, and the two licenses have genuinely different
consequences if code crosses over. See ADR 0001 for the full reasoning.

## Where things belong

| Change | Goes in |
|---|---|
| Auth / org hierarchy / visibility logic | `partner-plane/internal/` |
| A new architectural decision | A new numbered ADR in `docs/adr/`, never edit a past one — supersede it |
| API surface change | `docs/contracts/*.yaml` **first**, then the handler — contract is the source of truth |
| Anything calling `nexwall-multi-tenant`'s Management Plane | Check `docs/adr/0003-integration-with-management-plane.md` first — that contract lives in the *other* repo; don't assume its shape, read it there |

## Before opening a PR

1. `cd partner-plane && go vet ./... && go test ./...`
2. If the change touches an ADR's "Consequences" section materially, flag
   it in the PR description.
3. If the change was informed by reading `my`'s code or docs, say so
   explicitly in the PR description, and confirm it's pattern-level, not
   copied — this is worth over-documenting given the license stakes.
