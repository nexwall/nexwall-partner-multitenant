# Roadmap

| Phase | Status | Goal | Key exit criterion |
|---|---|---|---|
| 0 — Foundations | In progress | Repo scaffold, infra decision made | This commit |
| 1 — Auth + org hierarchy skeleton | Not started | Partners can log in; org hierarchy exists and enforces visibility | A reseller sees only their own customers |
| 2 — Entitlements + Management Plane integration | Not started | Onboarding a customer here provisions a real stack over there | One partner action creates a real, reachable customer stack |
| 3 — Billing | Not started | Subscriptions tied to entitlements, invoicing | A non-paying customer gets suspended automatically |
| 4 — Rebranding / white-label | Not started | Resellers can apply their own branding | A reseller's customers see the reseller's brand, not "Nexwall" |

Each phase doc in `docs/phases/` follows the same discipline as
`nexwall-multi-tenant`: every task says what to **study** (our own docs,
`my`'s design-level patterns, or generic third-party library docs), what to
**reimplement** (an existing pattern, written independently — never `my`'s
code, see `docs/adr/0001-independent-implementation-not-fork.md`), and what
has **no reference and must be built new** (the Management Plane
integration itself — `my` has no equivalent of this).

## Related system

`nexwall-multi-tenant` (`nexwall-controller` fleet management, separate
cluster) is where real customer infrastructure actually runs. This repo
calls into it (ADR 0003) but does not replace or duplicate it. See that
repo's `docs/adr/0006-partner-program-separate-service.md`.
