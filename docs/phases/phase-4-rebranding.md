# Phase 4 — Rebranding / white-label

**Status**: Not started.
**Goal**: a reseller's customers see the reseller's own branding, not
"Nexwall."

## 4.1 — Per-organization branding data

- **Study**: `my`'s `rebranding.go` for the concept (product name/assets
  served per organization) — pattern only (ADR 0001).
- **Build new**: where branding actually renders is genuinely new design
  surface `my` doesn't have to solve in the same way — `my`'s own product
  (NethServer/NethSecurity) runs on customer-owned hardware, so its
  rebranding is scoped to `my`'s own UI chrome. Nexwall's equivalent would
  need to reach further: does a reseller's branding also need to appear
  inside the *customer's own* `nexwall-controller` UI (the other cluster,
  other repo), not just this repo's partner-facing UI? That's a real
  cross-repo design question to resolve before implementation — likely
  needs a field on the Management Plane's tenant record (branding
  reference) rather than something this repo can do alone.

**Acceptance criteria (= Phase 4 exit criteria)**: a reseller sets a
product name/logo once; it appears both in this repo's own UI for that
reseller's context and, if the cross-repo design question above is resolved
in favor of it, in that reseller's customers' `nexwall-controller` UI too.
