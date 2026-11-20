# Token Launch Radar

[![make.fun](https://make.fun/badge/token-radar-01.svg)](https://make.fun/bounty/token-radar-01)
[![rank](https://make.fun/badge/token-radar-01/Akbar-Lab-Droid/token-launch-radar/rank.svg)](https://make.fun/bounty/token-radar-01)
[![reward](https://make.fun/badge/token-radar-01/Akbar-Lab-Droid/token-launch-radar/reward.svg)](https://make.fun/bounty/token-radar-01)

A radar for protocols whose token does not exist yet: it ranks 198 pre-token protocols by an
implied token value, and shows a reader how to farm each one. Built for the
[make.fun](https://make.fun/bounty/token-radar-01) bounty "Build the best token launch radar".

## The model (radar-2)

Implied value is priced from the market itself, not a hardcoded multiple: for each category, the
median market-cap-to-TVL ratio of the live-token protocols in that category (three or more of
them, otherwise the global median), clamped to [0.05, 1.5]. A protocol's implied value is its TVL
at its category's multiple, moved by its seven-day growth, by how long it has run with no token
on record, and by how many chains it runs on:

```
implied = round(tvl_usd x category_multiple x growth x maturity x chains)
```

No protocol name is special-cased: the same function scores every row, and the dashboard
recomputes each row from the same figures and checks the number `board.json` carries.

The board covers the snapshot's 40-protocol watchlist (every protocol with at least $50M of TVL
whose record shows no token) plus every other rankable pre-token protocol the snapshot records —
158 early rows below the floor. Rankable means the record shows no token and the protocol is not
a wrapped, staked or bridge product of a token that already trades (the same rule that produced
the watchlist: a wrapped or staked category, a version-marked name, a whole word "wrapped",
"staked" or "staking" in the name, or a name beginning with an exchange's name). Live-token
protocols are set aside with their market caps shown; they price the comps, they are never ranked
as pre-token.

"Records no token" means the record shows none — it does not prove the protocol has none, and
implied value is a model, not a price.

## The dashboard

`dashboard/` is a static page with no network: a sortable, filterable board of the 313 rows, a
detail view for each protocol (its model breakdown and its six-step farm plan, each step with a
cost and a time), the category comps table, and the live-token view. Open `dashboard/index.html`
beside the generated `data.js`.

## Run it

```sh
mkdir -p out && MAKEFUN_OUTPUT_DIR=out node main.ts --input <snapshot.json|directory>
```

`main.ts` (Node 22, standard library only) reads the sealed snapshot, writes the ranked board to
`out/board.json`, the dashboard's data to `out/data.js`, copies `dashboard/` and `screenshots/`
to `out/`, and writes `out/dossier.json` with the vendored make.fun library.
