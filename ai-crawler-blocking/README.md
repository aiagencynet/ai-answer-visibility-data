# AI crawler blocking in robots.txt: 5,000 most-linked domains (November 2026)

One row per domain: what each site's robots.txt tells 15 AI and search crawlers (GPTBot, ClaudeBot, PerplexityBot, OAI-SearchBot, Googlebot and others), evaluated under RFC 9309, plus whether the site serves `/llms.txt`, a sitemap and a `Content-Signal` line.

- Report with tables, method and coverage: [aiagencynet.com/reports/ai-crawler-blocking-2026-11](https://aiagencynet.com/reports/ai-crawler-blocking-2026-11?utm_source=github&utm_campaign=e-b2-6)
- Version 2 (2026-11-27): the list was extended from the first 1,000 to the first 5,000 domains. Version 1, the first run over 1,000 domains, is kept unchanged in `2026-11/v1-top1000/`. See `CHANGELOG.md`.
- Checked: 2026-11-26, 11:26 to 17:37 UTC, in two runs from one cloud server
- License: **CC BY 4.0**, licensed by HATDOWN LLC (operator of aiagencynet.com). Domain names come from the Majestic Million (CC BY 3.0), see Attribution below.

## Key numbers

Shares use the 3,819 domains whose robots.txt we could read (3,661) or that have none (158: `/robots.txt` answered 404 or 410 and the homepage works). Filter `in_shares == true` to reproduce them.

| Crawler | Purpose (vendor docs) | Blocked | Share of 3,819 |
|---|---|---|---|
| GPTBot | OpenAI, model training | 741 | 19.4% |
| ClaudeBot | Anthropic, model training | 727 | 19.0% |
| PerplexityBot | Perplexity, AI search | 469 | 12.3% |
| OAI-SearchBot | OpenAI, AI search | 299 | 7.8% |
| Claude-SearchBot | Anthropic, AI search | 307 | 8.0% |
| CCBot | Common Crawl, training | 826 | 21.6% |
| Google-Extended | Google, training opt-out token | 640 | 16.8% |
| Googlebot | Google, search | 34 | 0.9% |
| Bingbot | Microsoft, search | 47 | 1.2% |

- 438 domains block GPTBot but leave OAI-SearchBot (the crawler OpenAI says it uses for ChatGPT search) allowed.
- 536 domains (14.0%) block at least one training crawler (GPTBot, ClaudeBot, Google-Extended, Applebot-Extended, CCBot, Bytespider or meta-externalagent) while leaving all three AI search crawlers (OAI-SearchBot, Claude-SearchBot, PerplexityBot) fully allowed.
- 582 of the 3,753 domains whose files we fetched (15.5%) answered `/llms.txt` with a non-HTML text file; 209 (5.5%) of the 3,819 have a `Content-Signal` line in robots.txt.

"Blocked" means robots.txt disallows both the homepage (`/`) and an ordinary page path for that crawler; "partial" means one of the two. Rules for specific folders such as `/admin` do not count as blocking.

## First run and extended run

| | First run (v1, top 1,000) | Extended run (v2, top 5,000) |
|---|---|---|
| Domains in the shares | 779 | 3,819 |
| GPTBot blocked | 21.7% | 19.4% |
| ClaudeBot blocked | 22.1% | 19.0% |
| PerplexityBot blocked | 17.5% | 12.3% |
| OAI-SearchBot blocked | 10.8% | 7.8% |

The top 5,000 include the first 1,000, and the rows for those 1,000 domains are identical in both files (v2 only adds the `checker_opted_out` column). The first-run figures still describe the first 1,000 domains. The shares over 5,000 are lower because the less-linked domains added in the extended run block these crawlers less often. Articles and posts that quote 21.7% for GPTBot cite version 1.

## Files

```
2026-11/
  ai-crawler-blocking-2026-11.csv   5,000 rows, alphabetical by domain (version 2)
  meta.json                         list edition, selection rule, request rule, the two runs
  v1-top1000/
    ai-crawler-blocking-2026-11.csv 1,000 rows, first run (version 1, unchanged)
    meta.json                       version 1 metadata
CHANGELOG.md                        what changed between versions
MANIFEST.json                       sha256 and size of each data file
LICENSE                             CC BY 4.0 legal code
```

A new monthly run adds a new `YYYY-MM/` folder. Earlier versions stay available: on GitHub as releases (`crawler-2026-11` is version 1, `crawler-2026-11-v2` is version 2), on Hugging Face in the commit history, on Kaggle as dataset versions.

## Columns

| Column | Meaning |
|---|---|
| `domain` | Host name, `www.` stripped |
| `checked_at_utc` | When this domain was checked |
| `in_shares` | `true` if the row counts in the published shares (readable robots.txt, or a real 404/410 with a working homepage) |
| `robots_state` | `ok`, `missing`, `refused` (HTTP 401/403), `could_not_check` (DNS, connection or timeout), `unreachable` (HTTP 5xx or 429), `redirects` (more than 3), `error_page` (redirected to an error page), `blocked_url` (an address we do not fetch, such as a private network) |
| `robots_http_status`, `robots_groups` | HTTP status of `/robots.txt` and number of user-agent groups parsed |
| one column per crawler (15) | `allowed`, `partial`, `blocked` or `unknown` |
| `<crawler>_rule` | Which rule applied: `named` (a group for that crawler), `wildcard` (`User-agent: *`), `none` (no group applies), `no_robots`, or the reason it is unknown (`refused`, `html_page`, `could_not_check`, ...) |
| `llms_txt`, `sitemap`, `content_signal` | `/llms.txt` returned a 2xx body that is not HTML (not checked against the llms.txt format); a same-site sitemap was found; robots.txt has a `Content-Signal` line |
| `homepage_http_status` | HTTP status of the homepage request |
| `checker_opted_out` | New in version 2. `true` for the 66 domains in the extended run (positions 1,001 to 5,000) whose robots.txt disallows our client (`aiagencynet-crawler-check` by name, or through `User-agent: *`); for them only robots.txt was fetched, so `llms_txt`, `sitemap` and `homepage_http_status` are empty. Always `false` for the first 1,000 rows: the first run did not apply this rule, so `false` there does not mean the site allows our client. Crawler verdicts are filled in for all rows. |

Rows with `robots_state = ok` but `<crawler>_rule = html_page` got an HTML page (often a bot challenge) instead of a robots.txt file; they are `unknown` and not in the shares.

## Quick start

```python
import csv

rows = list(csv.DictReader(open("2026-11/ai-crawler-blocking-2026-11.csv")))
shares = [r for r in rows if r["in_shares"] == "true"]
blocked = sum(r["GPTBot"] == "blocked" for r in shares)
print(blocked, "of", len(shares), "block GPTBot")   # 741 of 3819 block GPTBot
```

For the llms.txt and sitemap shares, also drop rows where `checker_opted_out == "true"`.

## Method

- **Domains.** The first 5,000 usable hosts of the Majestic Million (2026-11-26 edition), which orders domains by how many networks link to them, not by traffic. Removed: invalid and reserved names, adult and gambling sites, two hosts with no identifiable public site, and subdomains of a listed domain. We do not republish the list or its ranks; rows are alphabetical.
- **Requests.** Per domain, four or five GETs: `/robots.txt`, the homepage, `/llms.txt` and one same-site sitemap, identified as `aiagencynet-crawler-check/1.0` with a link to the tool. No crawling and no requests that pretend to be an AI crawler.
- **Two runs.** The first run checked list positions 1 to 1,000 and fetched all files for each reachable domain. The extended run checked positions 1,001 to 5,000 with an opt-out rule: robots.txt is read first, and when it disallows our client, nothing else is requested. That applied to 66 domains whose robots.txt disallows our client. To opt out: `User-agent: aiagencynet-crawler-check` / `Disallow: /`.
- **Rules.** RFC 9309: groups matched by crawler name (case-insensitive) and merged; a crawler with no named group follows `User-agent: *`; the longest matching rule wins and an `Allow` that ties a `Disallow` wins.

## Limitations

- robots.txt is a request that well-behaved crawlers follow, not access control. CDNs and firewalls can block crawlers that robots.txt allows, and the reverse.
- One server, one day. A site may serve different files to different clients. 1,181 of 5,000 domains are left out of the shares for reasons like that: 439 refused, 413 no connection, 203 HTML instead of robots.txt, 86 no working site or an error page, 23 server errors or HTTP 429, 10 redirect loops, 7 addresses we do not fetch.
- Counts of published rules only. They do not measure traffic, quality or AI visibility, and whether a crawler actually visited a site is unknown. Blocking a crawler is often a deliberate choice.
- ChatGPT-User and Perplexity-User fetch pages when a user asks; their vendors say robots.txt may not apply, so their `blocked` values describe the file, not behavior.

## Attribution and license

- This dataset: CC BY 4.0, licensed by HATDOWN LLC. Credit "aiagencynet.com AI crawler blocking report, November 2026" with a link to the [report](https://aiagencynet.com/reports/ai-crawler-blocking-2026-11?utm_source=github&utm_campaign=e-b2-6).
- Domain selection: [Majestic Million](https://majestic.com/reports/majestic-million) by Majestic, licensed [CC BY 3.0](https://creativecommons.org/licenses/by/3.0/). We used the list order to pick domains and changed it (filtered, deduplicated, re-sorted alphabetically, ranks removed).
- Site owners can ask for their domain to be removed through the contact form linked from the report; removed domains are dropped from the site and from the files of this dataset.
- Not affiliated with Majestic, OpenAI, Anthropic, Perplexity, Google, Microsoft or any site listed. aiagencynet also offers a free crawler checker and paid robots.txt fixes; nobody paid to be included or excluded.
