# NOTICE

This project's design was informed by studying [`NethServer/my`](https://github.com/NethServer/my)
(Nethesis's own account/billing/RBAC/reseller-hierarchy platform behind
`my.nethesis.it`) as a reference architecture — see
[`dev-nethsec-reference/multi-tenant-design/my-nethesis-reference.md`](https://github.com/nexwall/dev-nethsec-reference/blob/main/multi-tenant-design/my-nethesis-reference.md)
for the full analysis.

**No code from `NethServer/my` is copied or adapted into this repository.**
`my`'s `backend/` and `collect/` services are licensed AGPL-3.0-or-later —
materially different obligations from this repo's own GPL-3.0-only, and
this repo is a network service. Everything here is an independent,
clean-room implementation written from a design-level understanding of the
*patterns* `my` uses (organization hierarchy, entitlements-in-the-same-
schema, soft-delete conventions), not from its source. See
`docs/adr/0001-independent-implementation-not-fork.md` for the full
reasoning and the practical rule this implies for contributors.

This repository also calls the Management Plane API defined in
[`nexwall-multi-tenant`](https://github.com/nexwall/nexwall-multi-tenant)
(`docs/contracts/management-plane-openapi.yaml` there) to provision real
customer infrastructure — that repo's own NOTICE.md covers its own
licensing (GPL-3.0, inherited from `nexwall-controller`/NethSecurity); this
repo has no dependency on that lineage itself.

This repository's own code is licensed GPL-3.0-only — see `LICENSE`.
