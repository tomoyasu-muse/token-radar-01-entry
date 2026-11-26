# Token Radar — pre-token protocols ranked by implied value

[![make.fun bounty](https://make.fun/badge/token-radar-01.svg)](https://make.fun/bounty/token-radar-01)
[![rank](https://make.fun/badge/token-radar-01/tomoyasu-muse/token-radar-01-entry/rank.svg)](https://make.fun/bounty/token-radar-01)
[![reward](https://make.fun/badge/token-radar-01/tomoyasu-muse/token-radar-01-entry/reward.svg)](https://make.fun/bounty/token-radar-01)

A program that builds a token launch radar from a frozen DefiLlama
snapshot: 214 pre-token protocols ranked by implied value, each with
a seven-step farm plan, entered in the
[make.fun bounty "Build the best token launch radar"](https://make.fun/bounty/token-radar-01).

## What the radar does

The snapshot (14 November 2026, 758 protocols) records which protocols
have no token on record. The board covers all **40 watchlist**
protocols — every protocol with at least $50M of TVL and no token
recorded — plus **174 early protocols** below that floor, picked by
implied value rather than by size alone, so a fast-growing $15M
protocol can outrank a flat $45M one. **32 live-token protocols** are
set aside with their market caps, never mixed into the ranking.

## The model (radar-2)

Implied value follows only from the snapshot's own figures, in five
steps any reader can check in the dashboard's detail view:

- **Tier multiple** — TVL under $100M: 0.45x; $100M–$1B: 0.32x;
  over $1B: 0.22x. A launch multiple compresses as TVL grows.
- **Growth** — 7-day TVL change clamped to −60%…+120%. Gains count
  at 0.7, losses at 1.0: a falling TVL hurts a launch case more
  than a rising one helps it.
- **Maturity** — 0.55 + 0.45 × √(age / 1095 days). Age is the time
  since the protocol was first listed: the best record of how long
  it has run with no token. Unknown age uses 365 days.
- **Breadth** — 1 + 0.04 per extra chain, capped at 1.16.
- **Category** — Dexs and Lending 1.08x, Yield and Derivatives
  1.06x, CDP, RWA, curators and collateral markets 1.04x,
  everything else 1.00x, Payments 0.97x, Chains 0.95x, bridges 0.92x.

`implied = round(TVL × tier × growth × maturity × breadth × category)`

No protocol name is special-cased. The dashboard recomputes every
row from the same figures and says so in each detail view.

Rank 1 is **Steakhouse Financial** at $619.7M implied ($2.75B TVL,
−2.19% in 7 days, 1.5 years with no token, live on 11 chains),
followed by Gauntlet ($377.3M) and Veda ($339.4M).

## Farm plans

Every row carries seven steps derived from its own category, main
chain, TVL and age: a fresh wallet, the category's main action
(supply, provide liquidity, bridge, stake, mint, …), a second
qualifying action of a different kind, a weekly cadence, a hold of
30–60 days, a points-page check that never pays to claim, and a
farm log with an unwind in reverse. Gas follows the main chain
(Ethereum about $8, Solana about $0.02, L2s about $1), capital
follows TVL ($60–$300), the hold follows age.

## How it works

`main.ts` (Node 22, standard library only) reads
`/input/snapshot.json`, scores the board with `lib/model.ts`,
writes `/output/board.json` and `/output/data.js`, copies
`dashboard/` and `screenshots/` to `/output`, and writes
`/output/dossier.json` with the vendored `vendor/makefun-board.ts`.

The dashboard is a static page with no network: a top-three
spotlight, a sortable and filterable board (search, category,
chain, minimum TVL, pre-token / live-token tabs), a detail view
for every protocol with its model breakdown and farm plan, and a
methodology panel with a worked example for rank 1.

Run it locally:

```sh
mkdir -p out
MAKEFUN_OUTPUT_DIR=out node main.ts --input <snapshot.json>
```

A protocol on this board **records no token** — that is what the
snapshot says, never that it has none. Implied value is the model's
own claim, never a price and never a prediction that a token will
launch, or when.

## License

MIT — see [LICENSE](LICENSE).
