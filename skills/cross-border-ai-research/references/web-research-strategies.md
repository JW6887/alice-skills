# Web Research Strategies in Restricted Environments

This project runs in a container without browser sandbox support and with unreliable search engine access. Below are the strategies tested and their outcomes.

## ✅ Working Approaches

### Direct Site Fetching (curl)
```bash
curl -sL --max-time 15 \
  -H "User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36" \
  "<URL>"
```
Sites confirmed working:
- `cifnews.com` (雨果网) — Chinese cross-border e-commerce news and articles
- `amz123.com` — Cross-border seller navigation portal
- `github.com` — Open-source repos and topics
- `g2.com` — SaaS product reviews (title-level scraping)
- `en.wikipedia.org` — General reference

### Python HTTP
```python
import urllib.request
req = urllib.request.Request(url, headers={'User-Agent': 'Mozilla/5.0 ...'})
with urllib.request.urlopen(req, timeout=15) as resp:
    content = resp.read()
```
Same sites as curl, plus any JSON API that doesn't require auth.

## ❌ Not Working

### Search Engines
| Engine | Method | Result |
|--------|--------|--------|
| DuckDuckGo HTML | curl | Timeout after 30s |
| DuckDuckGo HTML | curl with browser UA | Empty results |
| Bing | curl + grep b_algo | Empty output |
| Serper.dev API | Python urllib | 403 Forbidden (no API key) |

### Browsers
| Tool | Issue |
|------|-------|
| Chrome (browser_navigate) | No sandbox support in container; `--no-sandbox` may work but untested |
| ProductHunt scraping | Cloudflare JS challenge blocks curl |

## Strategy for This Project

1. **Internal knowledge first**: Your training data through 2024 covers cross-border e-commerce AI tools well. Use it as primary source and note it.
2. **Verify with direct fetches**: For claims you're uncertain about, try fetching known authoritative URLs directly.
3. **Don't chase search**: If 2 search attempts fail, pivot to internal knowledge + direct verification.
4. **Search terms that work**: When search does work, use specific product names rather than broad queries.
5. **Subagent fallback**: When ALL `delegate_task` subagents time out (confirmed pattern: competitive analysis subagents can succeed, but pain-point/architecture subagents often time out at 600s), proceed with training knowledge alone. A full competitive analysis — 15-30 tools, pricing tiers, feature matrices, pain points — can be produced from training knowledge at equivalent quality. Don't block on subagent results.

6. **Single comprehensive subagent > multiple narrow subagents** (R05 learning): For domain deep-dives (e.g., TikTok Shop ecosystem), a single subagent with a comprehensive prompt covering market data, policies, pain points, and competitive analysis produces more coherent results than splitting into 2-3 parallel narrow subagents. The comprehensive subagent returned 4,700+ tokens of structured analysis in ~3 minutes, covering all requested dimensions. The parallel narrow subagent timed out. **Recommend**: launch one thorough subagent, not multiple shallow ones.
