# ADR 0002: Organization hierarchy — default to Nexwall → Reseller → Customer, confirm Distributor tier before building it

**Status**: Proposed (one open question below needs a real answer before
Phase 2 implementation starts, not before this ADR is accepted)

## Context

`my`'s hierarchy is `Owner (Nethesis) → Distributor → Reseller → Customer`,
with hierarchical visibility (each level sees only what it or its
descendants created). This is a proven, extensible shape — but it's
Nethesis's *own* business structure, not automatically ours. Copying it
wholesale without checking whether Nexwall actually has a distributor tier
(as distinct from resellers) risks building unused complexity, or worse,
the wrong complexity.

## Decision

Adopt the same *pattern* — a small fixed set of organization roles with
hierarchical, `createdBy`-based visibility, independently implemented (see
ADR 0001) — but default to three tiers, not four:

```
Nexwall (platform owner) → Reseller → Customer
```

A `Reseller` in this model is any partner who onboards their own end
customers through `partner.nexwall.com.br`. If Nexwall's actual channel
turns out to need a distinct distributor tier (partners who manage other
resellers, rather than customers directly), the visibility-scoping pattern
(described below) extends to a fourth level without a schema rewrite — it's
the same recursive `createdBy`/`createdByRole` shape either way, just one
more enum value and one more level of the visibility check.

### Visibility rule (the actual reusable idea from `my`)

Every org record stores who created it and at what role. A read/list query
filters to: records the requester created directly, plus (recursively) records
created by anyone the requester's own descendants created. Implemented as our
own query logic against our own schema — not copied from `my`'s
implementation (per ADR 0001).

## Open question — confirm before Phase 2 hierarchy work starts

**Does Nexwall's actual go-to-market have a distributor tier, or is
`Reseller` (flat, one level between Nexwall and Customer) the whole
channel?** This determines whether Phase 2 builds a 3-tier or 4-tier model.
Building the 4th tier speculatively, before it's a real requirement, repeats
the exact mistake `nexwall-multi-tenant`'s own ADR 0002 warned against for a
different question (Option A shared-tenancy, deferred until real scale data
justifies it) — don't guess ahead of a real need here either.

## Consequences

- Phase 1 (auth/RBAC skeleton) can proceed without this being answered, as
  long as the role enum and visibility-check code are written to make
  adding a 4th tier a small change, not a rewrite.
- Phase 2 (actual hierarchy + entitlements) should not start until this is
  answered.
