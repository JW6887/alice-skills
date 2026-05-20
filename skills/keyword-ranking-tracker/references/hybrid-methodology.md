# Hybrid Methodology for GEO Ranking Tracking (v2.0+)

When direct search-engine scraping fails (0-byte responses, dictionary results, anti-bot blocks), this methodology produces **directionally valid** ranking estimates from proxy metrics + news signals. All estimates are labeled as such ("估算") to distinguish from measured rankings.

## Three-Pillar Approach

### Pillar 1: Domain Health Check (域名健康检测)

Probe all tracked domains for HTTP status, response time, and page size. These are proxy indicators for AI search visibility:

| Signal | Good | Warning | Critical |
|--------|------|---------|----------|
| HTTP Status | 200 | Redirect (301/302) | 404, 403, 000(timeout) |
| Response Time | < 1s | 1-3s | > 3s |
| Page Size | > 100KB | 50-100KB | < 50KB |

**Interpretation rules**:
- Fast + large page → AI crawler can index rich content quickly → strong ranking signal
- 404 → page gone from both traditional search and AI index → immediate ranking drop
- 403 → blocked for crawlers → AI search engines may also be blocked → ranking erosion
- 000/timeout → server unreachable → worst case, domain may be delisted entirely
- Small page (26KB) + slow (3.28s) → insufficient content for AI to cite → weak ranking signal

**Command template**:
```bash
curl -sL -o /dev/null -w '%{http_code}|%{size_download}|%{time_total}' \
  --max-time 15 -A 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36' \
  'https://www.example.com'
```

### Pillar 2: Industry News Cross-Validation (行业信号交叉验证)

When domains can't be queried directly, industry news provides directional signals:

**How to use news for ranking estimates**:
- If a company announces a major partnership/deal → expect AI search citations to rise in 24-72h
- If a company has negative news (factory defects, regulatory issues) → AI search may deprioritize
- Regional market booms (e.g., Australia storage) → all vendors active in that region benefit
- New technology entry (e.g., EnerVenue in China) → may shift keyword-level citation patterns

**Proven working sources**:
- Energy Storage News RSS (`energy-storage.news/feed/`) — most reliable, clean XML
- CNESA (cnesa.org) — Chinese industry authority
- OFWeek 储能网 (chuneng.ofweek.com) — GBK encoding, needs `iconv`

**News-to-ranking mapping example**:
```
Gotion + Richardson Electronics partner for US BESS
→ "储能电池": Gotion ↑2 positions (English coverage triggers global AI citations)
→ "储能行业": Gotion 🆕 enters top 5 (strategic significance)
→ Bing/ChatGPT particularly sensitive (trained on English news)
```

### Pillar 3: Baseline Delta Analysis (基准差值分析)

Compare current domain health against the last measured snapshot:

1. For each domain, compare HTTP status, speed, and size against previous round
2. Score deltas:
   - 404→404 (no change): -2 sustained erosion
   - 404→200: +3 recovery signal
   - 200→404: -3 immediate crisis
   - Speed improvement >50%: +1
   - Speed degradation >50%: -1
3. Combine domain health delta with news signals to produce an estimated ranking table
4. Always label estimated tables as "估算" and show the comparison column (vs previous snapshot)

## Full Workflow Per Snapshot

```
1. Run domain health check on full matrix (22 domains)
2. Fetch ESN RSS feed → extract titles, dates, categories
3. Cross-reference news with tracked brands
4. Compare domain health deltas vs previous snapshot
5. Produce:
   a. Domain health table (full matrix)
   b. Key changes table (5-8 items)
   c. News signal table (5-8 items)
   d. Estimated ranking tables (4 keywords × top 5)
   e. Brand share delta table (vs snapshot #1)
   f. New monitoring targets (if any)
6. Append to geo-ranking.md with clear methodology label
```

## Weekly Report Insight Update Workflow

The `generate-report.py` script produces a structurally correct HTML report, but its insight section is static. After running the script, patch the HTML:

1. **Replace "关键发现" with this week's actual top 3-5 findings** — use the key changes table from the latest snapshot
2. **Add a "品牌 AI 搜索占有率 · 周度变化" table** — compare snapshot #1 vs latest, show deltas
3. **Rewrite GEO optimization advice** — tie each recommendation to a specific event this week (not generic)
4. **Add a "下周预测" section** — project next week's ranking changes based on current signals

Template for the patched insights HTML block is in `templates/weekly-insights-template.html`.

## Monitoring Matrix Evolution

The tracking matrix should expand over time:

| Phase | Domains | Date | Trigger |
|-------|---------|------|---------|
| v1.0 | 14 | 2026-05-15 | Baseline: 8 enterprises + 6 research/media |
| v1.1 | 19 | 2026-05-16 (#2) | Added: Huawei alternative URLs, Kehua |
| v1.2 | 22 | 2026-05-16 (#3) | Added: CNESA, OFWeek, EESI, NREL, ESA |

**Expansion criteria** — add a domain to the matrix when:
- A Chinese company expands overseas (Indonesia, US, Europe factories)
- A new technology enters the China market (EnerVenue nickel-hydrogen)
- A domestic industry authority not previously tracked (CNESA)
- A tracked entity changes its primary URL (Huawei migration)
