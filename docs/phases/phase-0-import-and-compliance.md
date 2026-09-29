# Phase 0 — Import, license compliance, and infra decision

**Status**: In progress. The fork/merge itself is done (see ADR 0004) —
this phase covers what has to happen before the inherited code is safely
adaptable and legally sound to run.

## 0.1 — License compliance mechanics (done in this commit)

- `LICENSE` set to upstream's AGPL-3.0 text (unchanged, verbatim).
- `NOTICE.md` rewritten to state the fork, preserve attribution, and spell
  out the ongoing AGPL §13 obligations (public + in-sync + in-app source
  link — the last one is not yet done, see 0.4).
- `CONTRIBUTING.md` rewritten: the modification-notice rule (AGPL §5) is
  now load-bearing for every future change to an inherited file.

## 0.2 — CI triage

Upstream's `.github/workflows/` includes Render-specific PR-preview and
deploy automation (`pr-preview-resume.yml`, `pr-preview-suspend.yml`,
`pr-redirect-uris-add/remove.yml`, `release-production.yml`) tied to
Nethesis's own Render account and GitHub App. These will fail harmlessly
(missing secrets) if left enabled — no security exposure (verified: no real
credentials committed upstream, only `.env.example` and Render
`sync: false` placeholders), just noise.

- **Build new / decide**: either disable these specific workflows (add a
  top-of-file comment explaining why, don't silently delete — future-us
  should know they existed and why they're off, not just find them gone)
  or adapt them once Nexwall has its own Render (or equivalent) account.
  `ci-main.yml` (build/test per component) and `e2e-*.yml` are
  infra-agnostic and worth keeping active as-is.

## 0.3 — Strip / replace Nethesis-specific configuration (inventory, not yet done)

Found during import, needs a deliberate pass before this is "Nexwall's own"
rather than "Nethesis's `my`, running under a different name":

- `render.yaml` — Nethesis HQ IP allowlist, Frankfurt region, DigitalOcean
  Spaces buckets, `my-*-prod`/`my-*-qa` service names. Needs Nexwall's own
  values throughout, or a decision to deploy differently (see 0.5 — this
  isn't necessarily a Render deployment for us at all).
- `backend/authz/apps.yml` — the list of third-party apps this identity
  system federates SSO for (`nethshop.nethesis.it`, `stock.nethesis.it`,
  `helpdesk.nethesis.it`, `formazione.nethesis.it`, `partner.nethesis.it`
  itself). Nexwall almost certainly federates a different, much shorter
  list (likely just `partner.nexwall.com.br` and whatever internal tools
  MSP staff use) — this file needs a full rewrite, not editing.
- Branding: default product name, logo, colors throughout `frontend/` —
  distinct from the *reseller white-labeling feature* (`rebranding.go`,
  Phase 4) — this is the *default*/Nexwall's-own brand, not a customer
  reseller's.
- Any hardcoded `nethesis.it` / `my.nethesis.it` domain references in
  backend config, CORS allowlists, email templates.

**This is inventory, not yet action** — each item above becomes a task in
Phase 1 once triaged for scope.

## 0.4 — In-app "Source code" link

Required before any real partner uses the live service (NOTICE.md, ADR
0004). Small `frontend/` change: a visible link in the footer/about page
pointing at this exact repo. Cheap, easy to forget — do it early rather than
as an afterthought right before launch.

## 0.5 — Infra decision: RESOLVED, k3s (ADR 0005)

k3s, own cluster, separate from `nexwall-multi-tenant`'s. See
`docs/adr/0005-infra-choice-k3s.md` for the full reasoning. This means:
Render-specific CI workflows get disabled (not adapted), and a new
`charts/` directory (mirroring `nexwall-multi-tenant`'s
`charts/nexwall-controller/` structure) is a concrete Phase 1 deliverable.

## 0.6 — Single-FQDN login + tenant handoff: RESOLVED architecture (ADR 0006)

`partner.nexwall.com.br` serves everyone — partners and customer end-users
alike — with post-login routing decided by this backend, not by per-tenant
Logto redirect URIs. See `docs/adr/0006-single-fqdn-email-login-tenant-
handoff.md` for the full design, including the **cross-repo dependency**:
`nexwall-controller` needs a new endpoint to accept the handoff token this
repo will mint (tracked in that repo's own new
`docs/adr/0001-sso-handoff-from-partner-program.md`) — not yet built, not
part of this repo's own work.

## Exit criteria

License compliance mechanics done (0.1 — already true as of this commit).
CI triaged: Render-specific workflows disabled with explanatory comments
(not yet done — now unambiguous per ADR 0005, do this before Phase 1
starts rather than leaving it "to decide"). Nethesis-specific config fully
inventoried (not yet fixed — that's Phase 1). In-app source link live.
Infra and login-architecture decisions made and recorded as ADRs (0005,
0006 — both done as of this commit).
