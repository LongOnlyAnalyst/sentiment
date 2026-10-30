# Sentiment

Static site at https://longonlyanalyst.github.io/sentiment/. All content lives in `data.json`; `index.html` renders it client-side. To update the site, edit `data.json` only and push.

## data.json schema

- `title`, `subtitle` (string); `updatedAt` (ISO 8601 with offset, shown as "Last updated" in America/Chicago).
- `bigPicture`: `{ lead: string, body: string[] }`.
- `watchlist`: string[] of symbols. Symbols without a matching `tickers` entry show as "pending" (add new tickers here first).
- `tickers[]`: `{ symbol, company, earningsDate (YYYY-MM-DD), move, moveTone ("up"|"down"|"flat"|"unverified"), sentiment ("bullish"|"mixed"|"bearish"), sentimentLabel, topTheme, updatedAt (ISO), changed (string, may be ""), facts: [{label, value}], before, after, controversy (may be ""), posts: [{author, gist, engagement, url}] }`. Order `posts` by reach, highest first.
- Each ticker may carry `addedAt` (ISO). The table and sections sort newest `addedAt` first; tickers added in the last 7 days get a New tag.
- `requests`: `{ repo, label, show }`. The page lists open issues with that label as Pending requests (public GitHub API, hidden if rate-limited).
- `method` (string); `sources`: `[{ title, site, url }]`.

Rules: no emojis, no em dashes, spell out months in prose (Oct 8, not 10/8).

## Handling requests

The search box lets Zane look up any ticker or company. If it is not tracked, the page offers "Request a read on TICKER", which opens a prefilled GitHub issue titled "Sentiment request: TICKER" with the label `request`.

On every run (and when asked), the Sentiment bot:

1. Lists open issues: `gh issue list -R LongOnlyAnalyst/sentiment --label request --state open`.
2. For each, researches the ticker the same way as the existing ones: X search around the latest earnings call (before and after the print), high-signal professional accounts only, posts ranked by reach, no memes or low-quality retail noise.
3. Adds the ticker to `tickers[]` (with `addedAt` set to now) and to `watchlist`, updates `updatedAt`, then commits and pushes to main.
4. Comments on the issue with a link to `https://longonlyanalyst.github.io/sentiment/#t-TICKER` and closes it.
