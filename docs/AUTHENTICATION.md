# Authentication Architecture

## Status

**Planned, not yet deployed.** The current repository contains static HTML and does not authenticate visitors.

## Components

- **Public TSO surface:** content intended for anonymous visitors.
- **Identity provider:** authenticates the human operator.
- **Server-side auth middleware:** protects `/ops/*` and authenticated `/api/*` routes.
- **Session store:** tracks short-lived authenticated sessions.
- **Authorization policy:** determines which authenticated identities may use Ops capabilities.
- **Protected APIs:** provide DAD/dispatch/system data only after authentication and authorization.

## Request flow

```text
Browser
  |
  +--> Public route --------------------------> Public content
  |
  +--> /ops/* or protected /api/*
             |
             v
        Auth middleware
          |       |
       no session valid session
          |       |
          v       v
        Login   Authorization
          |       |
          v       v
     Identity    Protected
     provider    resource
          |
          v
       Callback
          |
          v
     Validate + establish
     secure server session
```

## Token and credential rules

OAuth/OIDC client secrets, signing material, refresh tokens, PayPal credentials, and private service keys stay server-side.

The browser should receive only the minimum session material required to identify its authenticated session. Cookies should be `HttpOnly`, `Secure`, and `SameSite`.

Access tokens should be short-lived. Refresh tokens, when required, should be held server-side and rotated according to the identity provider's capabilities.

## DAD boundary

TSO Ops may consume DAD status through authenticated APIs. DAD's Job Authorization Object, Funding Gate, payment state adapter, evidence, and job records remain authoritative in DAD rather than being duplicated into static Ops HTML.

## Before production

The exact identity provider and deployment runtime must be selected before middleware implementation because a static host alone cannot enforce this server-side security model.
