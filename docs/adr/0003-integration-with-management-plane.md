# ADR 0003: How this service talks to `nexwall-multi-tenant`'s Management Plane

**Status**: Accepted

## Context

Per `nexwall-multi-tenant`'s ADR 0006, this repo owns partner/business data;
that repo owns real infrastructure provisioning. When a partner onboards a
new end customer here, this service must call that repo's Management Plane
API (`POST /tenants`, `docs/contracts/management-plane-openapi.yaml` in
that repo) to actually provision the customer's `nexwall-controller` stack.

## Decision

- **Auth**: a dedicated `PARTNER_API_KEY`, issued specifically to this
  service, distinct from the key Management Plane's human operators type by
  hand (`MGMT_API_KEY` there). Independently rotatable. This repo never
  uses an operator's personal credential.
- **Direction**: one-way for now — this service calls Management Plane;
  Management Plane does not call back into this service. If status webhooks
  become necessary later (e.g. push tenant-health changes here instead of
  polling), that's a new decision, not assumed now.
- **Failure handling**: if the Management Plane call fails during customer
  onboarding, this service's own record of the customer is created in a
  `provisioning_failed` state (not silently retried forever, not left in an
  ambiguous "maybe it worked" state) — a partner-facing onboarding flow
  needs to surface this clearly, since an unresponsive customer stack with
  no clear error is the worst outcome for both the partner and Nexwall
  support.
- **What's duplicated vs. referenced**: this service stores its own
  `tenant_id` (matching the ID Management Plane assigns) plus whatever
  partner/billing metadata is ours to own. It does not duplicate
  Management Plane's own state (namespace name, Helm release status,
  network allocation) — that's queried live via `GET /tenants/:id/status`
  when needed, not mirrored into this service's own database, to avoid two
  systems disagreeing about the same fact.

## Consequences

- This service has a hard runtime dependency on Management Plane's API
  being reachable across clusters — network path and firewalling between
  the two clusters needs to be a deliberate infra decision (own ADR when
  Phase 1 infra work starts), not an afterthought.
- Management Plane's `POST /tenants` becomes a semi-public surface once
  this integration exists — any breaking change to that contract now needs
  coordination across two repos, not just one. Treat
  `management-plane-openapi.yaml` in `nexwall-multi-tenant` as a real,
  versioned external contract from this point on.
