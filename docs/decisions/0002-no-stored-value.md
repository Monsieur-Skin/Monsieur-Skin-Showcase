# ADR 0002: No stored value, no wallet

**Status:** accepted

## Context

A balance users can top up and spend is the obvious way to simplify payments. It also turns the
platform into something much closer to a regulated payment institution.

## Decision

No rechargeable balance. Every movement of money is tied to one transaction. Refunds return to
the original payment method.

## Consequences

- Regulatory surface stays small: the platform orchestrates payments but does not hold a user's
  money as a standing balance.
- Each trade pays its own processing fees, with no netting across trades.
- Refund logic is simpler to explain and audit: the money goes back where it came from.
