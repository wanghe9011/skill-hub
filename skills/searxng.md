# SearXNG

> 用本地 SearXNG 实例做隐私搜索，替代 Brave/Google API，无需外部 API Key。

## 基本信息

| 项目 | 内容 |
|------|------|
| 名称 | `searxng` |
| 来源 | ClawHub |
| 作者 | Avinash Venkatswamy |
| 版本 | 1.0.1 |
| 平台 | 全平台（macOS / Linux / Windows） |
| 依赖 | Python 3.11+、uv、本地 SearXNG 实例 |
| 适合谁 | 注重隐私、想摆脱商业搜索 API 的用户 |

## 评分

| 维度 | 分数 | 说明 |
|------|------|------|
| 🔧 可用性 | 3/5 | 需要自建 SearXNG 实例，对新手有门槛 |
| 🎯 有效性 | 4/5 | 搜索质量取决于 SearXNG 配置的上游引擎，聚合多引擎结果比单一搜索好 |
| 🛡️ 安全性 | 5/5 | 纯本地请求，不依赖外部 API Key，无数据外传 |
| 📖 文档质量 | 4/5 | 命令示例清晰，配置说明完整，缺少 SearXNG 本身的部署指南 |
| 🔄 维护状态 | 4/5 | 当前版本 1.0.1，结构清晰，代码质量好 |
| 🌐 中文适配 | 4/5 | 支持 `--language zh` 参数，中文搜索效果取决于 SearXNG 上游引擎配置 |
| **综合** | **⭐⭐⭐⭐** | **推荐使用** |

## 这个 Skill 做什么

让 OpenClaw agent 通过你自建的 SearXNG 实例搜索网页，替代内置的 Brave Search。

核心优势：
- **隐私**：搜索请求只发到你自己的 SearXNG，不经过第三方
- **免费**：不需要任何 API Key
- **聚合**：SearXNG 后面挂多个搜索引擎（Google、DuckDuckGo、Brave 等），结果比单一引擎全
- **灵活**：支持分类搜索（网页、图片、新闻、视频）、语言筛选、时间范围

## 安装方法

```bash
openclaw skills install searxng
```

### 前置条件

需要一个可用的 SearXNG 实例。如果没有，用 Docker 快速搭一个：

```bash
docker run -d -p 8080:8080 searxng/searxng
```

### 配置

在 `~/.openclaw/openclaw.json` 中设置 SearXNG 地址：

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

## 实测记录

### 测试环境

- 系统：macOS (Apple Silicon)
- OpenClaw 版本：当前最新
- SearXNG：Docker 自建实例
- 测试日期：2026-03-26

### 测试过程

**基础中文搜索**：直接在对话中说"搜一下中国域名备案要多久"，agent 自动调用 SearXNG skill，返回多引擎聚合结果（startpage、duckduckgo、brave、google），结果质量好。

**语言参数**：使用 `--language zh` 后中文结果更精准。

**分类搜索**：支持 general / images / news / videos 等分类，实测 news 分类可以搜到近期新闻。

**输出格式**：table 模式可读性好，json 模式方便程序处理。

### 测试结果

- ✅ 中文搜索正常，结果质量与配置的上游引擎强相关
- ✅ 多引擎聚合，同一条结果标注了来自哪些引擎
- ✅ 响应速度快（本地实例，无网络延迟）
- ⚠️ 需要先自建 SearXNG 实例，这是最大的门槛

## 优点

- 完全隐私，零外部 API 依赖
- 搜索质量高（多引擎聚合）
- 支持中文、多语言
- 代码质量好，用了 httpx + rich，输出美观
- 自动绕过本地代理，处理了自签证书

## 缺点

- 需要自建 SearXNG 实例（Docker 一行命令，但仍是门槛）
- SKILL.md 没有包含 SearXNG 部署指南
- 搜索质量强依赖 SearXNG 的上游引擎配置，默认配置可能效果一般

## 适用场景

- ✅ 适合：注重隐私的用户、已有 SearXNG 实例的用户、想替代 Brave Search API 的用户
- ✅ 适合：作为 agent 的默认搜索工具（设置后对话中自然触发）
- ❌ 不适合：不想折腾自建服务的用户、只需要偶尔搜一下的轻度用户

## 使用技巧

1. 在 MEMORY.md 中写上"搜索优先使用 searxng"，agent 就会默认用它而不是 Brave
2. SearXNG 引擎配置很关键，建议至少启用 Google、DuckDuckGo、Brave 三个引擎
3. 中文搜索加 `--language zh` 效果更好

## 相关 Skill

- [fetch_url_text](./fetch_url_text.md) — 搜到结果后，用这个提取网页正文
- [summarize_text](./summarize_text.md) — 搜到内容后，用这个做摘要
