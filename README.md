# Sentiment

A real-time sentiment tracker for any public company: https://longonlyanalyst.github.io/sentiment/

Each read looks back about 3 to 5 days, names the real driver of the stock move, and then summarizes how credible, money-moving voices on X are reading it. Those voices are sell-side and buy-side analysts, fund managers, respected fundamental investors, reputable financial journalists, and company executives or IR. Memes, hype, recap bots and retail noise are excluded.

## How the site behaves

- The page starts blank: a search box and "Type a company to get a read". Nothing shows until you look something up.
- Recent lookups are kept in your browser (localStorage) and start empty.
- A tracked company or ticker opens its read right away.
- For an untracked one, the page posts the request to Formspree (`https://formspree.io/f/mvkkbrnl`, subject "Sentiment request: SYMBOL") and shows "Searching, this takes a few minutes". It then checks data.json every 20 seconds until that ticker appears with an `updatedAt` later than the request. At that point it shows "Ready, tap to refresh" and adds the ticker to Recent lookups. It stops after about 15 minutes.
- Every read has a "Refresh this read" button. It posts with subject "Sentiment refresh: SYMBOL" and follows the same searching flow.
- Reads more than 3 days old are labeled "Stale, last updated October 24" (using that read's own date).

## Updates: on request only, no timers

Nothing refreshes on a schedule. A read is created or updated only when Zane asks:
- in chat, or
- through the site form. Formspree emails womp+sentiment@mail.grokbot.com, which triggers an email-triggered routine to research the ticker, update data.json, and push.

## data.json schema

### Top level

- `title`, `subtitle`
- `updatedAt`: ISO time with offset
- `bigPicture {lead, body[]}`
- `watchlist[]`
- `tickers[]`
- `method`
- `sources[] {title, site, url}`

### Ticker fields

- `symbol`, `company`
- `driver {type, title, date}`: `type` is one of Earnings, Equity raise, Product, Guidance, Analyst or regulatory, M&A, Other. `date` is YYYY-MM-DD.
- `move`: text with dates. `moveTone`: up, down or flat.
- `sentiment`: bullish, bearish or mixed. `sentimentLabel` is its display form. `verdict` is a one-line call with its reason.
- `topTheme`
- `updatedAt`: ISO time with offset. Used for the as-of line and the stale label.
- `changed`: what changed since the previous read.
- `overview`: shown as "Overall picture". Either an array of strings or the object below.
- `whatHappened`: what happened in the window, with dated figures.
- `howReading`: how credible investors are reading it.
- `facts[] {label, value}`: each value carries its date.
- `controversy`
- `posts[]`: see below.
- `addedAt` (optional)

### overview object

- `lean`
- `camps[] {name, argues}`
- `agree`
- `split`
- `shift[] {date, note}`
- `hinges`

### posts fields

- `url`, `author`, `gist`
- `date`: ISO with offset, from X `created_at`
- `views`: `impression_count`, or null, which displays as "views n/a"
- `likes`, `reposts`: from X `public_metrics`
- `excerpt`: a short, trimmed excerpt of the real post text
- `engagement`: a display string

Posts are sorted by views, then likes. Metrics come only from X `public_metrics` and `created_at`.

## Research instructions

1. Find the driver from the last 3 to 5 days: what actually moved the stock, with its date. Corroborate it and the move with free news sources only, no paywalls. Mark anything you cannot confirm as unverified.
2. Search X with the public read tools by cashtag and company name (start_time about 5 days back, `min_likes` around 50 or 10K+ views). Keep posts from credible voices only.
3. Fetch the chosen posts by ID to verify URLs and record `created_at` and `public_metrics`. Never make up numbers, quotes or links. Never post, reply, like or follow.
4. Write the overview: the lean, the camps and what each argues, where they agree and split, how the read shifted by date, and what it hinges on. Then write `whatHappened`, `howReading`, the verdict, `changed`, and 2 to 5 posts.
5. Style: no emojis, no em dashes, months spelled out.
6. Set the ticker's `updatedAt` and the top-level `updatedAt`, validate the JSON, then commit and push.
