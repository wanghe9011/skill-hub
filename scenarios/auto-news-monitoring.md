# 自动追热点监控

> 让 AI 帮你盯着信息源，每天自动汇总值得关注的内容，不再手动刷资讯。

**适合**：内容创作者 · 运营 · 研究员 · 产品经理

**难度**：⭐⭐ 进阶

**预计搭建时间**：30 分钟

## 最终效果

搭完之后，你可以每天问 agent "今天有什么值得关注的"，它会：

1. 自动抓取你设定的信息源的最新内容
2. 过滤噪音，筛选出有价值的信息
3. 生成摘要，告诉你每条为什么值得看
4. 如果你需要深挖某条，直接搜索更多相关信息

## 你需要什么

- OpenClaw 已安装并正常运行
- （可选）自建 SearXNG 实例（用于搜索，Docker 一行命令）
- （可选）了解你要监控的 RSS 源地址

## 第一步：设置 RSS 监控

**用什么**：`blogwatcher`（OpenClaw 内置）

**说明**：blogwatcher 可以监控任意 RSS/Atom 源。国内很多网站没有 RSS，推荐用 [RSSHub](https://docs.rsshub.app/) 桥接。

**常用中文信源的 RSS 地址**（需部署 RSSHub）：

| 信源 | RSSHub 路由 |
|------|-------------|
| 36氪快讯 | `/36kr/newsflashes` |
| 虎嗅首页 | `/huxiu/article` |
| 知乎热榜 | `/zhihu/hot` |
| 微博热搜 | `/weibo/search/hot` |
| 少数派首页 | `/sspai/matrix` |
| V2EX 热门 | `/v2ex/topics/hot` |
| Hacker News | 原生 RSS 可用 |

**怎么用**：

在对话中告诉 agent：
```
帮我监控这些 RSS 源：
- https://rsshub.example.com/36kr/newsflashes
- https://rsshub.example.com/huxiu/article
每天汇总一次更新
```

**验证**：问 agent "最近 36 氪有什么新消息"，看是否能返回内容。

---

## 第二步：设置搜索能力

**用什么**：`searxng`

**为什么需要**：RSS 监控是"等信息来"，搜索是"主动找信息"。两个配合使用。

**怎么装**：
```bash
openclaw skills install searxng
```

**前置条件**：需要一个 SearXNG 实例

```bash
# Docker 一行命令搭建
docker run -d -p 8080:8080 searxng/searxng
```

**配置**：

在 `~/.openclaw/openclaw.json` 中：
```json
{
  "skills": {
    "entries": {
      "searxng": {
        "env": {
          "SEARXNG_URL": "http://localhost:8080"
        }
      }
    }
  }
}
```

在 workspace 的 MEMORY.md 中加一行：
```
搜索优先使用 searxng
```

**验证**：问 agent "搜一下最近 AI agent 领域有什么新动态"，看是否通过 SearXNG 返回结果。

---

## 第三步：搜到内容后快速提取和摘要

**用什么**：

| Skill | 作用 |
|-------|------|
| `fetch_url_text` | 从 URL 提取网页正文 |
| `summarize` | 对正文做摘要 |

**怎么用**：

给 agent 一个链接：
```
帮我看看这篇文章讲了什么：https://example.com/article
```

Agent 会自动提取正文 → 生成摘要 → 返回核心内容。

---

## 第四步：固化为日常流程

把上面的步骤变成你的日常习惯：

**每天早上**：
```
今天监控的信息源有什么更新？帮我筛选出最值得关注的 5 条，每条一句话说明为什么值得看。
```

**看到感兴趣的**：
```
帮我深挖一下 xxx 这个话题，搜一下更多相关信息。
```

**要写文章时**：
```
把今天收集的关于 xxx 的素材整理一下，列出关键信息点。
```

## 用到的 Skill

| Skill | 分类 | 作用 | 评测 |
|-------|------|------|------|
| `blogwatcher` | 信息获取 | RSS/博客监控 | 待写 |
| `searxng` | 信息获取 | 隐私搜索 | [已评测](../skills/searxng.md) |
| `fetch_url_text` | 信息获取 | 网页正文提取 | 待写 |
| `summarize` | 内容处理 | 文本摘要 | 待写 |

## 常见问题

### 国内网站没有 RSS 怎么办？
用 [RSSHub](https://docs.rsshub.app/) 把网站内容桥接成 RSS。可以自建（Docker 部署）或用公共实例。

### SearXNG 搜索结果质量不好？
取决于上游引擎配置。建议在 SearXNG 设置中至少启用 Google、DuckDuckGo、Brave 三个引擎。

### 能不能完全自动化，不用每天手动问？
可以。OpenClaw 支持 Heartbeat（定时任务），配置后 agent 每天定时执行并把结果推送给你。

## 进阶玩法

- **加上 Hacker News 监控**：用 `hn_fetch_latest` skill 自动抓取 HN 热门
- **搭配 AI 编辑部**：监控结果直接喂给写稿 agent，实现"追热点 → 出稿"半自动化
- **多语言监控**：SearXNG 支持 `--language` 参数，可以同时监控中英文信源
