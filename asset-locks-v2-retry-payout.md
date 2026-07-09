# A Retry-Payout Guarantee for Asset Locks v2

Proposed requirement for the Asset Locks v2 specification listed in the June 2026 Dash Core Group development update. One page, one ask, submitted while the draft is still in flight, which is the cheapest moment a guarantee like this will ever have.

**Plain words.** When credits leave Platform, the money arrives on the Core chain as a special withdrawal transaction with no inputs, already signed by a masternode quorum. Exchanges still wait about 2.5 minutes for a mined, ChainLocked block before crediting it, because of one loose end in the rules. A withdrawal that expires unmined can be retried, and nothing promises the retry pays the same place the same amount, or that a retry happens at all. Close that loose end with one short paragraph in Asset Locks v2 and wallets can credit withdrawals the moment they appear, with no Core consensus change and no new network message. With shielded pools scheduled to activate on or around July 12, 2026 per the July 9 development update, withdrawal speed is the protocol-side missing piece of an instant, private cash-out, and this is its cheapest fix.

## What the rules say today

Under [DIP-27](https://github.com/dashpay/dips/blob/master/dip-0027.md), an asset unlock transaction is special transaction type 9. It has zero inputs, a withdrawal index that must be unique, an explicit miner fee, and a signature from the Platform validator quorum. Nodes refuse it once the chain height exceeds its signing height by 48 blocks, it is not eligible for InstantSend, and it becomes final by inclusion in a ChainLocked block.

Authorization is therefore never the receiver's problem. A valid unlock in the mempool already carries quorum consent, and with no inputs there are no outpoints to double-spend. The only events that can change what the receiver ultimately gets are expiry followed by a retry, and abandonment. DIP-27 says an expired withdrawal "can be retried" and stops there. Nothing in the specification binds a retried transaction to the original payout, requires a retry to happen, or defines an abandoned state a receiver could detect.

## The requirement

Add one normative guarantee to Asset Locks v2, in three clauses:

> Platform fixes the payout script and amount for a withdrawal index at the moment it accepts the withdrawal request. Every quorum signing for that index MUST commit to exactly that payout script and amount. Signings for the same index may otherwise differ only in signing height, quorum, signature, and miner fee, and a fee adjustment MUST NOT be taken from the payout. A withdrawal index whose transaction expires unmined MUST either be re-signed until a transaction for it is mined or enter an explicitly defined abandoned state that receivers can detect.

The first two clauses may already describe how the implementation behaves. In that case v2 only needs to write the behavior down as a guarantee rather than an accident of the current code. The fee and liveness clauses are called out separately because DIP-27 itself names low Core fees as a reason an unlock may fail to be mined, so retries plausibly need fee headroom, and a payout guarantee without a liveness rule leaves the receiver unable to distinguish a slow retry from an abandoned withdrawal.

## What it buys

- Instant acceptance becomes pure receiving policy. A wallet or exchange that sees a valid quorum-signed unlock in the mempool can credit it immediately, because every signing for that index pays out identically and the index either confirms eventually or reaches a detectable abandoned state. Receivers MUST deduplicate credits by withdrawal index rather than by transaction id, since a re-signing changes the txid while paying the same recipient.
- Binding the payout to the index at request time, rather than to a "first signing", also removes any ambiguity from concurrent signings across quorum rotation. Any two valid transactions for the same index pay identically by construction, so the receiver never needs to know which signing came first or which will confirm.
- No Core consensus change and no new Core network message. Enforcement lives where withdrawals are created, in Platform's state transition and signing rules, which is exactly the surface Asset Locks v2 is already revising. The heavier alternative, a deterministic Core lock message binding index, txid, and payout, only becomes necessary if this guarantee cannot be made.
- The user-facing effect is a withdrawal that clears in seconds instead of minutes, arriving exactly when shielded balances make withdrawals the flagship flow.

## Asks

- Confirm whether the current Platform implementation already preserves the payout script and amount across unlock re-signings.
- State whether re-signings may adjust the miner fee, and confirm a fee change is never taken from the payout.
- Add the guarantee above, or its equivalent, to the Asset Locks v2 draft as a normative requirement, including the retry-or-abandon liveness clause.
- State in the same section whether receivers may treat a valid mempool unlock as payout-final and deduplicate by withdrawal index, so exchange integrators have one paragraph to point at.

## Context

This requirement is the load-bearing question of project P1 in [dash-ideas-scoping.md](dash-ideas-scoping.md), where the full analysis lives, including the fallback lock-message design for the case where the guarantee cannot hold, the mempool-eviction and fee questions the audit should also answer, and the child-transaction and light-client extensions that can be layered on later. Withdrawal speed is the protocol-side piece of private cash-out. The wallet-side piece, protecting the pool boundary from amount and timing correlation, is covered under P4 and P7 of the same document. The underlying idea, extending instant settlement to zero-input transactions, is due to Joel Valenzuela (TheDesertLynx).
