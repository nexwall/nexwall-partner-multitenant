# Nexwall Partner Program

`partner.nexwall.com.br` — the Nexwall reseller/partner business layer:
partner onboarding, org hierarchy (Nexwall → Reseller → Customer, distributor
tier TBD — see `docs/adr/0002-org-hierarchy-model.md`), entitlements,
billing, and partner-facing SSO/RBAC.

**This repository is a fork of [`NethServer/my`](https://github.com/NethServer/my)**,
Nethesis's own equivalent system behind `my.nethesis.it`, forked directly
rather than reimplemented from scratch (see
`docs/adr/0004-fork-nethserver-my-directly.md`, which supersedes the
project's earlier clean-room decision in ADR 0001). Full upstream commit
history is preserved in this repo for attribution.

It is a **separate service on a separate cluster** from
[`nexwall-multi-tenant`](https://github.com/nexwall/nexwall-multi-tenant),
which owns real customer infrastructure (isolated `nexwall-controller`
stacks per customer). This repo owns partner/business data only and calls
that repo's Management Plane API to actually provision a customer's stack
— see `docs/adr/0003-integration-with-management-plane.md`. `NethServer/my`
has no equivalent of that integration; it's new code specific to Nexwall,
covered in `docs/phases/`.

## License

**AGPL-3.0-or-later** for `backend/`, `collect/`, and `sync/` (inherited
unchanged from upstream — see file headers and `NOTICE.md`).
**GPL-3.0-or-later** for `frontend/` (also inherited unchanged). Because
this is a network service incorporating AGPL-licensed components, the
*combined* service is subject to AGPL §13: complete corresponding source
of what's actually deployed must be available to everyone who interacts
with it over the network. This repo is public specifically to satisfy
that — see `NOTICE.md` for the full compliance posture and what's still
required going forward (keeping this in sync with deployments, an in-app
"Source code" link).

## 🏗️ Components (inherited from upstream, unchanged so far)

- **[frontend/](./frontend/)** — Vue.js application for UI
- **[backend/](./backend/)** — Go REST API with Logto JWT authentication and RBAC
- **[collect/](./collect/)** — Go REST API with Redis queues to handle inventories/heartbeat
- **[sync/](./sync/)** — CLI tool for RBAC configuration synchronization
- **[proxy/](./proxy/)** — nginx configuration as load balancer
- **[services/mimir/](./services/mimir/)** — Grafana Mimir, multi-tenant metrics store

## Start here

1. `docs/adr/` — read `0001` and `0004` first: why this project first chose
   clean-room reimplementation, then reversed that decision to fork
   directly, and what that reversal obligates us to do going forward.
2. `docs/phases/` — what's being adapted, in what order. This is **not**
   the same roadmap as before the fork: it's now about what to strip,
   rebrand, keep as-is, and add net-new (the Management Plane integration),
   not about building from scratch.
3. `DESIGN.md` (upstream, unchanged so far) — the original architecture
   doc; still accurate for how the inherited components work internally.

## Status

Just forked. Nothing has been adapted for Nexwall yet — running this
today would stand up Nethesis's own `my` unmodified (same default branding,
same assumption of a Nethesis Logto tenant, same Render-specific deploy
scripts pointed at Nethesis's own infrastructure). See
`docs/phases/phase-0-import-and-compliance.md` for what has to change
before this is actually usable as Nexwall's own service.

## Development setup (inherited from upstream — verify before relying on it)

Requirements per upstream: Go 1.24+, Node.js per `.nvmrc`, Make, Docker or
Podman, a Logto instance with M2M app + Management API permissions, a
Render account with GitHub integration for deploys. Several of these
assume Nethesis's own accounts/infrastructure (their Logto tenant, their
Render account) — Phase 0/1 work replaces these with Nexwall's own, see
the phase docs. Don't assume `docker-compose up` works out of the box
until that's done.

```bash
# Full local infra replica (once .env files are set up for OUR accounts,
# not copied from upstream's examples with fake values):
docker compose up
```

See upstream's original `backend/README.md`, `collect/README.md`, and
`frontend/README.md` (all inherited, unchanged) for per-component detail.

## Contributing

See `CONTRIBUTING.md` — in particular, the rule about preserving copyright/
license headers and adding modification notices to any upstream file this
project changes, which AGPL/GPL require and which matter a lot more now
that this is a direct fork rather than independent code.
