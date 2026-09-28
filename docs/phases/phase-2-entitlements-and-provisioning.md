# Phase 2 — Entitlements + Management Plane integration

**Status**: Not started.
**Goal**: onboarding a customer here provisions a real, reachable
`nexwall-controller` stack on `nexwall-multi-tenant`'s cluster.
**Repos touched**: this repo (calling side); `nexwall-multi-tenant` only if
its Management Plane contract needs a field this repo discovers it's
missing — coordinate, don't fork the contract.

## 2.1 — Management Plane client

- **Build new**: per ADR 0003 — a client using the dedicated
  `PARTNER_API_KEY`, calling `POST /tenants`, `GET /tenants/:id/status`,
  `POST /tenants/:id/suspend`, `DELETE /tenants/:id` as defined in
  `nexwall-multi-tenant`'s `docs/contracts/management-plane-openapi.yaml`.
  No reference to study beyond that contract itself — this integration is
  unique to Nexwall's two-cluster shape (ADR 0006 over there explains why
  `my` has nothing equivalent).
- **Build new**: the `provisioning_failed` state handling from ADR 0003 —
  surfaced to the partner UI, with a retry action that's explicit (partner
  clicks "retry"), not automatic silent retry loops.

## 2.2 — Entitlements model

- **Study**: `my`'s `entitlements.go` for the *concept* (subscription/plan
  state stored alongside org/system data, not in a separate license
  microservice) — pattern only, independently implemented (ADR 0001).
- **Build new**: whether `firewall-msp`'s existing `nexwall-license`
  package (talking to `license.nexwall.com.br`) gets folded into this
  service's data model at this point, or stays separate. This is the
  decision `nexwall-multi-tenant`'s own Phase 4 doc flagged as "worth its
  own ADR" before this repo existed — now that this repo is the natural
  home for it, make that call here.

**Acceptance criteria (= Phase 2 exit criteria)**: a partner creates a
customer through this service's UI/API; within the same flow (sync or
polled async), a real stack exists and is reachable at its subdomain on the
other cluster.
