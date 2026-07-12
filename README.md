# Dash Scoping Notes

Decision-ready scoping documents for proposed Dash improvements across Core, Mobile, and the Evolution Platform. The goal is to hand implementers analysis they can act on directly, each project comes with a verdict, a sizing, its open design questions, and the primary sources behind every load-bearing claim.

## Contents

- [dash-ideas-scoping.md](dash-ideas-scoping.md) ([PDF](dash-ideas-scoping.pdf)), four projects (D1-D4) scoped against four roadblocks (pooled masternodes, speed of capital movement to and from Platform, DashPay usability, privacy). Headline recommendation, an audit-confirmed path to instant private credit withdrawals (D1), a batch-rotation wallet-privacy policy held as a contingency (D2), and private payments to any username (D3).
- [asset-locks-v2-retry-payout.md](asset-locks-v2-retry-payout.md) ([PDF](asset-locks-v2-retry-payout.pdf)), co-authored with [Joel Valenzuela](https://github.com/thedesertlynx), a one-page upstream request distilled from D1. It asks the in-flight Asset Locks v2 specification to guarantee that every quorum signing for a withdrawal index commits to the same payout, with a retry-until-mined liveness rule, which makes instant acceptance of Platform withdrawals a receiving-policy change.
- [d0-audit-retry-payout.md](d0-audit-retry-payout.md) ([PDF](d0-audit-retry-payout.pdf)), the source-code audit behind D1. It reads dashpay/platform and dashpay/dash at pinned commits and finds the retry-payout invariant already holds as a Platform-layer property, so instant withdrawal acceptance is a receiving-policy change, and surfaces two withdrawing-user edge cases (no refund on a permanently stuck withdrawal, and a fixed fee that can strand a low-fee withdrawal) for the spec to close.
- [d3-payment-code-design.md](d3-payment-code-design.md) ([PDF](d3-payment-code-design.pdf)), the buildable-today design for D3, paying a DashPay username who is not yet a contact via a published payment code (BIP47-style) with the notification carried as a Platform document instead of an on-chain transaction. Resolves the key-separation, recovery, and graph-visibility questions.

## Method

Claims are checked against primary sources (the [dashpay/dips](https://github.com/dashpay/dips) and [bitcoin/bips](https://github.com/bitcoin/bips) repositories, Dash Core source, live pool data), and each document goes through multiple rounds of independent adversarial review before publication, with every disputed claim adjudicated against those sources. Revision notes at the top of each document record what changed and why.

## Status

Living documents. Verdicts and sizings get revised as designs are audited and prototypes report numbers, and each revision lands as its own commit so changes are diffable.

Issues and pull requests are welcome, especially corrections with a primary source attached.

## Credits

The originating proposals and the roadblock framing behind these documents come from [Joel Valenzuela](https://github.com/thedesertlynx) (TheDesertLynx), business development and marketing lead for the Dash DAO. The scoping, the verdicts, and any errors are the maintainer's.

## License

MIT. Use anything here freely, attribution appreciated but not required.
