# Web Scraping in Restricted Environments

This reference documents the specific behaviors encountered when scraping search engines from restricted/containerized Hermes Agent environments.

## Environment Characteristics

- Browser (Chrome) unavailable: `--no-sandbox` fails in container
- Outbound network: limited, some sites blocked entirely
- Security scanner (Tirith): blocks non-ASCII characters in URL paths
- pip install: may timeout due to network restrictions

## Search Engine Behavior Matrix

### Bing (www.bing.com)
| Aspect | Behavior | Workaround |
|--------|----------|------------|
| English queries | Usually works, returns real results with `li.b_algo` blocks | Use English translations of keywords |
| Chinese queries (raw) | May return dictionary entries for single characters instead of compound words | Use double-quote exact phrase: `"%E5%82%A8%E8%83%BD"` |
| Byte-identical responses | When all queries return exactly 50,033 bytes, Bing is serving a cached/generic page | Add more specific query terms; retry later |
| Chinese encoding | `setlang=zh-Hans` parameter helps but doesn't guarantee Chinese results | Accept mixed results; extract what's usable |

### Baidu (www.baidu.com)
| Aspect | Behavior | Workaround |
|--------|----------|------------|
| Search requests | Returns "百度安全验证" CAPTCHA page | NOT bypassable — skip Baidu entirely |
| Connectivity | Host reachable (HTTP 200) but search blocked | Use Bing or DDG instead |

### DuckDuckGo (lite.duckduckgo.com)
| Aspect | Behavior | Workaround |
|--------|----------|------------|
| Lite version | Returns simpler HTML, `result-link` class for titles | Easier to parse than full DDG |
| Timeout | 30s timeout common in restricted environments | Use as secondary fallback |
| Chinese support | Limited; English queries work better | Prefer English queries |

### Google
| Aspect | Behavior | Workaround |
|--------|----------|------------|
| Connectivity | Completely blocked (HTTP 000) | Not available |

### 36kr (36kr.com)
| Aspect | Behavior | Workaround |
|--------|----------|------------|
| Article API | Accessible (46KB responses) | Good for Chinese industry news |
| JSON extraction | `"title":"..."` pattern in inline JSON | Use regex on page source |

## Detection Heuristics

When results look suspicious, check:
1. **Byte count**: If all 4 keyword queries return identical byte counts → cached/generic page
2. **Domain check**: If top domains are all dictionary sites (zdic, cidian, bmcx) → Chinese tokenization failed
3. **Content check**: If snippets discuss "国民经济行业分类" instead of the target industry → query too generic

## URL Encoding Reference

Common Chinese terms (for manual URL construction):
```
储能      → %E5%82%A8%E8%83%BD
储能电池  → %E5%82%A8%E8%83%BD%E7%94%B5%E6%B1%A0
储能系统  → %E5%82%A8%E8%83%BD%E7%B3%BB%E7%BB%9F
储能行业  → %E5%82%A8%E8%83%BD%E8%A1%8C%E4%B8%9A
排名      → %E6%8E%92%E5%90%8D
公司      → %E5%85%AC%E5%8F%B8
行业      → %E8%A1%8C%E4%B8%9A
```

For exact phrase match (double quotes): `%22` + encoded + `%22`
Example: `%22%E5%82%A8%E8%83%BD%22` = `"储能"`
