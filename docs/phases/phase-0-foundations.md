# Phase 0 — Foundations

**Status**: In progress.
**Goal**: repo scaffold exists, infra decisions made, nothing deployed yet.

## Tasks

1. **Build new**: choose and provision infra for this cluster — a genuinely
   new decision, not inherited from `nexwall-multi-tenant`'s ADR 0001. k3s
   is a reasonable default for consistency (same operational knowledge
   reused), but this repo's actual load shape (a smaller number of human
   partner users hitting a business-logic API, vs. many isolated tenant
   stacks) is different enough that it's worth a deliberate ADR rather than
   copying the other repo's reasoning unexamined. Write `docs/adr/0004-
   infra-choice.md` before provisioning.
2. **Build new**: DNS for `partner.nexwall.com.br` and its API subdomain
   (mirror the pattern already established for `license`/`updates`/
   `lists.nexwall.com.br` per `nexwall-multi-tenant`'s ADR 0005 — same
   registrar, same certbot-on-VPS approach is a reasonable default unless
   this cluster's TLS termination needs differ).
3. **Study**: confirm network reachability requirements between this
   cluster and `nexwall-multi-tenant`'s cluster (needed for ADR 0003's
   Management Plane calls) — this affects the infra choice in task 1, so
   resolve it as part of the same decision, not after.
4. Resolve ADR 0002's open question (distributor tier: yes/no) — needed
   before Phase 1's role enum is finalized, not before Phase 0 itself.

## Exit criteria

Cluster reachable, DNS resolving, repo pushed with CI passing on the
skeleton (lint-only, nothing to test yet) — mirrors
`nexwall-multi-tenant`'s own Phase 0 bar.
