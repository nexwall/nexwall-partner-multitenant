# Phase 3 — Billing

**Status**: Not started.
**Goal**: subscriptions tied to entitlements; non-payment leads to
suspension without manual intervention.

## 3.1 — Billing provider integration

- **Build new**: entirely new — no `my` reference for the actual payment
  provider integration (that's Nethesis-internal/unpublished regardless of
  license). Check what `license.nexwall.com.br` already integrates with
  before standing up a second payment relationship (per Phase 2.2's
  decision on folding `nexwall-license` in).

## 3.2 — Suspension automation

- **Reimplement** (pattern from `my`'s `local_systems_suspend_test.go`,
  independently written): non-payment triggers a call to this repo's own
  Management Plane client (Phase 2.1) → `POST /tenants/:id/suspend` on the
  other cluster. The suspend *mechanism* already exists once Phase 2 of
  `nexwall-multi-tenant` ships; this phase only adds the *trigger*.

**Acceptance criteria (= Phase 3 exit criteria)**: a simulated non-payment
results in the customer's real stack being suspended (scaled to 0) within
one billing cycle's grace period, with no engineer involved.
