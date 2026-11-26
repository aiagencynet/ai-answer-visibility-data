# Changelog: AI crawler blocking in robots.txt

## Version 2, 2026-11-27

GitHub release `crawler-2026-11-v2`.

- **More domains.** The list now covers the first 5,000 domains of the same Majestic Million edition (2026-11-26), up from the first 1,000. Positions 1,001 to 5,000 were checked in a second run on 2026-11-26 (UTC, 16:44 to 17:37).
- **Same first 1,000 rows.** The rows for the first 1,000 domains are identical to version 1, apart from the new column below. Version 1 is kept unchanged at `2026-11/v1-top1000/ai-crawler-blocking-2026-11.csv` (sha256 `83773040080815ad408f9023213253ee8796ef800d193d3d83faf85d86ba8996`), with its own `meta.json`.
- **New column `checker_opted_out`** (last column). The second run read robots.txt first and requested nothing else when it disallows our client. That applied to 66 domains, all in positions 1,001 to 5,000. The column is `false` for every first-run row because the rule was not applied then. For those rows `llms_txt`, `sitemap` and `homepage_http_status` are empty, and they are left out of the llms.txt and sitemap shares.
- **Headline shares differ because the set of domains differs.** The first-run numbers still describe the first 1,000 domains. The less-linked domains added in version 2 block these crawlers less often, so the shares over all 5,000 are lower:

| | First run (v1, top 1,000) | Extended run (v2, top 5,000) |
|---|---|---|
| Domains in the shares | 779 | 3,819 |
| GPTBot blocked | 21.7% | 19.4% |
| ClaudeBot blocked | 22.1% | 19.0% |
| PerplexityBot blocked | 17.5% | 12.3% |
| OAI-SearchBot blocked | 10.8% | 7.8% |

- `meta.json` now lists both runs (`runs`) and covers 5,000 domains.

## Version 1, 2026-11-26

GitHub release `crawler-2026-11`.

- First 1,000 domains of the Majestic Million (2026-11-26 edition), checked 2026-11-26, 11:26 to 11:37 UTC.
- 779 domains in the shares. GPTBot 21.7%, ClaudeBot 22.1%, PerplexityBot 17.5%, OAI-SearchBot 10.8% blocked.
