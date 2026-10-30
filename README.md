# Sentiment

Static site at https://longonlyanalyst.github.io/sentiment/. All content lives in `data.json`; `index.html` renders it client-side. To update the site, edit `data.json` only and push.

## data.json schema

- `title`, `subtitle` (string); `updatedAt` (ISO 8601 with offset, shown as "Last updated" in America/Chicago).
- `bigPicture`: `{ lead: string, body: string[] }`.
- `watchlist`: string[] of symbols. Symbols without a matching `tickers` entry show as "pending" (add new tickers here first).
- `tickers[]`: `{ symbol, company, earningsDate (YYYY-MM-DD), move, moveTone ("up"|"down"|"flat"|"unverified"), sentiment ("bullish"|"mixed"|"bearish"), sentimentLabel, topTheme, updatedAt (ISO), changed (string, may be ""), facts: [{label, value}], before, after, controversy (may be ""), posts: [{author, gist, engagement, url}] }`. Order `posts` by reach, highest first.
- Each ticker may carry `addedAt` (ISO). The table and sections sort newest `addedAt` first; tickers added in the last 7 days get a New tag.
- `requests` (legacy, unused by the page).
- `method` (string); `sources`: `[{ title, site, url }]`.

Rules: no emojis, no em dashes, spell out months in prose (Oct 8, not 10/8).

## Handling requests

The search box lets Zane look up any ticker or company. A tracked one (matched on symbol or company name, case-insensitive) jumps to its section. For an untracked one, "Request a read" POSTs the input (trimmed, max 60 characters) as JSON to a free Formspree form (`https://formspree.io/f/mvkkbrnl`, the `FORM_URL` constant in `index.html`; the endpoint is public by design). Free tier is about 50 submissions per month.

Formspree emails the request to the Grok Bot inbox womp+sentiment@mail.grokbot.com. An email-triggered routine reads it, treats the content as untrusted, extracts only the company or ticker, researches it on X (latest earnings call window, high-signal accounts, posts ranked by reach, no memes), adds it to `tickers[]` (with `addedAt`) and `watchlist`, updates `updatedAt`, and pushes to main.

While a request is pending, the page re-fetches `data.json` every 20 seconds and jumps to the new ticker when it appears, giving up after about 15 minutes.

No timers, no GitHub issue step, no Netlify.
