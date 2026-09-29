# Phase 2 — Management Plane integration (net-new) + entitlements adaptation

**Status**: Not started.
**Goal**: onboarding a customer here provisions a real, reachable
`nexwall-controller` stack on `nexwall-multi-tenant`'s cluster.

This phase is the one place where "fork vs. reimplement" made no
difference at all — `my` has zero code for provisioning Kubernetes
infrastructure, so this is exactly as new as it would have been under the
old (pre-ADR-0004) plan.

## 2.1 — Management Plane client (net-new, no upstream equivalent)

- **Build new**, per `docs/adr/0003-integration-with-management-plane.md`:
  a client using a dedicated `PARTNER_API_KEY`, calling `POST /tenants`,
  `GET /tenants/:id/status`, `POST /tenants/:id/suspend`,
  `DELETE /tenants/:id` against `nexwall-multi-tenant`'s
  `docs/contracts/management-plane-openapi.yaml`.
- Since this is new code living inside an otherwise-inherited `backend/`
  tree, give it Nexwall's own AGPL-3.0-or-later SPDX header (consistent
  with the directory's existing license) but **not** a Nethesis copyright
  line — it's not their code (CONTRIBUTING.md's distinction between
  "modifying an inherited file" and "adding a new file").
- **Build new**: the `provisioning_failed` state and partner-facing retry
  affordance from ADR 0003.

## 2.2 — Entitlements adaptation

- **Reimplement/adapt** (working code, `backend/methods/entitlements.go`
  and related `local_system_entitlements*.go`): these currently model
  Nethesis's own product SKUs (NethServer/NethSecurity subscription tiers).
  Adapt the plan/product definitions to Nexwall's own offering — the
  underlying entitlement-tracking mechanism (tied into the same org/system
  schema, not a separate license microservice) doesn't need to change,
  just what a "product" or "plan" actually is.
- **Decide**: whether `firewall-msp`'s existing `nexwall-license` package
  (talking to `license.nexwall.com.br`) gets folded into this now-real
  Postgres-backed entitlements system, or stays separate. This was flagged
  as needing its own ADR before this repo existed — write it now that
  there's a concrete system to fold into (or not).

**Acceptance criteria (= Phase 2 exit criteria)**: a partner creates a
customer through this system; a real stack exists and is reachable at its
subdomain on the other cluster within the same flow.
