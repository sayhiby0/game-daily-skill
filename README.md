# game-daily-skill

> QoderWork skill — Fetches and summarizes the latest video game news from major global **and** Chinese gaming outlets.

## Attribution

This skill is a fork / extension of [**byeolbit/gamer-news-skill**](https://github.com/byeolbit/gamer-news-skill) (original author: [byeolbit](https://github.com/byeolbit)), which covers 9 international gaming outlets.

**game-daily-skill** builds on that foundation by adding Chinese domestic gaming news sources, bringing the total to **20 outlets across 4 tiers**:

| Tier | Type | Sources |
|------|------|---------|
| Tier 1 | International — native RSS | IGN, Kotaku, GameSpot, Polygon, Eurogamer, Rock Paper Shotgun, VG247, Gematsu, PlayStation Blog |
| Tier 2 | China — native RSS | Gcores (机核), Chuapp (触乐) |
| Tier 3 | China — via [RSSHub](https://github.com/DIYgod/RSSHub) | Gamersky (游民星空), 3DMGame, Ali213 (游侠网), 17173, TapTap |
| Tier 4 | China — web scrape fallback | GameLook, Youxiputao (游戏葡萄), Youxituoluo (游戏陀螺), NGA |

All Tier 1 sources, feed parsing logic, deduplication rules, and error handling patterns are inherited from the original gamer-news-skill.

Thank you byeolbit for the excellent groundwork!

## Install

```bash
# If you have a compatible skill installer:
# npx clawhub@latest install game-daily
# Or manually copy SKILL.md to your QoderWork skills directory
```

## Sources detail

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
| 10 | Gcores (机核) | `https://www.gcores.com/rss` | Deep gaming culture, original long-form, podcasts |
| 11 | Chuapp (触乐) | `https://www.chuapp.com/feed` | Interviews, industry reports, feature stories |

### Tier 3 — China (via RSSHub)

Uses [RSSHub](https://github.com/DIYgod/RSSHub) open-source aggregator. Public instance: `https://rsshub.app`

| # | Outlet | RSSHub Route | Focus |
|---|--------|-------------|-------|
| 12 | Gamersky (游民星空) | `/gamersky/news` | Console/PC game news, reviews, walkthroughs |
| 13 | 3DMGame | `/3dm/news` | PC gaming news, localization, guides |
| 14 | Ali213 (游侠网) | `/ali213/news` | General game news, downloads, guides |
| 15 | 17173 | `/17173/news` | Online / MMO game news |
| 16 | TapTap | `/taptap/topic/:id/official` | Mobile game community (swap `:id` for specific games) |

### Tier 4 — China (web scrape fallback)

No RSS available. Used only when user explicitly requests these sources.

| # | Outlet | Homepage | Focus |
|---|--------|----------|-------|
| 17 | GameLook | `https://www.gamelook.com.cn/` | Industry deep-dives, business analysis |
| 18 | Youxiputao (游戏葡萄) | `https://www.youxiputao.com/` | Industry trends, company profiles |
| 19 | Youxituoluo (游戏陀螺) | `https://www.youxituoluo.com/` | Market data, industry reports |
| 20 | NGA | `https://bbs.nga.cn/` | Player leaks, community discussion |

## Usage

| Trigger | Example |
|---------|---------|
| Slash command | `/game-daily` |
| Chinese keyword | `最新游戏资讯` / `游戏圈有什么新闻` |
| English keyword | `What's new in gaming today?` |
| Korean keyword | `요즘 게임 뉴스 뭐 있어?` |
| Platform-specific | `PS5 最新消息` / `PC game news` / `手游动态` |

## What it does

1. Fetches RSS feeds from up to 20 gaming outlets (international + Chinese) simultaneously
2. Deduplicates stories covered by multiple sources
3. Adapts source selection based on platform/topic of interest (e.g., PC → RPS + 3DM; mobile → TapTap + Gcores)
4. Summarizes top 5 stories in 2-3 sentences each, in the user's language (ZH/EN/KO)
5. Fetches full article on demand for deeper summaries
6. Supports tracking specific games on TapTap via game ID

## Changes from gamer-news-skill

- **Added 11 Chinese gaming sources** across 3 tiers (native RSS, RSSHub, web scrape)
- **Smart source selection** expanded with Chinese-interest categories (国产游戏, mobile gaming, industry/business)
- **Multi-language output** now defaults to Chinese for zh-CN users, with translated article titles
- **RSSHub fallback** for sources without native RSS, with self-host guidance
- **Tier 4 web scrape** for industry-focused outlets (GameLook, Youxiputao, etc.)

## License

MIT — same as the original [gamer-news-skill](https://github.com/byeolbit/gamer-news-skill).
