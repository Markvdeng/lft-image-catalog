# lft-image-catalog

Public host for LiveFootballTickets AI ad-creative images + the lookup
`catalog.json` that `feed-enricher-pro` reads to attach image URLs to feed
events (for Channable → Google Ads dynamic image assets).

This repo is **public on purpose** — Channable/Google fetch the image URLs
directly.

## Layout

```
catalog.json        # the lookup the enricher reads
clubs/              # club-spotlight images (one per team, reused across fixtures)
  <slug>-club-square.jpg     # 1:1 1200x1200, IP-safe crowd in club colours
```

## catalog.json contract

```json
{
  "clubs":    { "<slug>":           { "square": "<url>", "landscape": "<url>" } },
  "matchups": { "<home>-vs-<away>": { "square": "<url>", "landscape": "<url>" } }
}
```

- Keys MUST be the enricher's exact `*_slug_final` (e.g. `milan`, `as-roma`,
  `us-sassuolo`) — pull them from the live mapping sheet, not a naive slugify.
- `clubs[slug]` is reused on every fixture that team plays (home or away).
- `matchups["<home>-vs-<away>"]` is per-fixture; missing → enricher leaves the
  matchup columns blank (graceful).
- `square` = 1:1 (1200×1200), `landscape` = 1.91:1 (1200×628, added later).

Full spec + rollout: see `clients/liveticketgroup/image-feed-integration.md`
in the brain. Masters (2K PNG) + the generators live there too
(`scripts/build-club-configs.py`, `batch-generate-clubs.py`).

## Current coverage

- **Serie A**: 20 clubs (square) + 3 derby matchups (square + 1.91:1 landscape, drummer-VS style). More matchups + club landscape: TODO.
- Premier League / Ligue 1 / Eredivisie / World Cup: TODO (generate as needed;
  the enricher join is league-agnostic).
