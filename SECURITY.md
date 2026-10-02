# TSO Ops Security Baseline

## Current state

This repository currently serves a static prototype. The HTML under `/ops` is not an authentication boundary. Until server-side access control is deployed, no customer records, DAD job data, payment credentials, private operational telemetry, or SB/SeeBoard/SkiBoard material may be exposed through these pages.

## Authentication target

The protected request path is:

1. Browser requests a protected `/ops/*` or `/api/*` resource.
2. Server-side middleware checks a secure session.
3. Unauthenticated requests are redirected to the identity provider/login flow.
4. The callback is validated server-side.
5. A short-lived session is established using an `HttpOnly`, `Secure`, `SameSite` cookie.
6. Authorization is checked before protected content or APIs are returned.
7. Logout invalidates the server-side session.

## Credential handling

Secrets belong in the deployment platform's secret/environment store, never in Git or browser JavaScript. This includes identity-provider secrets, PayPal secrets, private API keys, signing keys, refresh tokens, customer credentials, and DAD service credentials.

Do not store long-lived bearer or refresh tokens in localStorage.

## Data boundary

The public TSO website and authenticated TSO Ops application are separate trust zones. DAD remains a separate application/domain component and should integrate through authenticated APIs rather than being merged into static Ops pages.

SB/SeeBoard/SkiBoard information is excluded from this repository unless explicitly approved for release.

## Incident rule

If a real secret is ever committed, removing the file is not sufficient. Revoke/rotate the credential first, then clean repository history as a separately approved operation.
