# Phase 4 — Reseller white-labeling

**Status**: Not started, but now mostly verification rather than
construction.
**Goal**: a reseller's customers see the reseller's own branding, not
"Nexwall" (distinct from Phase 1.4's Nexwall-own-default branding).

## 4.1 — Verify and adapt `rebranding.go`

- **Reimplement/adapt** (working code, already does most of what's
  needed): `backend/methods/rebranding.go` already serves per-organization
  product name/assets. Verify it actually covers what Nexwall resellers
  need; adapt field names/asset types if Nexwall's branding model differs
  from Nethesis's (e.g. different asset dimensions, additional fields like
  a support-contact email per reseller).

## 4.2 — Cross-repo branding question (genuinely new design surface)

- **Build new** — no upstream equivalent, since `my`'s own product
  (NethServer/NethSecurity) runs on customers' own hardware and never
  needed this: does a reseller's branding also need to appear inside the
  *customer's own* `nexwall-controller` UI (the other cluster, other repo),
  not just this repo's partner-facing UI? If yes, this needs a field on
  `nexwall-multi-tenant`'s Management Plane tenant record (a branding
  reference passed at provisioning time via Phase 2.1's client) — a
  cross-repo design decision, not something this repo can complete alone.

**Acceptance criteria (= Phase 4 exit criteria)**: a reseller sets a
product name/logo once; it appears in this repo's own UI for that
reseller's context, and — if 4.2 is resolved in favor of it — in that
reseller's customers' `nexwall-controller` UI too.
