# ADR 0001: Separate charges and transfers instead of destination charges

**Status:** accepted

## Context

Funds must be held for up to a week, until the Steam trade is verified, before reaching the
recipient.

## Options

1. **Destination charge**: Stripe moves the money to the recipient's connected account at charge
   time. Simple, but the transfer is immediate, so there is nothing to hold.
2. **Authorize now, capture later**: keeps funds on the payer's card. Authorizations expire
   around 7 days, the same window as the verification period.
3. **Separate charges and transfers**: capture on the platform, transfer to the recipient only
   after verification, or refund on failure.

## Decision

Option 3.

## Consequences

- The hold is real: money is captured and sits on the platform's Stripe balance, tracked by an
  internal escrow ledger.
- A refund after capture loses the original processing fee, so the platform fee is sized to
  cover it.
- The platform must control its payout schedule and reconcile ledger against balance.
