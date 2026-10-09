# Testing strategy

## Two lanes with different budgets

| Lane | Runs | Cost | Rules |
|---|---|---|---|
| **Gate tests** | Every commit (pre-commit hook) | Free, local | Deterministic, under 2 seconds per file group, never flaky |
| **Periodic evals** | Before shipping and nightly | Paid (model calls) | May vary run to run, but must clear a pass threshold |

Deterministic questions (arithmetic, state transitions, parsing, signatures) belong in code and
gate tests. Judgment-heavy output belongs in evals. If the same input must always give the same
answer, it is not allowed to live in a prompt.

## Rules I hold myself to

- A feature ships with its tests and evals in the same commit.
- A bug fix ships with a regression test, plus a check that the fix generalizes.
- No change without a named outcome: the behavior or metric it moves, and the trace it leaves
  (a log line, a metric, an eval score).
- Contracts between services are asserted on both sides. The verifier webhook signature has a
  shared test vector checked in Rust and in Node.

## What the suite covers

- **Backend**: money paths (cash-trade creation, cancellation, teardown concurrency, dispute
  resolution, payout), middleware (CSRF, rate limits, auth), workers, alerting.
- **Extension**: message contracts, manifest and build guards, log-buffer limits, a ban on unsafe
  DOM injection, the relay between background and offscreen contexts.
- **Frontend**: routing and redirects, i18n key coverage, CSP policy.
- **Verifier**: hardening tests proving removed routes stay removed.
- **Load**: k6 scenarios against the API.

## Concurrency tests

The riskiest bugs in a money system are races: two workers handling the same trade, a retry
overlapping a cancellation. Teardown and cancellation have dedicated concurrency tests that run
the competing paths together and assert that money moves exactly once.
