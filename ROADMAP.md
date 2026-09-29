# Roadmap

| Phase | Status | Goal | Key exit criterion |
|---|---|---|---|
| 0 — Import + compliance | In progress | Fork done (ADR 0004); license mechanics, CI triage, Nethesis-config inventory, infra decision | In-app source link live, infra decision recorded as ADR |
| 1 — Strip + rebrand | Not started | Own IdP tenant, own federated-app list, own default branding, hierarchy decision resolved | A partner logs in seeing Nexwall's own branding, not Nethesis's |
| 2 — Management Plane integration | Not started | Onboarding a customer here provisions a real stack on `nexwall-multi-tenant` | One partner action creates a real, reachable customer stack |
| 3 — Billing | Not started | Subscriptions tied to entitlements, invoicing | A non-paying customer gets suspended automatically |
| 4 — Reseller white-labeling | Not started | Mostly verifying already-working code (`rebranding.go`) + the one genuinely new cross-repo branding question | A reseller's customers see the reseller's brand |

This project is a **direct fork of `NethServer/my`** (ADR 0004, superseding
the earlier clean-room decision in ADR 0001) — full upstream history
preserved. Every phase doc distinguishes **adapt** (working upstream code,
being changed for Nexwall) from **build new** (no upstream equivalent —
mainly the Management Plane integration, which `my` never needed since
Nethesis's products run on customers' own hardware rather than
infrastructure Nethesis operates itself).

Read `docs/adr/0004-fork-nethserver-my-directly.md` and `NOTICE.md` before
starting any phase — they cover the ongoing AGPL compliance obligations
this decision created, which are operational (keep this repo in sync with
production, preserve attribution, add modification notices) not one-time.

## Related system

`nexwall-multi-tenant` (`nexwall-controller` fleet management, separate
cluster, separate license — GPL-3.0) is where real customer infrastructure
runs. This repo calls into it (ADR 0003) but does not replace or duplicate
it. See that repo's `docs/adr/0006-partner-program-separate-service.md`.
