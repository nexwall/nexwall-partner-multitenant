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

## 1.5 — New `charts/` (k3s, per ADR 0005)

- **Build new**: translate `docker-compose.yml` (and
  `services/mimir/docker-compose.yml` separately) into a Helm chart,
  mirroring `nexwall-multi-tenant`'s `charts/nexwall-controller/`
  structure and its README's 1:1-mapping-table convention — that
  convention proved itself well worth repeating here rather than
  inventing a different documentation style for this chart.
- **Build new**: disable the Render-specific CI workflows (ADR 0005),
  with the explanatory top-of-file comment already planned in Phase 0 —
  do this now, it's unambiguous and was only deferred pending the infra
  decision.

## 1.6 — Routing-by-role + per-tenant handoff secret storage (per ADR 0006)

- **Build new**: in the existing token-exchange step, branch on org role —
  Reseller/Owner staff get today's behavior (Partner Program session);
  Customer-org users get no Partner Program session at all, instead a
  signed handoff token and an HTTP redirect to their tenant's subdomain.
- **Build new**: store each Customer org's per-tenant handoff secret
  (generated at provisioning time, Phase 2) and subdomain — new fields on
  whatever this repo's Customer-org record already holds.
- Note this task only produces the *redirect* — actually skipping the
  customer's local login screen depends on `nexwall-controller` shipping
  its own receiving endpoint (a different repo, not blocked on by this
  repo's own Phase 1 exit criteria, but worth flagging to whoever's
  coordinating both repos' timelines).

## Exit criteria

A partner (a real or test account under Nexwall's own Logto tenant) can log
in, see Nexwall's own branding (not Nethesis's), and the federated-app list
reflects Nexwall's actual apps, not Nethesis's internal tools.
