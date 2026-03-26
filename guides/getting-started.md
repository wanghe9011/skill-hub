# 从零开始：怎么安装和使用 Skill

## 什么是 Skill

Skill 是一个文件夹，里面有一个 `SKILL.md` 文件，用来教 OpenClaw agent 怎么完成特定任务。

你可以把 Skill 理解为 agent 的"技能包"：装上之后，agent 就会了一个新能力。

## Skill 装在哪里

OpenClaw 从三个地方加载 Skill（优先级从高到低）：

1. **工作区 skills**：`~/.openclaw/workspace/skills/`（当前 agent 专属）
2. **共享 skills**：`~/.openclaw/skills/`（所有 agent 共享）
3. **内置 skills**：随 OpenClaw 安装包自带

同名 Skill，优先级高的覆盖低的。

## 安装 Skill

### 方式一：从 ClawHub 安装（推荐）

```bash
# 搜索
openclaw skills search "关键词"

# 安装
openclaw skills install <skill-slug>

# 更新全部
openclaw skills update --all
```

### 方式二：手动安装

把 Skill 文件夹放到 `~/.openclaw/workspace/skills/` 下：

```bash
# 例如安装一个叫 my-skill 的 Skill
cp -r my-skill ~/.openclaw/workspace/skills/
```

### 方式三：Git clone

```bash
cd ~/.openclaw/workspace/skills/
git clone https://github.com/xxx/my-skill.git
```

## 安装后激活

安装后需要开启新会话才能生效：

```bash
# 在聊天中输入
/new

# 或重启 gateway
openclaw gateway restart
```

验证是否加载成功：

```bash
openclaw skills list
```

## 检查 Skill 依赖

有些 Skill 需要额外的 CLI 工具或 API Key：

```bash
# 检查所有 Skill 的依赖状态
openclaw skills check
```

## 常见问题

### 装了但 agent 没反应？

- 确认开了新会话（`/new`）
- 运行 `openclaw skills list` 看是否在列表中
- 检查 `openclaw skills check` 是否有缺失依赖

### 怎么卸载？

删掉对应的 Skill 文件夹，开新会话即可。

### 怎么判断一个 Skill 安全不安全？

- 打开 `SKILL.md` 阅读内容
- 重点关注是否有 `exec` 类指令
- 检查是否有数据外传的网络请求
- 参考本仓库的评测结果
