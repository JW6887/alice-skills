# Competitive Analysis Document Template

Use this structure for all competitive analysis deliverables in the cross-border AI research project.

## Document Header
```
# [领域名称] —— 竞品深度对比分析

> **轮次**: {NN} | **日期**: {YYYY-MM-DD} | **研究领域**: {domain}
> **产出类型**: 竞品对比表 + 机会分析
```

## Section 1: Market Overview
- **赛道定义**: What problem does this solve? Core decision chain?
- **市场规模**: Table with market size, seller counts, penetration rates, key trends
- **LLM Impact**: How has generative AI changed this domain since 2023?

## Section 2: Competitive Landscape

### Quadrant Matrix
Use a 2x2 matrix to position competitors. Common axes:
- AI Depth (传统数据 → AI原生决策引擎)
- Platform Coverage (单平台 → 多平台)
- Target Market (中国卖家 → 国际通用)
- Maturity (新兴 → 成熟)

### Detailed Comparison Tables
Group by category (e.g., International Mainstream, Chinese Native, Platform Native).

Columns: Product | Core Features | AI Capability | Pricing | Strengths | Weaknesses

Aim for 12-20 competitors across all categories.

## Section 3: AI Capability Assessment

### Maturity Model (L0-L4)
| Level | Description | Representative Products | Core Tech |
| L0: No AI | Pure data query/display | Keepa, CamelCamelCamel | SQL/OLAP |
| L1: AI-Assisted | Rule engine + simple ML scoring | AMZScout, Sorftime | Rules + Basic ML |
| L2: AI-Enhanced | LLM generation + prediction models | Jungle Scout, Helium 10 | LLM+NLP+Time Series |
| L3: AI Decision | Multi-modal + Agent autonomous decision | Perci (claimed) | Multi-agent+Multi-modal |
| L4: AI Autonomous | End-to-end AI operations | None mature yet | Full-chain AI Agent |

### AI Capability Gap Analysis
Table: AI Capability | Current State | Ideal State | Gap Size

## Section 4: Pain Point Analysis

### Decision Chain Pain Map
ASCII diagram showing the decision chain (发现→分析→验证→确定→监控) with pain points at each step.

### Role-Based Pain Points
| Role | Core Pain | Tool Shortcomings | Opportunity |

## Section 5: Opportunities & MVP Recommendations
- 3-5 concrete opportunity areas with rationale
- Recommended MVP path (Phase 1/2/3)
- Core differentiators

## Section 6: Summary
- What was produced this round
- Next round preview
- File path and run duration
