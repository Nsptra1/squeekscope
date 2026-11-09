<!-- SQUEEKCHAIN-VERIFY: pending -->

# SqueekScope — Squeekchain Round & Settlement Tracker
**Open-source developer tool** — a live monitoring dashboard for the Squeekchain testnet (Ethereum Sepolia), built with vanilla HTML/CSS/JS. Live demo: https://nsptra1.github.io/squeekscope/


SqueekScope is a read-only, single-page dashboard that tracks the live state of
**Squeekchain** — the hamster-powered proof-of-work toychain that settles on
Ethereum Sepolia — together with the **Squeekchain Genesis** community campaign.

The hamster runs on a wheel; each lap is a unit of work. Settled work completes
Squeek blocks, and completed blocks mint SQK to Squeek holders. SqueekScope
turns the raw public API feeds into the numbers that actually describe the
chain: how much SQK is being issued, how fast the hamster is working, how
regularly rounds settle, and how the Genesis leaderboard is shaping up.

## What it shows

- **Hamster status** — live state from the media feed when available, with an
  activity indicator inferred from settlement recency as a fallback.
- **Chain status** — indexed vs. safe block from the indexer, confirmation lag,
  API status and the active deployment release.
- **SQK issuance (derived, not raw dumps)** — total SQK minted across the
  fetched settlement window, average SQK per completed block, issuance rate
  (SQK/hour), settlement cadence, hamster work rate (laps/hour, laps/block),
  and lifetime totals (settlements, blocks, laps). Sums are computed with
  `BigInt` because SQK base units exceed float precision.
- **Settlements table** — recent settled rounds with block ranges, work added,
  SQK minted, timestamps, confirmation status and explorer links.
- **SQK-per-settlement chart** — hand-rolled canvas bar chart, no chart library.
- **Deployment contracts** — Core, SQK token, Mining, Collection and Staking
  addresses read from the API's deployment manifest, linked to the explorer.
- **Genesis community** — member count, total Aura, badges earned, verified
  projects, per-member averages, a top-5 leaderboard, and the top-5 share of
  all Aura in circulation.

The page auto-refreshes every 30 seconds. Each section fails independently:
if one feed is down, that section shows an "unavailable" note and the rest of
the page keeps working.

## Data sources (public, no auth, CORS-open)

| Feed | URL | Used for |
| --- | --- | --- |
| Events | `https://api.squeekchain.com/v1/events` | Chain status, confirmation lag, deployment manifest & contract addresses |
| Settlements | `https://api.squeekchain.com/v1/settlements` | Recent settlements, `sqkMintedBaseUnits`, work & block counts, lifetime summary |
| Live media | `https://media.squeekchain.com/live/current.json` | Live hamster state (best-effort; page falls back to settlement-based inference) |
| Community stats | `https://community.squeekchain.com/v1/community/stats` | Genesis members, total Aura, badges earned, projects verified |
| Leaderboard | `https://community.squeekchain.com/v1/community/leaderboard` | Top Genesis members |

Field notes, verified against the live APIs and the chain:

- SQK has **18 decimals** (checked on-chain against the token contract in the
  deployment manifest), so `sqkMintedBaseUnits / 1e18 = SQK`.
- The newest settlement in the feed may not carry `sqkMintedBaseUnits` yet;
  the page labels it "pending" and reports issuance coverage (e.g. 9 of 10).
- Under the current `funded-stream-q128-1` reward model, per-block issuance is
  a stream rate (≈3,674 SQK/block at the time of writing) — the page derives it
  from the data rather than hardcoding a block reward.
- Settlement transactions are linked to **Sepolia Etherscan**.

## How to run

It is one static file — no build step, no dependencies beyond Google Fonts.

```sh
# any static server works, e.g.:
python3 -m http.server 8000
# then open http://localhost:8000
```

## How to deploy

Drop `index.html` on any static host (GitHub Pages, Netlify, Vercel, IPFS…).
For GitHub Pages: push this repo, enable Pages on the default branch, done.
All paths are relative and all data is fetched client-side from the public
APIs, so the page works from any origin.

## Tech

Vanilla HTML/CSS/JS in a single file. No frameworks, no bundler, no wallet
connection, no tracking. Canvas chart rendered by hand with devicePixelRatio
scaling.

---

Built on the public Squeekchain APIs · Sepolia testnet · Not affiliated with the Squeekchain team
