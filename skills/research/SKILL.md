---
name: research
description: Produces a structured investment research note on a single stock ticker — current price, 52-week range, recent news, analyst consensus, and a thesis fit check against the user's investing criteria in portfolio.md. Output is appended to projects/investments/dashboard.md. Triggered when user says "research [TICKER]", "deep dive on [TICKER]", "look into [TICKER]", or "analyse [TICKER]".
---

# Research — Investment Deep-Dive

Produce a structured research note on a single ticker. Extract the ticker symbol from the user's message. Follow every step in order.

## Step 1 — Read investing context

Read `{BASE_PATH}/projects/investments/portfolio.md`.

Extract:
- Investing philosophy and criteria (from the Philosophy section)
- Whether the ticker is a current holding (check the holdings table)
- If held: cost basis, quantity, unrealised gain/loss

If portfolio.md does not exist or has no philosophy section, note "No investing criteria defined — add your philosophy to portfolio.md for thesis fit assessment." Continue with the research regardless.

## Step 2 — Web search: price and recent news

Run these searches:
1. `[TICKER] stock price today`
2. `[TICKER] news last 7 days`
3. `[TICKER] analyst consensus price target [current year]`

Extract:
- Current price
- 52-week high and low
- 3 most relevant recent developments (earnings, product news, macro headwinds, regulatory events, acquisitions)
- Analyst consensus rating and median price target

Never fabricate data. If a search returns no useful result, write "Not found" for that field.

## Step 3 — Assess thesis fit

Compare the stock against the user's investing philosophy from portfolio.md.

Rate as:
- **Strong fit** — aligns well with stated criteria, runway and thesis intact
- **Neutral** — mixed signals, thesis partially intact or unclear
- **Weak fit** — thesis broken or criteria no longer met

If no philosophy is defined, skip thesis fit rating and note it in the output.

Special cases:
- **Leveraged ETFs** (TQQQ, SOXL, etc.): note that standard thesis fit criteria don't apply — these are tactical instruments with daily rebalancing decay. Assess differently.
- **Near-zero positions** (-90% or worse): explicitly address whether the original thesis is dead, and flag the tax-loss harvesting option.

## Step 4 — Write the research note

Format the output exactly as:

```markdown
## Research: [TICKER] — [DD Month YYYY]

**Thesis fit:** [Strong / Neutral / Weak / N/A] — [one sentence why]
**Current price:** $[X] | 52-week range: $[low] – $[high]
**Position status:** [Holding X shares at $X cost basis, unrealised [+/-]% | Not held | Watchlist]
**Analyst consensus:** [rating] | Median target: $[X]

### Recent developments
- [Development 1 — date, one line]
- [Development 2 — date, one line]
- [Development 3 — date, one line]

### Bull case
[2 sentences: what has to go right for this to compound from here]

### Bear case
[2 sentences: what breaks the thesis or creates downside]

### Verdict
[Watch / Add / Hold / Trim / Pass] — [one sentence reasoning]

---
*Sources: [list URLs used]*
```

## Step 5 — Append and display

Append the research note to `{BASE_PATH}/projects/investments/dashboard.md`.

Display the note in chat.

---

## Notes

- Verdicts are assessments, not instructions. Frame them as "this looks like a Hold" not "you should Hold".
- Cite every factual claim with a URL in the Sources line.
- If the user asks to research a company by name rather than ticker (e.g. "research Apple"), resolve the ticker first before running searches.
