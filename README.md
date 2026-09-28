# Nexwall Partner Multi-Tenant

The Nexwall Partner Program — `partner.nexwall.com.br`. The business/
account layer for Nexwall's reseller channel: partner onboarding, the
Distributor → Reseller → Customer hierarchy, entitlements, billing, and
partner-facing SSO/RBAC.

This is a **separate system on a separate cluster** from
[`nexwall-multi-tenant`](https://github.com/nexwall/nexwall-multi-tenant),
by deliberate decision — see `docs/adr/0001-independent-implementation-not-fork.md`
and `nexwall-multi-tenant`'s own `docs/adr/0006-partner-program-separate-service.md`
for the full reasoning on both sides of that split.

| Piece | What it is | Where |
|---|---|---|
| Partner Plane | New service: partner/reseller/customer hierarchy, entitlements, billing, auth | `partner-plane/` |
| Docs | Architecture, decision records, phased roadmap, API contracts | `docs/` |

## What this repo does NOT do

It does not provision Kubernetes infrastructure itself. When a partner
onboards a new end customer, this service calls **`nexwall-multi-tenant`'s**
Management Plane API (`POST /tenants`) to provision the real
`nexwall-controller` stack on that other cluster. This repo owns the
business data (who the customer belongs to, what plan they're on, whether
they're paid up); the other repo owns the infrastructure that actually runs
their firewalls.

## Relationship to `NethServer/my`

`my` (Nethesis's own equivalent system) was studied closely as a reference
architecture — its org hierarchy, entitlements model, and rebranding/
white-label support are genuinely close to what this repo needs. **No code
is copied from it.** `my`'s backend is AGPL-3.0-or-later; this repo is
GPL-3.0-only and a network service, so copying would carry real obligations
neither repo currently has. See `NOTICE.md` and
`docs/adr/0001-independent-implementation-not-fork.md`.

One structural difference worth knowing before reading `my`'s code for
ideas: `my` is comparatively passive — Nethesis's products run on
customers' own hardware, so `my` mostly records inventory/heartbeat from
units that phone home to it. **Nexwall hosts every customer's controller
stack itself**, so this repo isn't a pure bookkeeping layer like `my` — it
must actually trigger infrastructure provisioning in another system. That's
new design surface `my` never had to solve.

## Start here

1. [`docs/adr/`](docs/adr) — read before touching anything. ADR 0001 in
   particular explains the ground rule for how `my` may and may not be used
   as a reference while implementing this repo.
2. [`docs/phases/`](docs/phases) — what's being built, in what order.
3. [`docs/contracts/`](docs/contracts) — this service's own API contract,
   once written (Phase 1), plus a copy of the fields it depends on from
   `nexwall-multi-tenant`'s Management Plane contract.

## Status

Phase 0 (this scaffold). Nothing deployed yet. See
[`docs/phases/phase-0-foundations.md`](docs/phases/phase-0-foundations.md).

## License

GPL-3.0-only — see `LICENSE` and `NOTICE.md`.
