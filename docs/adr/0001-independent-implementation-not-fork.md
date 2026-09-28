# ADR 0001: Independent implementation, informed by `NethServer/my` but not derived from it

**Status**: Accepted

## Context

This repo exists because `NethServer/my` (Nethesis's account/billing/RBAC/
reseller-hierarchy platform) was identified as a close architectural match
for what a Nexwall Partner Program needs — see
`dev-nethsec-reference/multi-tenant-design/my-nethesis-reference.md` for the
full analysis. `my`'s `backend/` and `collect/` services are licensed
**AGPL-3.0-or-later**; `frontend/` is GPL-3.0-or-later. This repo is
GPL-3.0-only.

AGPL's network-copyleft clause (§13) means: if code derived from `my`'s
`backend`/`collect` ends up combined into a network service we run, the
*combination as such* inherits AGPL's obligation to offer complete
corresponding source to every user who interacts with it over the network —
regardless of whether the surrounding project is nominally GPL-3.0 (GPLv3
§13 explicitly permits the combination, but does not remove AGPL's
network-source-disclosure requirement from it). For a commercial partner
portal, that's a materially different posture than we currently have, and
not one to take on by accident.

## Decision

Everything in this repo is an **independent, clean-room implementation**.
`my` is studied at the design level only — organization hierarchy shape,
the idea of storing entitlements in the same schema as the org/system data
rather than a separate billing microservice, soft-delete conventions, the
token-exchange SSO pattern. None of its source is copied, adapted line-by-
line, or used as a starting template.

Concretely, for anyone (human or AI) implementing a feature here:

1. **Do not have `my`'s source open while writing this repo's code.** Read
   analysis documents (`my-nethesis-reference.md`, this repo's own phase
   docs) instead, and implement from those. This is the practical version
   of a classic clean-room separation — one party studies and writes a
   spec, a different pass implements from the spec without the original
   open alongside it.
2. **Interface/API shapes are lower-risk than implementation code, but not
   risk-free.** `my` publishes a versioned OpenAPI contract
   (`backend/openapi.yaml`, also live at `api.my.nethesis.it`) that is
   useful to study for realistic request/response shapes, pagination, and
   error conventions — implementing against a published contract is
   standard interoperability practice. Still: write this repo's own
   `docs/contracts/*.yaml` from scratch, informed by what `my`'s contract
   gets right, not copied from its YAML text (the YAML file itself is
   AGPL-licensed content, same as the Go source).
3. **Reuse the same freely-licensed third-party libraries `my` uses where
   they fit** (whatever IdP, ORM, or framework choices make sense on their
   own merits) — that's a completely free way to get proven building
   blocks without touching Nethesis's own code at all.
4. **When in doubt, don't copy — ask.** If a task seems to require
   reproducing something close to verbatim from `my` (a specific validation
   rule, an exact error message, a specific algorithm), stop and flag it
   rather than pasting it in.

## Consequences

- Some initial quality gap vs. `my`'s production-hardened edge cases is
  expected and accepted — mitigated by: studying `my`'s public OpenAPI
  contract closely (design-level, not copied), reusing proven third-party
  libraries, and this repo's own phased rollout (a handful of real partners
  before wide availability, mirroring `nexwall-multi-tenant`'s own
  pilot-first approach).
- Every phase doc in this repo that references `my` as a study source
  (see `docs/phases/`) repeats the "pattern not code" instruction inline,
  rather than relying on this ADR being remembered — licensing caveats that
  only live in one document tend to get lost across a long project.
- This is not legal advice; if this repo starts scaling commercially,
  the earlier judgment call here should be revisited with an actual lawyer
  experienced in GPL/AGPL compliance, particularly given `my`'s author
  (Nethesis) is based in Italy and this project is Brazilian.
