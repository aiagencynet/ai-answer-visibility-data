# AI crawler blocking in robots.txt: 1,000 most-linked domains (November 2026)

One row per domain: what each site's robots.txt tells 15 AI and search crawlers (GPTBot, ClaudeBot, PerplexityBot, OAI-SearchBot, Googlebot and others), evaluated under RFC 9309, plus whether the site serves `/llms.txt`, a sitemap and a `Content-Signal` line.

- Report with tables, method and coverage: [aiagencynet.com/reports/ai-crawler-blocking-2026-11](https://aiagencynet.com/reports/ai-crawler-blocking-2026-11?utm_source=github&utm_campaign=e-b2-6)
- Checked: 2026-11-26, 11:26 to 11:38 UTC, from one cloud server
- License: **CC BY 4.0**, licensed by HATDOWN LLC (operator of aiagencynet.com). Domain names come from the Majestic Million (CC BY 3.0), see Attribution below.

## Key numbers

Shares use the 779 domains whose robots.txt we could read (757) or that have none (22: `/robots.txt` answered 404 or 410 and the homepage works). Filter `in_shares == true` to reproduce them.

| Crawler | Purpose (vendor docs) | Blocked | Share of 779 |
|---|---|---|---|
| GPTBot | OpenAI, model training | 169 | 21.7% |
| ClaudeBot | Anthropic, model training | 172 | 22.1% |
| PerplexityBot | Perplexity, AI search | 136 | 17.5% |
| OAI-SearchBot | OpenAI, AI search | 84 | 10.8% |
| Claude-SearchBot | Anthropic, AI search | 95 | 12.2% |
| CCBot | Common Crawl, training | 191 | 24.5% |
| Google-Extended | Google, training opt-out token | 142 | 18.2% |
| Googlebot | Google, search | 12 | 1.5% |
| Bingbot | Microsoft, search | 13 | 1.7% |

- 83 domains block GPTBot but leave OAI-SearchBot (the crawler OpenAI says it uses for ChatGPT search) allowed.
- 95 domains (12.2%) block at least one training crawler (GPTBot, ClaudeBot, Google-Extended, Applebot-Extended, CCBot, Bytespider or meta-externalagent) while leaving all three AI search crawlers (OAI-SearchBot, Claude-SearchBot, PerplexityBot) fully allowed.
- 120 domains (15.4%) answered `/llms.txt` with a non-HTML text file; 33 (4.2%) have a `Content-Signal` line in robots.txt.

"Blocked" means robots.txt disallows both the homepage (`/`) and an ordinary page path for that crawler; "partial" means one of the two. Rules for specific folders such as `/admin` do not count as blocking.

## Files

```
2026-11/
  ai-crawler-blocking-2026-11.csv   1,000 rows, alphabetical by domain
  meta.json                         list edition, selection rule, request rule, run window
MANIFEST.json                       sha256 and size of each data file
LICENSE                             CC BY 4.0 legal code
```

A new monthly run adds a new `YYYY-MM/` folder. Old months are not edited.

## Columns

| Column | Meaning |
|---|---|
| `domain` | Host name, `www.` stripped |
| `checked_at_utc` | When this domain was checked |
| `in_shares` | `true` if the row counts in the published shares (readable robots.txt, or a real 404/410 with a working homepage) |
| `robots_state` | `ok`, `missing`, `refused` (HTTP 401/403), `could_not_check` (DNS, connection or timeout), `unreachable` (HTTP 5xx or 429), `redirects` (more than 3), `error_page` (redirected to an error page) |
| `robots_http_status`, `robots_groups` | HTTP status of `/robots.txt` and number of user-agent groups parsed |
| one column per crawler (15) | `allowed`, `partial`, `blocked` or `unknown` |
| `<crawler>_rule` | Which rule applied: `named` (a group for that crawler), `wildcard` (`User-agent: *`), `none` (no group applies), `no_robots`, or the reason it is unknown (`refused`, `html_page`, `could_not_check`, ...) |
| `llms_txt`, `sitemap`, `content_signal` | `/llms.txt` returned a 2xx body that is not HTML (not checked against the llms.txt format); a same-site sitemap was found; robots.txt has a `Content-Signal` line |
| `homepage_http_status` | HTTP status of the homepage request |

Rows with `robots_state = ok` but `<crawler>_rule = html_page` got an HTML page (often a bot challenge) instead of a robots.txt file; they are `unknown` and not in the shares.

## Quick start

```python
import csv

rows = list(csv.DictReader(open("2026-11/ai-crawler-blocking-2026-11.csv")))
shares = [r for r in rows if r["in_shares"] == "true"]
blocked = sum(r["GPTBot"] == "blocked" for r in shares)
print(blocked, "of", len(shares), "block GPTBot")   # 169 of 779 block GPTBot
```

## Method

- **Domains.** The first 1,000 usable hosts of the Majestic Million (2026-11-26 edition), which orders domains by how many networks link to them, not by traffic. Removed: invalid and reserved names, adult and gambling sites, two hosts with no identifiable public site, and subdomains of a listed domain. We do not republish the list or its ranks; rows are alphabetical.
- **Requests.** Per domain, four or five GETs: `/robots.txt`, the homepage, `/llms.txt` and one same-site sitemap, identified as `aiagencynet-crawler-check/1.0` with a link to the tool. No crawling and no requests that pretend to be an AI crawler. From the December 2026 run on, the batch reads robots.txt first and stops there when it disallows our client. To opt out: `User-agent: aiagencynet-crawler-check` / `Disallow: /`.
- **Rules.** RFC 9309: groups matched by crawler name (case-insensitive) and merged; a crawler with no named group follows `User-agent: *`; the longest matching rule wins and an `Allow` that ties a `Disallow` wins.

## Limitations

- robots.txt is a request that well-behaved crawlers follow, not access control. CDNs and firewalls can block crawlers that robots.txt allows, and the reverse.
- One server, one moment. A site may serve different files to different clients; 221 of 1,000 domains are left out of the shares for that kind of reason (77 refused, 50 HTML instead of robots.txt, 64 no connection, 19 no working site, 5 server errors, 4 redirect loops, 2 error pages).
- Counts of published rules only. They do not measure traffic, quality or AI visibility, and whether a crawler actually visited a site is unknown. Blocking a crawler is often a deliberate choice.
- ChatGPT-User and Perplexity-User fetch pages when a user asks; their vendors say robots.txt may not apply, so their `blocked` values describe the file, not behavior.

## Attribution and license

- This dataset: CC BY 4.0, licensed by HATDOWN LLC. Credit "aiagencynet.com AI crawler blocking report, November 2026" with a link to the [report](https://aiagencynet.com/reports/ai-crawler-blocking-2026-11?utm_source=github&utm_campaign=e-b2-6).
- Domain selection: [Majestic Million](https://majestic.com/reports/majestic-million) by Majestic, licensed [CC BY 3.0](https://creativecommons.org/licenses/by/3.0/). We used the list order to pick domains and changed it (filtered, deduplicated, re-sorted alphabetically, ranks removed).
- Site owners can ask for their domain to be removed through the contact form linked from the report; removed domains are dropped from the site and from later versions of this file.
- Not affiliated with Majestic, OpenAI, Anthropic, Perplexity, Google, Microsoft or any site listed. aiagencynet also offers a free crawler checker and paid robots.txt fixes; nobody paid to be included or excluded.
