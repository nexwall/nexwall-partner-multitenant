# Phase 1 — Auth + org hierarchy skeleton

**Status**: Not started.
**Goal**: partners can log in; the org hierarchy exists and actually
enforces visibility (a reseller genuinely cannot query another reseller's
customers).
**Repos touched**: this repo only.

---

## 1.1 — Choose and stand up an IdP

- **Study**: `my`'s `DESIGN.md` token-exchange flow diagram (external IdP
  login → this service exchanges the IdP token for its own JWT embedding
  RBAC claims). This is the pattern to follow — not Nethesis's specific IdP
  choice (Logto). Evaluate Logto, Keycloak, Authentik, or Zitadel on their
  own merits (self-hosting story, license, admin UX) since nothing about
  the pattern requires matching their vendor choice.
- **Build new**: the token-exchange endpoint itself
  (`POST /auth/exchange` or similar) and the custom JWT's claim shape
  (org ID, role, hierarchy path) — written independently, informed by the
  diagram's *shape*, not by reading `my`'s Go implementation of it (ADR
  0001).

**Acceptance criteria**: a partner logs in via the chosen IdP, receives a
JWT from this service (not the IdP's raw token), and that JWT is what every
subsequent API call is authenticated with.

## 1.2 — Org hierarchy schema + visibility enforcement

- **Study**: this repo's own ADR 0002 for the exact tier decision (3 vs 4
  levels) — confirm it's actually been answered before starting this task,
  it's a hard prerequisite.
- **Build new**: `organizations` table (self-referencing `parent_id` or
  explicit `created_by_org_id` + `created_by_role`), and the recursive
  visibility query described in ADR 0002. No code to reuse from `my` here
  (ADR 0001) — but the *shape* of the query (walk descendants, filter list
  endpoints by it) is exactly what to replicate independently.
- **Build new**: apply the same visibility filter to every list/read
  endpoint from day one, not bolted on later — a hierarchy model that's
  correct in the schema but unenforced in half the endpoints is worse than
  no hierarchy at all, since it looks secure without being secure.

**Acceptance criteria**: create a Reseller A with Customer 1, Reseller B
with Customer 2 — Reseller A's token can list Customer 1 but a request for
Customer 2 returns 404 (not 403 — don't leak that Customer 2 exists at
all), and Nexwall's own top-level role can see both.

## 1.3 — Basic partner-facing UI shell

- **Build new**: no reference in `my` to draw the actual UI from (ADR 0001
  applies to `my`'s frontend too, even though it's GPL-3.0 not AGPL — same
  clean-room discipline, simpler license question but same practice).
  Reasonable default: match `nexwall-ui`'s existing Vue/Tailwind stack for
  consistency across Nexwall's own products, login + org list/detail views
  only for this phase — no entitlements or billing UI yet (that's Phase 2/3).

**Acceptance criteria (= Phase 1 exit criteria)**: a partner can log in and
see a list of their own customers (empty list is fine at this stage — no
customer creation flow yet, that's Phase 2 once Management Plane
integration exists).
