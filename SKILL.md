# Agent Hub

**触发词**：`agent hub`、`创建 agent 文件夹`、`统一管理 agent`、`set up agent hub`、`配置 agent`

为用户创建一个可跨 Claude / Cursor / OpenClaw 等 LLM 工具共享的中央 Agent 管理目录。

---

## 什么是 Agent Hub

一个本地文件夹，包含：
- **用户信息**（USER.md）：让所有 AI 工具了解你是谁
- **Agent 定义**（SOUL 文件）：每个 Agent 的身份、职责、协作方式
- **Skills 索引**：已安装的 skill 的归属与说明
- **总纲文件**（AGENTS.md）：启动入口，所有工具读取
- **可视化面板**（index.html）：本地浏览器一目了然查看配置

所有 LLM 工具指向同一个文件夹，无需为每个工具单独维护配置。

---

## 工作流

### Phase 1 · 了解用户

收集以下信息（可以逐步问，不要一次全问）：

```
1. 你叫什么名字？希望 AI 如何称呼你？
2. 你的职业/身份是什么？
3. 你在用哪些 LLM 工具？（Claude / Cursor / OpenClaw / 其他）
4. 你希望 Agent Hub 创建在哪个路径？（默认：~/agents/）
5. 你现在有哪些 Agent 需求？（可选，有默认值）
```

### Phase 2 · 创建目录结构

```
{hub_path}/
├── AGENTS.md          ← 总纲，所有工具启动时首先读取
├── index.html         ← 可视化面板，本地浏览器打开
├── user/
│   └── USER.md        ← 用户信息
├── agents/
│   ├── BOSS_SOUL.md   ← 默认 Boss Agent（必创建）
│   └── ...            ← 其他 Agent（按需）
└── skills/            ← 已安装 skills 放这里
```

### Phase 3 · 生成文件

按以下顺序生成：

**1. USER.md**
```markdown
# USER.md - {name}

## 基本画像
- **姓名**: {name}
- **称呼**: {nickname}
- **时区**: {timezone}
- **身份**: {role}

## 协作偏好
- {preferences}

## AI 团队规划
{agent_plan}
```

**2. BOSS_SOUL.md**（默认 Boss Agent，必须创建）
```markdown
# BOSS_SOUL.md - Boss Agent

_默认激活的统筹 Agent，理解用户的工作方式，分配任务给专项 Agent。_

## 身份
[根据用户信息定制]

## 统一资源目录
[填入实际 hub_path]

## Agent 调度规则
[根据用户的 Agent 需求填写]

## Continuity
每次唤醒时：
1. 读 {hub_path}/AGENTS.md
2. 读 {hub_path}/user/USER.md
3. 根据任务决定自己处理或激活专项 Agent
```

**3. AGENTS.md**（总纲）
```markdown
# AGENTS.md — {name}'s AI Team 总纲

> 所有 AI 工具启动时首先读取本文件。

## 目录结构
[实际路径]

## 主人
[用户基本信息]

## Agent 一览
| Agent | 文件 | 职责 | 默认激活 |
...

## Skills 一览
[如有已安装的 skills]

## 启动顺序
1. 读 AGENTS.md（本文件）
2. 读 user/USER.md
3. 读 agents/BOSS_SOUL.md
4. 按任务激活对应 Agent / Skill
```

**4. index.html**（可视化面板）

生成深色风格的本地 HTML 面板，展示：
- 用户信息卡片
- 所有 Agent 卡片（含颜色标识和默认标记）
- Skills 横向列表
- 配置来源路径

### Phase 4 · 配置 LLM 工具

根据用户使用的工具，配置对应的全局入口文件：

**Claude Code**（`~/.claude/CLAUDE.md`）：
```markdown
## 启动顺序
1. {hub_path}/AGENTS.md
2. {hub_path}/user/USER.md
3. {hub_path}/agents/BOSS_SOUL.md
4. 按任务激活对应 Agent / Skill
```

**Cursor**（`~/.cursor/rules/` 或项目 `.cursorrules`）：
```
Read {hub_path}/AGENTS.md at the start of every conversation.
Then read {hub_path}/user/USER.md and {hub_path}/agents/BOSS_SOUL.md.
```

**OpenClaw**（项目 `CLAUDE.md` 或全局配置）：
```markdown
## Agent Hub
所有 Agent 配置见 {hub_path}/AGENTS.md
```

### Phase 5 · 确认 & 收尾

完成后输出：
```
✅ Agent Hub 已创建：{hub_path}

文件清单：
- AGENTS.md（总纲）
- user/USER.md
- agents/BOSS_SOUL.md
- index.html（用浏览器打开可视化查看）

LLM 工具配置：
- Claude Code：已更新 ~/.claude/CLAUDE.md
- [其他工具...]

下一步：
- 在浏览器打开 {hub_path}/index.html 查看效果
- 如需添加专项 Agent，告诉我你的需求
- 如需安装 Skill，告诉我 GitHub 地址
```

---

## 设计原则

1. **单一来源**：所有 LLM 工具读取同一个文件夹，不维护多份副本
2. **Boss 优先**：默认激活 Boss Agent，有特定任务再分配专项 Agent
3. **渐进扩展**：从最小配置开始，按需添加 Agent 和 Skill
4. **可视化**：index.html 让配置一目了然，不用翻文件夹

---

## 注意事项

- Hub 路径建议用绝对路径（如 `/Users/name/agents/`），不用 `~` 简写，避免跨工具解析问题
- `skills/` 目录只存放 skill 文件，具体安装仍需各工具的 skill 安装机制（如 `npx skills add`）
- 每次新增 Agent 或 Skill，同步更新 `AGENTS.md` 和 `index.html`
