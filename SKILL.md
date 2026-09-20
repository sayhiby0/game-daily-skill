---
name: game-daily
description: Fetch and summarize the latest video game news from major global and Chinese gaming outlets. Covers IGN, Kotaku, GameSpot, Polygon, Eurogamer, Rock Paper Shotgun, VG247, Gematsu, PlayStation Blog (international) plus Gcores, Chuapp, Gamersky, 3DM, Ali213, 17173, TapTap (China). Use when the user invokes /game-daily, asks for gaming news, game updates, or anything related to the latest in video games — in any language.
version: 1.0.0
author: user
tags: gaming, news, ign, kotaku, gamespot, polygon, eurogamer, gcores, chuapp, gamersky, 3dm, taptap, summary
---

# Game Daily

从全球主流游戏媒体（海外 + 国内）抓取 RSS 订阅源，聚合去重后生成每日游戏资讯简报。

## When to use

**Slash command trigger:**
- User types `/game-daily`

**Keyword auto-trigger (any language):**
- "游戏新闻"、"游戏资讯"、"最新游戏消息"、"游戏圈动态"
- "gaming news"、"latest game news"、"what's new in gaming"、"game updates"
- "게임 뉴스"、"게임 소식"、"최신 게임 소식"
- Any question about new game releases, gaming industry news, or console news

## News sources

### Tier 1 — International (native RSS)

| # | Outlet | RSS Feed URL | Focus |
|---|--------|-------------|-------|
| 1 | IGN | `https://feeds.ign.com/ign/all` | Broad coverage: games, movies, TV |
| 2 | Kotaku | `https://kotaku.com/rss` | News, culture, opinion |
| 3 | GameSpot | `https://www.gamespot.com/feeds/mashup/` | Reviews, news, all platforms |
| 4 | Polygon | `https://www.polygon.com/rss/index.xml` | Culture, features, reviews |
| 5 | Eurogamer | `https://eurogamer.net/feed` | European perspective, Digital Foundry tech analysis |
| 6 | Rock Paper Shotgun | `https://www.rockpapershotgun.com/feed` | PC gaming focus |
| 7 | VG247 | `https://vg247.com/feed` | Breaking news, all platforms |
| 8 | Gematsu | `https://gematsu.com/feed` | Japan / Asian game focus |
| 9 | PlayStation Blog | `https://blog.playstation.com/feed` | Official Sony/PS announcements |

### Tier 2 — China (native RSS)

| # | Outlet | RSS Feed URL | Focus |
|---|--------|-------------|-------|
| 10 | Gcores 机核 | `https://www.gcores.com/rss` | 深度游戏文化、原创长文、播客 |
| 11 | Chuapp 触乐 | `https://www.chuapp.com/feed` | 人物专访、行业报道、事件特稿 |

### Tier 3 — China (via RSSHub)

RSSHub is an open-source RSS feed aggregator. The public instance is `https://rsshub.app`. If the public instance is unavailable, users can [self-host](https://docs.rsshub.app/deploy/) or use any public mirror listed at https://docs.rsshub.app/guide/instances.

| # | Outlet | RSSHub Route | Focus |
|---|--------|-------------|-------|
| 12 | Gamersky 游民星空 | `https://rsshub.app/gamersky/news` | 单机/主机游戏资讯、评测、攻略 |
| 13 | 3DMGame | `https://rsshub.app/3dm/news` | PC 游戏资讯、汉化、攻略 |
| 14 | Ali213 游侠网 | `https://rsshub.app/ali213/news` | 综合游戏资讯、下载、攻略 |
| 15 | 17173 | `https://rsshub.app/17173/news` | 网游/端游资讯，MMO 侧重 |
| 16 | TapTap 热门 | `https://rsshub.app/taptap/topic/142793/official` | 手游社区动态（可换 `:id` 追踪具体游戏） |

### Tier 4 — China (web scrape fallback)

These outlets do not expose RSS feeds. If the user explicitly requests news from them, use `WebFetch` to scrape their homepage and extract recent headlines.

| # | Outlet | Homepage URL | Focus |
|---|--------|-------------|-------|
| 17 | GameLook | `https://www.gamelook.com.cn/` | 游戏行业深度报道、商业分析 |
| 18 | Youxiputao 游戏葡萄 | `https://www.youxiputao.com/` | 行业趋势、公司报道、产品分析 |
| 19 | Youxituoluo 游戏陀螺 | `https://www.youxituoluo.com/` | 行业数据、市场报告 |
| 20 | NGA 玩家社区 | `https://bbs.nga.cn/thread.php?fid=-563198` | 玩家爆料、八卦、社区讨论 |

## How to fetch news

### Default behavior (no specific source requested)

Fetch from **3 tiers simultaneously** using subagents:

1. **International**: Pick top 3 from Tier 1 (IGN, Kotaku, VG247)
2. **China native RSS**: Fetch Tier 2 (Gcores + Chuapp)
3. **China RSSHub**: Fetch Tier 3 (Gamersky + 3DM)

Aggregate all results. Deduplicate stories that appear across multiple sources — if the same event is covered by 2+ outlets, mention it once but note the sources (e.g., "IGN、游民星空等多家报道").

**Show up to 5 stories by default.** If user asks for more, show up to 10.

### When user specifies a platform or interest

| Interest | Prioritize |
|----------|-----------|
| PlayStation / Sony | PlayStation Blog + IGN + Kotaku + Gcores |
| Xbox / Microsoft | IGN + GameSpot + VG247 + Gamersky |
| Nintendo | Gematsu + Polygon + 3DM + Gamersky |
| PC gaming | Rock Paper Shotgun + Polygon + GameSpot + 3DM |
| Mobile gaming | TapTap + Gcores + Gamersky |
| Japanese / Asian games | Gematsu + Gcores + Gamersky |
| Industry / business | Eurogamer + VG247 + GameLook + Youxiputao |
| Reviews / scores | GameSpot + Polygon + Eurogamer + Gamersky |
| Chinese domestic games (国产游戏) | Gcores + Gamersky + 3DM + Chuapp |
| Indie games | Rock Paper Shotgun + Polygon + TapTap + Gcores |

### Feed parsing

From each RSS feed, extract per article:
- Title: `<title>`
- URL: `<link>` or `<link href="...">`
- Date: `<pubDate>` or `<published>` or `<updated>`
- Excerpt: `<description>` or `<summary>` (first 200 chars if full content included)
- Source name: derive from `<channel><title>` or the outlet table above

### Fetching full article content (on demand)

If the user asks for a deeper summary of a specific story, fetch the article URL and extract the main content from `<article>` or `<main>` tags.

## Output format

### Chinese output (default for zh-CN users)

```
🎮 游戏日报 · [YYYY-MM-DD]

📰 [标题] — [来源名称]
🔗 [URL]
📝 [2-3句中文摘要]

📰 [标题] — [来源名称]
🔗 [URL]
📝 [2-3句中文摘要]

...

---
数据来源：[列出本次成功抓取的源名称]
```

### English output (for en users)

```
🎮 Game Daily · [YYYY-MM-DD]

📰 [Title] — [Source]
🔗 [URL]
📝 [2-3 sentence summary]

...

---
Sources: [list successfully fetched outlets]
```

### Korean output (for ko users)

```
🎮 게임 뉴스 브리핑 · [YYYY-MM-DD]

📰 [제목] — [출처]
🔗 [URL]
📝 [2–3문장 요약]

...
```

## Rules

- **Language**: Match the user's language. Chinese input → Chinese output (translate English article titles and excerpts).
- **Ordering**: Lead with the most significant or surprising stories first.
- **Release announcements**: Include platform and release date if mentioned.
- **Reviews**: Include the score if present in the excerpt.
- **Factual**: Keep summaries factual — do not add opinions not present in the source.
- **Dedup**: If 2+ sources cover the same story, merge into one entry and list all source names.
- **Balance**: Try to show a mix of international and domestic Chinese news when both are available.
- **Freshness**: Prefer articles from the last 24-48 hours. Skip anything older than 7 days unless specifically relevant.

## Error handling

| Situation | Response |
|-----------|----------|
| One feed fails | Skip it silently, fetch from next source in the same tier |
| RSSHub instance down | Try the next Tier 3 source, or fall back to Tier 4 (web scrape) for that outlet |
| All international feeds fail | "海外游戏资讯源暂时无法访问，正在尝试国内源…" → continue with Chinese sources |
| All Chinese feeds fail | "国内游戏资讯源暂时无法访问，正在尝试海外源…" → continue with international sources |
| All feeds fail | "目前无法获取游戏资讯，请稍后再试。/ Cannot fetch gaming news right now. Please try again later." |
| Partial results | Show what was found, note which sources were unavailable in the footer |
| Tier 4 scrape fails | Skip silently — these are bonus sources, never mention their absence |

## Advanced: tracking specific games on TapTap

For Tier 3 TapTap, the default route `/taptap/topic/142793/official` tracks a popular game. If the user wants to track a specific game:

1. Ask the user for the game name
2. Search TapTap for the game to find its `:id` (numeric ID in the URL)
3. Use route: `https://rsshub.app/taptap/topic/:id/official`

Example: to track Genshin Impact (id=168332):
`https://rsshub.app/taptap/topic/168332/official`
