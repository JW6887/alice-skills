---
name: cross-border-ai-research
description: >
  Autonomous recurring research + deliverable production for cross-border AI tools product development.
  Runs as a scheduled cron job (every 8h). Each run produces one structured research document covering a
  sub-segment of cross-border e-commerce AI tools: competitive analysis, feature design docs, or industry articles.
  Target: determine MVP scope within 1 week, product design plan within 2 weeks, full PRD+tech plan within 1 month.
triggers:
  - "cross-border AI tools research cron"
  - "跨境AI工具研究"
  - "cross-border ecommerce product development research"
  - scheduled cron job producing research deliverables
---

# Cross-Border AI Tools Research & Product Development

Recurring autonomous research for building an AI-powered product selection agent for cross-border e-commerce.

## Workflow (per run)

### Step 1: Check State
Read `~/.hermes/survival/cross-border/PROGRESS.md` to understand:
- Which research domains have been covered
- Which are pending
- Key decisions made in prior rounds
- MVP direction identified (currently: AI选品Agent)

Also list existing files in `~/.hermes/survival/cross-border/` to avoid duplication.

**Current state (after Week 3, R11)**: 🎯 **Week 3 进行中** — PRD V1（38KB）+ 技术方案 V1（73KB）已完成。涵盖：功能需求（F1-F5）、API设计、数据库DDL、LangGraph Agent编排、集成架构。Week 3 剩余目标：评审与细化。整体进度：MVP范围已确定，产品设计方案V2完成，PRD+技术方案初稿完成。

**R06关键发现**:
- Review洞察→选品Agent联动 = 核心护城河（选品工具和Review工具全行业完全割裂）
- 差评即选品机会：竞品无人做到，是P0差异化切入点
- 30+竞品工具覆盖（国际15+国内7+AI原生8）
- Phase 1-3功能建议已明确

**Next round strategy** — Week 2:
1. **产品设计方案输出**: 将R01-R06六轮研究转化为正式PRD文档 + 技术方案（Week 2里程碑）
2. **继续补充研究**: 下一个优先级：AI供应链 & 库存预测 或 跨境合规AI深度专题
3. **MVP功能设计V2**: 细化交互原型，纳入Review洞察联动和TikTok双平台扩展点

### Step 2: Pick Research Domain
From the pending topics in PROGRESS.md, pick the next one. Priority order:
1. Continue deepening the MVP domain (AI选品Agent) — functional design V2, interaction prototypes, wireframe-level specs
2. Study adjacent domains to inform MVP scope — current priority: **跨境合规AI深度专题** (identified as critical moat in R01-R04 but never studied in depth), **AI Review分析 & 用户洞察**
3. After domain coverage is sufficient, shift focus to building: feature design docs V2, PRD sections, tech specs
4. **Cross-round validation**: After each new domain, explicitly analyze how findings reinforce/change MVP direction
- Provide a detailed goal and context (what to cover, sub-segments, key questions, output format)
- Cost: ~5-8 API calls, ~5 min for a thorough subagent run
- Quality: Equivalent to 30 min of manual research with working web access

**Fallback: Direct site fetches** — Known working sources:
  - `cifnews.com` (雨果网) — Chinese cross-border news
  - `amz123.com` — Cross-border seller portal
  - `github.com/topics/cross-border-ecommerce` — Open-source tools
  - `g2.com` — SaaS reviews (title scraping at minimum)
  - `producthunt.com` — May have Cloudflare; fall back to curl with browser UA

**Fallback: Training knowledge** — Your internal knowledge through 2025 is substantial. Cite "internal knowledge" where web verification failed.

### Step 4: Produce Deliverable
Save to `~/.hermes/survival/cross-border/{NN}-{slug}.md` where NN is the next run number (zero-padded).

Standard document structure for competitive analysis:
```
# [Title]
> Run: NN | Date: YYYY-MM-DD | Domain: [sub-segment]
> Output Type: [competitive-analysis | feature-design | industry-article]

## 1. Market Overview (市场规模, 趋势, 赛道定义, LLM影响)
## 2. Competitive Landscape (全景矩阵, 象限分布图, 详细对比表按Tier分组)
## 3. AI Capability Assessment (成熟度模型L0-L5, 关键能力Gap分析表)
## 4. Technology Architecture Deep Dive (可选)
##     - Technical patterns comparison (GPT Wrapper → Multi-Agent)
##     - Ideal architecture pipeline (ASCII diagram)
## 5. Pricing Strategy Comparison (可选: 国际vs中国价格带, 定价模型机会)
## 6. Pain Point Analysis (决策链痛点, 分角色痛点, 十大痛点排名)
## 7. Opportunity & MVP Recommendations
##     - Cross-round validation: how findings reinforce/change MVP direction
##     - MVP联动分析 (how this domain connects with the MVP domain)
## 8. Summary & Next Round Preview
```

For feature design docs, focus on: user flows, interaction patterns, data architecture, edge cases.

### Step 5: Update Progress
Update `PROGRESS.md` with the new run entry, and mark newly completed topics.

## Directory Structure
```
~/.hermes/survival/cross-border/
├── PROGRESS.md                           # Master progress tracker
├── 01-ai-product-research-competitive-analysis.md   # R01: AI选品
├── 02-ai-listing-content-competitive-analysis.md    # R02: AI Listing
├── 03-ai-ppc-competitive-analysis.md               # R03: AI PPC
├── 04-ai-customer-service-translation-competitive-analysis.md  # R04: AI客服
├── 05-mvp-feature-design-v1.md                    # R05: MVP功能设计V1
├── 06-tiktok-shop-ai-opportunity-map.md          # R06: TikTok Shop
├── 07-ai-review-analysis-user-insights.md         # R07: Review洞察
├── 08-ai-supply-chain-inventory-analysis.md      # R08: 供应链
├── 09-ai-pricing-strategy.md                     # R09: 定价策略
├── 09-compliance-ai-deep-dive.md                 # R09: 跨境合规
├── 10-product-design-v2-week2.md                 # Week 2: 产品设计方案V2
├── 11-prd-v1-week3.md                           # Week 3: 产品需求文档PRD
└── 12-technical-spec-v1-week3.md                # Week 3: 技术方案文档
```

## Deliverable Types (by phase)

The research cycle has 3 phases, each producing a distinct document type:

### Phase 1 (Week 1): Competitive Analysis
`{NN}-{slug}.md` — Single-subject research documents.
Template: `references/competitive-analysis-template.md`
Content: Market overview, competitor matrix, AI capability assessment, pain points, MVP recommendations.

### Phase 2 (Week 2): Product Design Document
`10-product-design-v2-week2.md` — Integrated design document merging all Phase 1 research.
Content: Product vision, user personas, competitive landscape, MVP functional design, Phase 2/3 roadmap, business model, data architecture, technical architecture, moat summary.

### Phase 3 (Week 3+): PRD + Technical Spec
- `11-prd-v1-week3.md` — Full product requirements document.
  Content: User stories, functional requirements (F1-F5), non-functional requirements, acceptance criteria.
- `12-technical-spec-v1-week3.md` — Full technical specification.
  Content: System architecture, API design, database schema (DDL), Agent orchestration (LangGraph state machine), external integrations, compliance engine, SSE streaming, deployment config.

## References
- `references/competitive-analysis-template.md` — Document template for competitive analysis deliverables (Phase 1)
- `references/web-research-strategies.md` — What works and doesn't for web research in restricted container environments
- `references/agent-world-ecosystem-scan.md` — Agent World alliance stations monetization scan (虾评 only viable path; 策场 for case building)

## Key Decisions (persisted in PROGRESS.md)

**From R01/R02**:
- **MVP Direction**: AI选品Agent — conversational, autonomous execution, end-to-end (Product Selection → Supply Chain → Profit)
- **Initial Platform**: Amazon only
- **Core Differentiator**: Agent that *does the work*, not just answers questions
- **MVP includes basic Listing generation**: The "选品→Listing初稿" closed loop is core differentiation, even if rough
- **Compliance engine is a must-have moat**: No competitor does compliance well; it's both a user pain point and the hardest engineering challenge

**From R03**:
- **AI PPC不属于MVP但Phase 1必须预埋架构**: PPC needs historical data to be effective (cold-start problem). Phase 1 accumulates users+data → Phase 2 PPC can deliver AI value. But data structures, API layers, and compliance engine must reserve PPC interfaces now.
- **TikTok Shop PPC是12个月内最大机会窗口**: Zero mature AI TikTok Shop ad tools globally. Chinese sellers pouring into TikTok Shop. Phase 2 should prioritize TikTok PPC over Amazon PPC.
- **「中文可解释性」是PPC付费转化发动机**: Chinese sellers' #1 barrier to paying for AI PPC is "不信任黑盒" (don't trust the black box). Explaining every AI decision in Chinese directly solves the trust problem. No competitor does this globally.
- **PPC定价必须是固定价（¥199-599）**: Chinese sellers deeply dislike percentage-of-ad-spend pricing ("多投还要多分你钱?"). Fixed monthly + pay-per-report is the only viable model.
- **R01+R02+R03覆盖了「发现→呈现→推广」三环**: Product+Listing+Ads competitive research complete. Ready to enter product design phase.

**From R04**:
- **AI客服进Phase 3**: Needs user scale and data (no users means no conversations to optimize). Phase 1 pre-embeds Amazon Messaging API interface only.
- **翻译在Phase 1做基础（DeepL API），Phase 2深化自研**: Basic Listing generation needs translation. DeepL API in Phase 1 keeps complexity low. Translation abstraction layer supports hot-swap to self-built model in Phase 2.
- **「客服→选品反馈闭环」是终极护城河**: No competitor can do this — it requires both a selection agent AND a customer service agent in the same system. Customer service data → product improvement insights → push to selection agent. This is the Phase 3 endgame moat.
- **MVP功能设计文档V1完成**: Full functional scope defined: conversation engine, opportunity analyzer, 1688 supply chain matcher, basic listing generator, compliance engine (~200 rules). 4-week dev roadmap, pricing (¥0-599), success metrics, tech architecture all specified.
- **Week 1 milestone ACHIEVED**: MVP scope determined. 四环研究（发现→呈现→推广→服务）全覆盖。

## Pitfalls
- Search engines (DDG, Bing, Serper) are often blocked/timed out in this environment. Don't retry >2 times.
- Chrome/browser cannot launch due to sandbox restrictions (Linux container/VM). Use curl + Python instead.
- ProductHunt has Cloudflare anti-bot; expect "Just a moment..." responses.
- `write_file` is the most reliable way to persist documents; the path must be absolute with `~` expanded.
- **delegate_task for subagent research**: Competitive landscape analysis subagents succeed reliably (~5-8 API calls, ~5 min). But pain-point research and technical architecture analysis subagents tend to **time out after 600s** — likely due to heavy web search attempts or complex reasoning chains. Mitigation: (1) Launch competitive-landscape subagents first as they reliably produce the core data; (2) For pain-point and tech-architecture research, either use shorter timeout windows or handle them directly in the main agent using internal knowledge; (3) Even if 2/3 subagents time out, the one that succeeds usually provides sufficient competitive data for the full report.
- **⚠️ Leaf subagent terminal tool limitations for file writing**: When a `delegate_task` leaf agent writes multi-line content (e.g., large markdown research reports), the **terminal tool fails reliably** — heredoc syntax, pipes, and multi-line output redirection all return errors or empty results. This is a known tool quirk in this environment. **Workaround**: After the subagent completes, the parent agent should use `execute_code` (Python subprocess with `subprocess.run(['python3', '-c', f'content = {repr(full_content)}; open(path, "w").write(content)']...)`) to write/append the complete file content in a single operation. Do NOT rely on terminal heredoc or multi-line `cat > file <<EOF` patterns for content >1KB — they will silently fail.
- **Multiple parallel delegate_task calls**: Launching 3 subagents simultaneously is fine — the main agent can proceed with analysis using whatever results return. Wait for all but don't block indefinitely; after ~10 min, proceed with partial results.

**From R05**:
- **TikTok Shop选品Agent进Phase 2 P0**: TikTok Shop is in its explosive growth phase with severely underdeveloped AI tools. The '达人匹配' (influencer matching) capability is TikTok's unique moat — Amazon doesn't need it, TikTok Shop can't succeed without it, and no competitor does AI-powered influencer matching yet.
- **Amazon+TikTok双平台选品=差异化杀手锏**: Zero tools globally cover both Amazon AND TikTok Shop product selection simultaneously. Cross-platform signal validation (e.g., "this product sells on Amazon AND has trending TikTok content") reduces seller risk significantly. Architecture must pre-embed multi-platform extension points from Day 1.
- **12个月窗口确认**: H2 2026 is the optimal window for AI Agent entry into TikTok Shop. First-mover advantage is clear given the current tool vacuum.
- **TikTok Shop vs Amazon的本质差异**: TikTok = 兴趣电商 (content-driven impulse buying), Amazon = 搜索电商 (intent-driven search buying). Only ~30% of Amazon seller skills transfer to TikTok Shop. AI tools must address this fundamental difference, not just port Amazon features.

## Research Domains (from PROGRESS.md)

**🎯 里程碑**: Week 1-3研究完成，PRD + 技术方案 V1 已产出。Week 3 继续细化文档。

| # | 领域 | 状态 | 关联文档 |
|---|------|------|---------|
| 1 | AI选品 (Product Research) | ✅ R01 | 竞品分析 |
| 2 | AI Listing优化 & 内容生成 | ✅ R02 | 竞品分析 |
| 3 | AI广告投放 (PPC Optimization) | ✅ R03 | 竞品分析 |
| 4 | AI客服 & 多语言翻译 | ✅ R04 | 竞品分析 |
| 5 | TikTok Shop生态 | ✅ R05 | 机会地图 |
| 6 | AI Review分析 & 用户洞察 | ✅ R07 | 洞察分析 |
| 7 | AI供应链 & 库存预测 | ✅ R08 | 供应链分析 |
| 8 | AI定价策略 | ✅ R09 | 定价策略 |
| 9 | 跨境合规AI | ✅ R09 | 合规深度 |
| 10 | 多平台店铺管理AI | ⏳ 待研究 | Phase 2/3 |

**Week 3 产出**:
- `11-prd-v1-week3.md` — 完整PRD（38KB）：用户故事、功能需求F1-F5、验收标准
- `12-technical-spec-v1-week3.md` — 完整技术方案（73KB）：架构、API、DDL、LangGraph、SSE

**Next round strategy** — Week 3 继续:
1. **PRD细化**: 补充Edge Case、API错误码规范、安全设计细节
2. **技术方案细化**: 补充监控告警阈值、压测方案
3. **下一优先级研究**: 多平台店铺管理AI 或 TikTok Shop达人匹配（Phase 2核心）
