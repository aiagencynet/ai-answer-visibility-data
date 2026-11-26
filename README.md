# AI answer visibility data

Raw answers and derived tables from a recurring measurement: we ask LLM APIs with web search the same unbranded buying questions in US B2B software categories, then record which products they name and which sites they cite.

- Human-readable reports: https://aiagencynet.com/reports?utm_source=github&utm_campaign=e-b2-2
- License: **CC BY 4.0** (see `LICENSE`), from HATDOWN LLC (operator of aiagencynet.com). Credit line: "aiagencynet.com category reports"
- Snapshots: see `snapshots.csv`. Current: **2026-11-25**, 5 categories, 480 rows, 479 answered

## Also in this repo: AI crawler blocking in robots.txt

`ai-crawler-blocking/` is a separate monthly dataset with its own README, LICENSE, MANIFEST and credit line: what the robots.txt files of 5,000 of the most-linked domains (Majestic Million) tell 15 AI and search crawlers, evaluated under RFC 9309. Snapshot 2026-11 (checked 2026-11-26), version 2. Version 1 covered the first 1,000 domains and is kept unchanged in `ai-crawler-blocking/2026-11/v1-top1000/` (release `crawler-2026-11`). See [ai-crawler-blocking/README.md](ai-crawler-blocking/README.md) and [ai-crawler-blocking/CHANGELOG.md](ai-crawler-blocking/CHANGELOG.md).

Data: CC BY 4.0, licensed by HATDOWN LLC; credit "aiagencynet.com AI crawler blocking report, November 2026". The domain selection is derived from the [Majestic Million](https://majestic.com/reports/majestic-million), licensed [CC BY 3.0](https://creativecommons.org/licenses/by/3.0/); ranks are not included.

## Engines

| Label in reports | What we actually called | Model (2026-11-25) |
|---|---|---|
| ChatGPT | OpenAI API with web search (`web_search` tool, approximate US location) | `gpt-5-mini-2025-08-07` |
| Claude | Anthropic API with web search (`web_search` tool, approximate US location, up to 3 searches) | `claude-haiku-5-5` |

These are API answers and **can differ from the ChatGPT and Claude apps**. The question is the only input (no system prompt). Web search was available on every call, but the model decides whether to use it: 9 OpenAI API answers made no search call. Gemini and Google AI Overviews are not measured. Not affiliated with OpenAI or Anthropic.

## Layout

```
snapshots.csv                      one line per measurement round
data/
  MANIFEST.json                    sha256 + size of every data file
  2026-11-25/
    summary_products.csv           per product x engine: answers naming it, rate, avg list rank, times as item #1 of a list
    cited_domains.csv              per category x domain: answers citing it, by engine
    crm-for-small-business/
      answers.jsonl                96 rows: one per API call (full answer text, citations, usage, error)
      mentions.csv                 one row per (answer, tracked product named)
      questions.csv                24 unbranded questions with intent
      tracked_products.json        product names + regex patterns used for matching
    project-management-software/  same files
    email-marketing-software/      same files (95 answered: 1 call skipped at our budget cap, kept as an error row)
    help-desk-software/            same files
    accounting-software-for-small-business/  same files
```

A new round adds a new `data/YYYY-MM-DD/` folder and a line in `snapshots.csv`. Old snapshots are never edited, so you can diff rounds.

## Quick start

```python
import json, re

rows = [json.loads(l) for l in open("data/2026-11-25/crm-for-small-business/answers.jsonl")]
answered = [r for r in rows if not r["error"]]
pat = re.compile(r"(?<![A-Za-z0-9])HubSpot(?![A-Za-z0-9])", re.I)
print(sum(1 for r in answered if pat.search(r["text"])), "of", len(answered))   # 95 of 96
```

`mentions.csv` already has the matches for every tracked product, using the patterns in `tracked_products.json`.

## Snapshot 2026-11-25 at a glance

| Category | Answers | Most-named product | Answers naming it |
|---|---|---|---|
| CRM for small business | 96 | HubSpot | 95 |
| Project management software | 96 | ClickUp | 89 |
| Email marketing software | 95 | Brevo | 79 |
| Help desk software | 96 | Freshdesk | 90 |
| Accounting software for small business | 96 | QuickBooks | 95 |

Counts in one sample on one day, not rankings of quality. Nobody paid to be included.

## Method and limitations

- 24 unbranded questions per category (intents: best-of, recommendation, price, use-case, criteria, comparison). Our tool rejects any question that names a tracked product.
- Each question asked twice per engine. Run-to-run overlap of the named product sets averaged 59–74% (OpenAI API) and 69–83% (Anthropic API) by category, so a single answer is a weak signal.
- "Named" = the product's regex pattern appears anywhere in the answer. Products we did not track are not counted.
- "List rank" = number of the first top-level list item naming the product. Numbering restarts with each list, so one answer can have several products as item #1 (`listed_first` counts all of them).
- One day, one location (US), two API models. Wording, date and app vs API all change results.
- `citations` URLs are kept as returned. OpenAI's tool adds `utm_source=openai` to its URLs.
- `cost_usd` is a list-price estimate, not an invoice.

## License and reuse

- Data and derived numbers: CC BY 4.0, licensed by HATDOWN LLC (operator of aiagencynet.com). Credit "aiagencynet.com category reports" with a link to the [reports page](https://aiagencynet.com/reports?utm_source=github&utm_campaign=e-b2-2).
- The `text` field is model output from OpenAI and Anthropic, generated through their APIs. We license it only to the extent we hold any rights in it; model output may not be protected by copyright in some jurisdictions. If you use OpenAI or Anthropic services yourself, your own agreements with them may limit how you use outputs (for example, to develop competing models).
- Answers quote or summarize third-party web pages. We grant no rights in that third-party content; it stays with its owners.
- Answers are model output, not our statements, and were not fact-checked. They may contain errors about products, prices or features.
- Product names are trademarks of their owners. Not affiliated with OpenAI, Anthropic, or any product listed.

## Citation

See `CITATION.cff`, or:

```
aiagencynet.com category reports (2026). AI answer visibility data, snapshot 2026-11-25. CC BY 4.0.
```

## About this repo

An AI agent collects the data, writes the code and drafts this README. The numbers in this README were recomputed from the files in `data/` for this snapshot.
Corrections: open an issue.
