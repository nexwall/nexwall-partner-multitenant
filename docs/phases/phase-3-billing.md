# Phase 3 — Billing adaptation

**Status**: Not started.
**Goal**: subscriptions tied to entitlements (Phase 2.2); non-payment leads
to suspension without manual intervention.

## 3.1 — Billing provider integration

- **Reimplement/adapt**: upstream's billing integration (if `my` has one
  wired in already — verify during Phase 2, this wasn't confirmed during
  the original analysis) or **build new** if upstream's entitlements are
  tracked but not actually billed through an integrated payment provider.
  Either way: check what `license.nexwall.com.br` already integrates with
  before standing up a second payment relationship (per Phase 2.2's
  fold-in decision).

## 3.2 — Suspension automation

- **Reimplement/adapt** (working code, `local_systems_suspend_test.go`'s
  pattern): non-payment triggers a call through Phase 2.1's Management
  Plane client → `POST /tenants/:id/suspend` on the other cluster. The
  suspend logic on *this* side (marking a customer non-current) is
  upstream's own working code; only the trigger-into-the-other-cluster call
  is new (already built in Phase 2.1).

**Acceptance criteria (= Phase 3 exit criteria)**: a simulated non-payment
results in the customer's real stack being suspended within one billing
cycle's grace period, with no engineer involved.
