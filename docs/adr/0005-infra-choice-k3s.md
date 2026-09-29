# ADR 0005: Infra target is k3s, not Render/Docker Compose

**Status**: Accepted. Resolves Phase 0.5's open question.

## Context

Upstream `NethServer/my` deploys via Render.com Blueprint
(`render.yaml`) + Docker Compose semantics. `nexwall-multi-tenant` runs
k3s. Phase 0.5 flagged this as needing an explicit decision before
`render.yaml`'s Nethesis-specific values could be triaged.

## Decision

**k3s**, on its own cluster, separate from `nexwall-multi-tenant`'s cluster
per ADR 0006 in that repo. One less infra pattern to operate long-term
(k3s knowledge, tooling, and `infra/k3s/install-master.sh`-style scripts
already exist and are proven) outweighs the one-time cost of translating
`render.yaml`'s service definitions into k8s manifests/Helm.

## Consequences

- `render.yaml`, `docker-compose.yml`, `deploy.sh`, `release.sh` (the
  Render-image-tag-bump-and-push script) become reference material for
  what each service needs (env vars, ports, dependencies between services)
  during translation, not files this project runs directly. Don't delete
  them yet — keep them until the k8s manifests are verified working, then
  they can be archived or removed in a later, deliberate cleanup commit.
- A new `charts/` directory (mirroring `nexwall-multi-tenant`'s
  `charts/nexwall-controller/` structure and README-mapping-table
  convention) is the concrete Phase 1 deliverable this decision implies —
  see the updated Phase 1 doc.
- The Render-specific GitHub Actions workflows (PR previews, redirect-URI
  automation, `release-production.yml`'s Render deploy step) are dead
  weight under this decision, not just "adapt later" — they assume a
  target (Render) this project no longer uses at all. Phase 0.2's earlier
  "disable or adapt" framing is resolved: disable them, with the
  explanatory comment already planned, rather than adapting them to a
  target we're not using.
- `services/mimir/`'s own `docker-compose.yml` (a separate, smaller compose
  file just for Mimir) needs the same translation treatment — check
  whether it warrants its own chart or folds into the main one.
