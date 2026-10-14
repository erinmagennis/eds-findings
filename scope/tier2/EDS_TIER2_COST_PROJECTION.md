# EDS Tier 2 — cost projection (roster v2, post-screen)

Generated 2026-10-14T08:28:40+00:00. Basis: 18,024 output tokens per full-text paper, 5,326 per abstract-only paper (cost model v2: least squares on the 4 Tier-1 pilot units (2026-10-07), residuals <= 6%).
Units per chunk = per chunk max(1, min(2, ceil(papers/25)), ceil(fulltext/12)).

**2,734 papers** (275 full text expected, 2,459 abstract-only), **171 units**, **18,053,234 output tokens nominal**, **23,469,206 at +30%**.

| decade | papers | full text | abstract-only | units | tokens nominal | tokens +30% |
|---|---|---|---|---|---|---|
| 1940s | 1 | 1 | 0 | 1 | 18,024 | 23,431 |
| 1960s | 3 | 0 | 3 | 2 | 15,978 | 20,772 |
| 1970s | 98 | 0 | 98 | 8 | 521,948 | 678,532 |
| 1980s | 308 | 1 | 307 | 20 | 1,653,106 | 2,149,039 |
| 1990s | 476 | 1 | 475 | 28 | 2,547,874 | 3,312,237 |
| 2000s | 676 | 12 | 664 | 41 | 3,752,752 | 4,878,577 |
| 2010s | 1,172 | 260 | 912 | 71 | 9,543,552 | 12,406,618 |

## Recommended pilot chunks

- chunk 136 (2018-2018; 36 papers, 19 FT, 2 units): 2010s chunk with the most full-text papers: exercises the full-text path, the max_fulltext_per_unit split and the worker ceiling; refit the per-FT token coefficient here
- chunk 56 (1988-1989; 30 papers, 0 FT, 2 units): largest pre-1990 chunk (abstract-only, oldest abstract style, highest screen-uncertain share): tests yield on short historical abstracts and the per-abstract coefficient

Chunks with no rostered paper (ids frozen; the driver must dispatch nothing for them): [31, 32, 33, 34, 35, 38]
