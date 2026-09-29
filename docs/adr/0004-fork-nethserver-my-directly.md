# ADR 0004: Fork `NethServer/my` directly — supersedes ADR 0001

**Status**: Accepted. Supersedes ADR 0001 (independent implementation).
ADR 0001 is left in place, unedited, per this project's own convention
(never edit a past ADR) — read it for the reasoning that applied until
this decision, since parts of it (the visibility model, the token-exchange
pattern) are still accurate descriptions of what's now literally in this
codebase.

## Context

ADR 0001 chose clean-room reimplementation specifically to avoid AGPL's
network-copyleft obligation attaching to this service. That reasoning was
correct as far as it went, but it optimized for a risk (license obligation)
against a cost (reimplementation effort, production-hardened edge cases
lost) without weighing a third factor: `my` already does essentially
everything this repo needs, correctly, in production, today. Reimplementing
it was going to cost real time to arrive back at approximately the same
design `my` already validated.

## Decision

Fork `NethServer/my` directly, full history preserved (`git merge
--allow-unrelated-histories` from a full clone, not a squashed import — see
the merge commit). Accept AGPL's obligations as the cost of this choice,
and satisfy them deliberately rather than incidentally.

## What this actually obligates us to do (not just "keep the repo public")

AGPL-3.0 §13 requires offering the **complete corresponding source of what
is actually running** to every user who interacts with the service over the
network. This is an ongoing operational obligation, not a one-time import:

1. **This repository must stay public and stay in sync with production.**
   A public repo that's three months stale while production runs a
   different, unpublished commit does not satisfy §13 — the source offered
   must correspond to what users are actually interacting with.
2. **An actual offer, reachable by users, not just a public URL that
   happens to exist.** Add a visible "Source code" link inside the running
   `partner.nexwall.com.br` application itself (footer, about page, or
   similar) pointing at this exact repo — required before any real partner
   uses the live service, tracked as a task in
   `docs/phases/phase-0-import-and-compliance.md`.
3. **Preserve every copyright and license header, in every file, as-is.**
   Do not strip Nethesis's copyright notices during rebranding work — AGPL/
   GPL require them regardless of how much the file's behavior changes.
4. **Any file we modify going forward needs a modification notice** (AGPL
   §5 / GPLv3 §5: "carry prominent notices stating that you modified it,
   and giving a relevant date"). A one-line comment near the top of a
   changed file (`// Modified by Nexwall, <date>: <what changed>`) satisfies
   this — add it as part of the same commit that makes the change, not
   retroactively.
5. **This is not the AGPL-avoidance posture ADR 0001 established.** There is
   now a real, live compliance obligation that didn't exist under the
   clean-room path. This isn't a one-time checkbox — it's a standing
   operational requirement for as long as this codebase (or a derivative of
   it) runs as a network service. If a lawyer with GPL/AGPL experience
   hasn't reviewed this posture yet, that review is worth doing before
   real partner traffic hits the service, not after — see ADR 0001's own
   closing note, which still applies.

## What does NOT change

- ADR 0002 (org hierarchy) — still open question about the distributor
  tier, now reframed: the codebase already implements 4 tiers
  (Owner/Distributor/Reseller/Customer), so "3 tiers" now means *removing*
  a tier from working code, not *adding* one to a blank schema. Doesn't
  change the underlying business question, changes the direction of the
  work once answered.
- ADR 0003 (Management Plane integration) — fully unchanged. `my` has zero
  code for provisioning Kubernetes infrastructure; this integration is
  still entirely new work regardless of fork-vs-reimplement, and still
  needs designing/building from scratch.

## Consequences

- `docs/phases/` is rewritten (see `phase-0-import-and-compliance.md`
  onward) — the work is now "adapt/strip/rebrand a working system," not
  "build one from a spec."
- `CONTRIBUTING.md`'s previous rule ("don't have `my`'s source open while
  writing code") is void and replaced with the opposite: this *is* that
  source now, work in it directly, but follow the modification-notice
  requirement above for every changed file.
- Upstream's own CI workflows (PR preview environments on Render,
  release/deploy automation) reference Nethesis's own Render account and
  will fail harmlessly (missing secrets) until adapted or disabled — see
  Phase 0's task list for triage; nothing here is a security exposure
  (verified no real credentials were committed upstream, only
  `.env.example` templates and Render `sync: false` placeholders), just
  noisy CI failures if left as-is.
