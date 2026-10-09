# ADR 0003: Release on verification, not on a timer

**Status:** accepted

## Context

The simplest escrow releases funds after N days. Steam can reverse a trade after it looked
complete, and a timer would pay out regardless.

## Decision

A worker checks the real Steam trade state and the protection window before any transfer. A
timer only decides when to look again or when to give up and refund, never when to pay.

## Consequences

- Payout depends on evidence (Steam state, plus a TLS proof when used), so a reversed trade
  cannot be paid out by accident.
- More moving parts: a verification worker, retry logic, and a clear failure path to refund.
- Requires careful handling of every Steam state, including ones that are rare or hard to
  observe.
