# Sentiment

Static site at https://longonlyanalyst.github.io/sentiment/. All content lives in `data.json`; `index.html` renders it client-side. To update the site, edit `data.json` only and push.

## data.json schema

- `title`, `subtitle` (string); `updatedAt` (ISO 8601 with offset, shown as "Last updated" in America/Chicago).
- `bigPicture`: `{ lead: string, body: string[] }`.
- `watchlist`: string[] of symbols. Symbols without a matching `tickers` entry show as "pending" (add new tickers here first).
- `tickers[]`: `{ symbol, company, earningsDate (YYYY-MM-DD), move, moveTone ("up"|"down"|"flat"|"unverified"), sentiment ("bullish"|"mixed"|"bearish"), sentimentLabel, topTheme, updatedAt (ISO), changed (string, may be ""), facts: [{label, value}], before, after, controversy (may be ""), posts: [{author, gist, engagement, url}] }`. Order `posts` by reach, highest first.
- `method` (string); `sources`: `[{ title, site, url }]`.

Rules: no emojis, no em dashes, spell out months in prose (Oct 8, not 10/8).
