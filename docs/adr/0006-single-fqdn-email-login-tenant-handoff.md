# ADR 0006: One shared FQDN, email-based login, Sophos-ID-style handoff into the customer's own controller

**Status**: Accepted. Has a real dependency on `nexwall-controller` (a
different repo — see "Cross-repo dependency" below); this ADR only covers
what this repo does.

## Context

`partner.nexwall.com.br` is **one** FQDN for every partner and every
customer — not a per-partner or per-customer subdomain. A user logs in with
their email; what they see next depends on who they are:

- **Reseller/Nexwall staff** → this repo's own Partner Program dashboard
  (customer list, entitlements, billing — the actual purpose of this repo).
- **A customer's own end-user** (an admin at the MSP's customer who needs
  to manage *their* firewalls) → should land directly inside *their*
  specific `nexwall-controller` instance, already authenticated — no second
  login screen, the way Sophos Central takes you straight into your
  tenant's console after one central login.

## Decision

### Where the redirect decision happens

Logto's OIDC redirect URI is **fixed** (this app's own callback,
`https://partner.nexwall.com.br/callback`) — not per-tenant. Registering
every customer's subdomain as an allowed Logto redirect URI would work
technically (upstream's own `pr-redirect-uris-add/remove.yml` workflows
prove Logto supports dynamic URI registration via API) but doesn't scale
cleanly to potentially hundreds of customer subdomains and adds needless
operational coupling.

Instead: Logto authenticates the user and redirects back to this app's one
fixed callback, exactly like today. **This backend**, in the existing
token-exchange step (`my`'s `DESIGN.md` pattern, already how this fork's
auth works), makes the routing decision:
- Org role is Reseller/Owner staff → issue this app's own JWT, show the
  dashboard. No change from current behavior.
- Org role is Customer → **don't** issue a Partner Program session at all;
  instead, mint a short-lived signed handoff token and HTTP-redirect the
  browser to `https://<their-subdomain>/sso/handoff?token=...`.

### The handoff token

- **Per-tenant signing secret**, not one global shared secret. Generated at
  tenant-provisioning time (Phase 2's Management Plane integration already
  calls `POST /tenants` — the secret is generated here and passed through
  as a Helm value for that tenant's `nexwall-controller` release,
  alongside its existing secrets per `values.yaml`'s `secrets:` block in
  `nexwall-multi-tenant`). A single global secret would mean one leak
  compromises every tenant; per-tenant secrets bound the blast radius to
  one customer.
- **Very short TTL** (30–60s) and **single-use** (a `jti` claim, checked
  against a short-lived seen-token cache on the receiving side) — the token
  travels as a URL query parameter, which can leak via browser history,
  Referer headers, or logs, so it must be worthless quickly and unusable
  twice.
- Claims: customer's email/identity, role/permissions within that tenant,
  `aud` = the specific tenant, `jti`, short `exp`.

### Cross-repo dependency — this ADR does not complete the feature alone

`nexwall-controller`'s own auth (`api/methods/auth.go`) is entirely local
today — username/password + TOTP, no external IdP awareness at all. It
needs a **new endpoint** (e.g. `POST /sso/handoff`) that validates the
per-tenant secret + `jti`/`exp`, and on success mints a normal session token
via its existing `gin-jwt` mechanism — same output as today's login, just a
different, additional input. This is tracked in `nexwall-controller`'s own
`docs/adr/0001-sso-handoff-from-partner-program.md` (new ADR added there in
this same work session) — implementation of that endpoint is **not** part
of this repo and not yet done.

**Until that endpoint exists, this ADR's redirect only gets a customer to
the right URL — it does not yet skip their local login screen.** Land this
repo's redirect-to-subdomain behavior first (works standalone, useful on
its own as a "deep link" even before true SSO exists), then treat the
no-second-login behavior as a dependent follow-up once the other repo's
endpoint ships.

## Consequences / open items

- Phase 1 (this repo) needs: the routing-by-role logic in the token-exchange
  step, and — new requirement — a per-tenant secret needs storing here
  (added to whatever this repo stores about each Customer org).
- Phase 2 (Management Plane integration) needs: generate the per-tenant
  handoff secret at `POST /tenants` time and pass it through as a Helm
  value — a new field on the request this repo sends, which may mean a
  new field on `nexwall-multi-tenant`'s `management-plane-openapi.yaml`
  contract too (coordinate across repos, don't assume the field already
  exists there — verify against that contract before building against it).
- A nice synergy, not required now: `my`'s existing `impersonation.go`
  (support staff acting as a customer) is architecturally the same
  mechanism as this handoff — "let this identity act inside that
  customer's context." Worth revisiting whether Phase 4-era reseller
  support access reuses the same handoff-token code path rather than being
  built separately.
- Not solved here, flagged for later: a user who is both Nexwall staff and
  personally a customer (unlikely but possible), or a Customer org with
  multiple end-users. Phase 1 handles the single-role, single-tenant-user
  case; multi-role edge cases are explicitly deferred, not silently broken
  — revisit if/when a real user actually needs it.
