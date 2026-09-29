# Phase 1 — Strip Nethesis-specifics, decide hierarchy, stand up our own IdP

**Status**: Not started. Depends on Phase 0.5's infra decision being made
first.

## 1.1 — Own Logto (or equivalent IdP) tenant

- Upstream assumes a Nethesis-owned Logto tenant with specific M2M app
  credentials. **Build new** (our own account, not upstream's): a Nexwall
  Logto tenant (or, if evaluated and preferred, a different IdP — the
  backend's token-exchange code is IdP-agnostic in principle, but currently
  wired specifically to Logto's API shape, so swapping IdPs is real work,
  not a config change; only do this if Logto itself is rejected on its own
  merits, not by default).
- **Reimplement** (already-working code, pointed at new credentials): the
  actual token-exchange flow (`backend`'s auth package) — no logic change
  needed, just configuration once our own tenant exists.

## 1.2 — Rewrite `backend/authz/apps.yml` and `sync/configs/config.yml`

- Per Phase 0.3's inventory: replace Nethesis's federated-app list with
  Nexwall's actual list (at minimum `partner.nexwall.com.br` itself; add
  others as they exist — MSP internal tools, if any need SSO through this
  system).
- **Study**: the access-control model documented in `apps.yml`'s own header
  comment (organization_ids AND organization_roles AND user_roles,
  fail-closed) before writing new entries — it's a real, specific
  authorization model, not free-form config; get the semantics right rather
  than pattern-matching an existing entry superficially.

## 1.3 — Resolve ADR 0002 (hierarchy tiers) and act on it

- If keeping 4 tiers (the new default recommendation per ADR 0002's
  update): **no code change**, just confirm and close the question.
- If removing the distributor tier: real schema migration + `backend/authz/`
  changes + `frontend/` UI changes — scope this as its own mini-project if
  chosen, not a quick edit.

## 1.4 — Default branding

- **Build new** (Nexwall's own default, distinct from the *reseller*
  white-labeling feature covered in Phase 4): product name, logo, color
  scheme throughout `frontend/`, replacing Nethesis's own defaults. Keep
  `rebranding.go`'s actual per-organization override mechanism untouched —
  this task only changes what shows when no reseller override is set.

## 1.5 — `render.yaml` (or its replacement, per Phase 0.5)

- Once Phase 0.5's infra decision is made: either rewrite `render.yaml`
  with Nexwall's own service names/regions/IP-allowlists (Option A), or
  begin the Helm-chart translation (Option B) — this task's shape depends
  entirely on that decision, don't start it before 0.5 is resolved.

## Exit criteria

A partner (a real or test account under Nexwall's own Logto tenant) can log
in, see Nexwall's own branding (not Nethesis's), and the federated-app list
reflects Nexwall's actual apps, not Nethesis's internal tools.
