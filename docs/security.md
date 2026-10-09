# Security model

<!-- PUBLISH GATE: only describe controls that are in place in production today.
     Do not list open findings or anything exploitable. Re-read before every update. -->

## Threat model, in one page

| Asset | Who might attack it | Main concern |
|---|---|---|
| User funds in escrow | A dishonest counterparty, a compromised account | Release without a real delivery, refund abuse |
| User Steam credentials | Malware, a malicious page, a rogue extension build | Token theft |
| The backend and its data | Anyone on the internet | Injection, auth bypass, abuse of internal endpoints |
| The extension's integrity | Forks, repackaged builds | Users installing a modified copy |

## Principles I apply

1. **Nothing secret in the client.** Anything shipped to a browser is readable. Secrets live on
   the server. Where the client must hold a key, it is a public key or a short-lived token.
2. **Trust the proof, not the party.** Release of money depends on a verified transcript and on
   independent Steam state checks, never on a claim from either user.
3. **Defense at every boundary.** Strict CSP, CSRF protection, per-route rate limiting, input
   sanitization, schema validation, country allowlist, age verification.
4. **Least privilege on the server.** Services bind to loopback, internal routes require an
   internal credential, production runs under a non-root account with a scoped command set.
5. **Replay and tampering resistance.** Signed payloads carry server-issued nonces. Webhooks are
   HMAC-signed and timestamped.
6. **Encrypted at rest on the client.** The extension stores the user's Steam token encrypted and
   rotates it on a short interval. Logs are bounded and redact credentials before writing.
7. **Reduce what a bug can cost.** Money movement is idempotent, ledgered and reconciled, so a
   defect becomes an alert rather than a loss.

## Process

- I commissioned a structured security review of my own product and track every finding to a fix
  with a regression test.
- Every bug fix ships with a test that would have caught it.
- Dependency overrides pin patched versions of transitive packages.
- Deployment is scripted, key-only SSH, with documented rollback.

## What open-sourcing would and would not change

The extension is already delivered to browsers, so its code is inspectable today. I keep the
source private for product and anti-fraud reasons, and I publish this write-up so the design can
be judged on its reasoning.
