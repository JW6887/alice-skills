---
name: keyword-ranking-tracker
description: Keyword ranking tracking system — automated search-engine data collection, cron-based scheduling, and periodic report generation for competitive intelligence / data products.
tags: [keyword-tracking, cron, data-collection, reporting, GEO, SEO, competitive-intelligence]
---

# Keyword Ranking Tracker

Automated system for tracking keyword rankings across multiple search engines with cron-based scheduling and periodic report generation. Produces a sellable data product (weekly PDF reports).

## When to Use

- User wants to track how brand/company ranks in AI search results (GEO tracking)
- User wants to build a competitive intelligence data product
- User needs scheduled keyword position monitoring with periodic reports
- User asks about "GEO", "AI搜索排名", "关键词追踪", or "search ranking monitoring"

## Architecture

```
~/.hermes/survival/
├── geo-ranking.md          ← Core data file (all snapshots, human-readable)
├── geo-scraper.py          ← Data collection script (multi-engine, graceful degradation)
├── generate-report.py      ← Weekly report generator (HTML + PDF)
└── reports/
    └── GEO-XXX_YYYY-Www.html  ← Generated reports
```

Three-tier data pipeline:
1. **Collection** (cron, every N hours): Scrape search results → append to markdown data file
2. **Storage**: Single markdown file with chronological snapshots, human-readable tables
3. **Reporting** (cron, weekly): Parse data file → generate branded HTML report → try PDF

### Hybrid Methodology (v2.0) — When Search Engines Block You

When direct search-engine scraping fails completely (0-byte responses from all engines, or responses filled with dictionary/irrelevant results), use this fallback chain:

1. **Domain Health Check**: Probe 12-15 core industry domains (HTTP status, response time, page size). Fast + large pages → strong AI-search visibility signal. 404/403 → domains falling out of AI search indexes. This is a **proxy metric** — not the actual ranking, but directionally validated.

2. **Baseline Delta Analysis**: Compare current domain health against the last known-good snapshot. If a domain that was highly ranked now returns 404, project a ranking drop. If a fast-growing company's domain is faster/larger than ever, project a rise.

3. **Estimated Ranking Table**: Based on signals, produce a labeled "estimated" ranking table (vs "measured"). Clearly mark the methodology so consumers know it's derived, not scraped.

4. **Key Changes Section**: Surface actionable deltas: "Huawei 404 → potential 3-5 rank drop", "BYD 0.11s/307KB → surge signal".

Full details in `references/hybrid-methodology.md`.

## Step-by-Step Setup

### 1. Create the data file
Write `geo-ranking.md` with a proper header section explaining methodology, keywords tracked, and search engines monitored. Include a baseline snapshot with realistic data seeded from known industry sources. Format each snapshot as:

```markdown
### 快照 #N — YYYY-MM-DD HH:MM UTC (Weekday)

#### Keyword Name

| 排名 | 搜索引擎 | 来源域名 | 品牌/机构 | 内容类型 | 备注 |
|------|---------|---------|----------|---------|------|
| 1 | Perplexity | example.com | Brand Name | 企业官网 | ↑ |
```

### 2. Create the scraper script
Build `geo-scraper.py` with multi-engine fallback:
- Try Bing first (English queries work more reliably in restricted environments)
- Fall back to DuckDuckGo Lite
- As last resort: record a timestamped "data unavailable" entry

**Critical: URL-encode all non-ASCII queries.** Security scanners (Tirith) block raw Chinese characters in URL paths. Use `urllib.parse.quote()`:

```python
import urllib.parse
enc = urllib.parse.quote("储能行业排名")
url = f"https://www.bing.com/search?q={enc}"
```

### 3. Create the report generator
Build `generate-report.py` that:
- Parses markdown snapshots with robust regex (handle format variations)
- Generates styled HTML with stats grid, brand ranking table, per-keyword detail, and insights
- Attempts PDF via `wkhtmltopdf` or `weasyprint`, falls back to HTML + MD text copy
- Idempotent: skips if report already exists for that week

### 4. Set up cron jobs
Two separate cron jobs via the `cronjob` tool:

| Job | Schedule | Script |
|-----|----------|--------|
| Data collection | `0 */8 * * *` (every 8h) | `python3 ~/.hermes/survival/geo-scraper.py` |
| Weekly report | `0 0 * * 6` (Saturday midnight) | `python3 ~/.hermes/survival/generate-report.py` |

## Pitfalls & Gotchas

### Web scraping in restricted environments
| Symptom | Cause | Fix |
|---------|-------|-----|
| All Bing queries return identical byte counts (e.g., 50,033) | Bing rate-limiting, returning cached generic page | Wait and retry; use English queries; test with `wc -c` |
| Baidu returns "百度安全验证" | CAPTCHA detection | Can't bypass; use Bing/DuckDuckGo instead |
| DuckDuckGo times out after 30s | Network firewall | Use as fallback only |
| Non-ASCII URL characters blocked | Tirith security scanner | Always URL-encode Chinese/Unicode queries |
| Bing shows dictionary entries instead of industry results | Chinese tokenization splits compound words (e.g., "储能" → "储") | Use double-quote exact phrase: `%22keyword%22` |
| Bing English query returns dictionary sites (iciba.com, dict.bing.com) for Chinese industry terms | Bing interprets "energy storage China" as English vocabulary lookup | Use Chinese keywords with `&cc=cn&setlang=zh-Hans` params; failing that, fall back to hybrid methodology |
| DuckDuckGo returns 0 bytes (complete block) | IP blocked entirely, not just rate-limited | Skip DDG entirely; use domain health proxy instead |
| All 4 keyword queries return identical byte counts AND garbage content | Search engine serving cached/generic page under block | Delete garbage snapshot immediately; switch to hybrid methodology for that round |
| Scraper reports "success" but results are dictionary/irrelevant | Success check only validates presence of HTML blocks, not content relevance | Add content validation: check if top 3 domains include known industry brands (catl, sungrow, byd, etc.); if not, mark as failure |

### Data parsing
- Bing HTML structure: results in `<li class="b_algo">`, titles in `<h2><a>`, domains in `<cite>`, snippets in `<p class="b_lineclamp2">`
- DuckDuckGo Lite: simpler HTML, `result-link` class for links
- Regex must handle `&nbsp;`, `&ensp;`, date patterns like `2025年12月7日`
- Always `html.unescape()` extracted text

### News signal sources (for industry context in snapshots)
When search engines block direct ranking queries, industry news provides directional signals for ranking estimates:

| Source | URL | Works? | Notes |
|--------|-----|--------|-------|
| Energy Storage News RSS | `energy-storage.news/feed/` | ✅ YES | Clean RSS XML, 8-10 items per fetch, parse with regex `<title>`, `<pubDate>`, `<link>`, `<category>` |
| CNESA (中关村储能联盟) | `cnesa.org` | ✅ YES | Chinese industry authority, 200 OK, 490KB, 2.11s |
| OFWeek 储能网 | `chuneng.ofweek.com` | ⚠️ Partial | Chinese industry media, 200 OK but GBK encoding — `grep` extracts garbled text; use `iconv -f gbk -t utf-8` if needed |
| Bing News Search | `bing.com/news/search?q=储能` | ❌ NO | Returns Bing homepage HTML, not news results |
| 36Kr 储能频道 | `36kr.com/search/articles/储能` | ❌ NO | ERR/blocked

### PDF generation
- `wkhtmltopdf` is the most reliable option: `apt install wkhtmltopdf`
- `weasyprint` is lighter but needs system dependencies (cairo, pango)
- `fpdf2` / `reportlab` require manual layout — not suitable for rich HTML→PDF
- When no PDF converter is available, deliver the HTML report — it's a valid product

### Report insights are NOT auto-generated
The `generate-report.py` insight section (本周洞察与建议) is **hardcoded static text** — it does not reflect the current week's actual findings. After running the script, you MUST patch the HTML report to update insights with:
- The current week's top 1-2 news events (e.g., Gotion US partnership)
- Brand share changes (周度变化) in a table
- GEO optimization advice tied to this week's actual events (not generic)
- See `references/hybrid-methodology.md` for the full insight-update workflow

### Parser only captures FIRST domain health / key changes table
The report generator uses `re.search()` (first match) for domain health and key changes parsing. When the data file contains multiple snapshots each with their own tables, **only snapshot #2's tables are captured**. Snapshot #3+ domain health data is invisible to the parser. Workaround: manually patch the HTML report, or fix the parser to use `re.findall()` with snapshot-aware grouping. Tracked as a known limitation.

### Alternative URL probing when a primary domain dies
When a monitored URL returns 404/403, test known alternative subdomains for the same entity:
- `huawei.com/cn/digital-energy` → 404 → found live at `digitalpower.huawei.com` and `solar.huawei.com`
- This heuristic should be formalized: for any dead URL, test `{brand}.com`, `www.{brand}.com`, `{vertical}.{brand}.com`, and `{brand}.com/{region}`
- Log the discovery in the key changes section

### Report parsing robustness
- Snapshot headers can vary: `### 快照 —`, `### 快照 #1 —`, `### 快照 #N —`
- Keyword headers can have numbers and quotes: `#### 1. 「储能」— Energy Storage` or `#### 储能` or `**1. 「储能」— Energy Storage**`
- Table columns vary by snapshot format: v1 uses `排名 | 搜索引擎 | 来源域名 | 品牌/机构 | 内容类型 | 备注`; v2 uses `排名 | 搜索引擎 | 来源域名 | 品牌/机构 | 信号`
- The brand column (4th) is the primary display field; domain (3rd) is used for brand matching
- Snapshot blocks may also contain non-ranking tables (domain health, key changes, brand share) — parser should extract all of them, not just keyword rankings
- When both v1 (measured) and v2 (estimated) snapshots exist, the report should clearly label which is which

## Data Product Branding

For sellable data products, embed:
- Product ID (e.g., `GEO-ES-001`)
- Clear subscription labeling ("机密 · 付费订阅")
- Professional cover page with reporting period
- Methodology section for credibility
- Change log for audit trail

## Verification

After setup, verify:
1. `python3 geo-scraper.py` runs without errors and appends to the data file
2. `python3 generate-report.py` produces HTML in `reports/` directory
3. Cron jobs are listed with correct schedules
4. Data file contains at least one baseline snapshot
