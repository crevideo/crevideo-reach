# TikTok Shop 达人分销 · 建联与营销技能包（Claude 插件）

> **Crevideo Reach** —— 把 Claude（Cowork / Claude Code）变成你的 TikTok Shop 达人分销运营助手：找人、建联、起草、邀约、复盘；Euka、Reacher 的平价平替。

> 🇨🇳 **中文版（本页）** · 🇺🇸 [English](./README.md)

> 1 个基础 skill + 8 个业务场景 skill + Crevideo Reach MCP（79 个工具）+ 5 个定时任务模板 + 2 个 Project Instructions
> 支持 Claude Code、Claude.ai 网页版、OpenAI Codex 三端。

> **面向**：TikTok Shop **中国跨境（POP）** 卖家做达人分销建联、邀约、谈判、私信邮件外发、复盘。美国本土卖家请看 [English version](./README.md)。

---

## 先确认：你用哪个客户端？

不同客户端装法不同。**这事 Anthropic 设计上区别对待**：

| 客户端 | 入口 | 步骤数 |
|---|---|---|
| **Claude Code**（终端 / VS Code 插件 / 桌面端 Code 标签） | `/plugin` 命令 | **1 步** |
| **Claude.ai 网页版**（浏览器打开 claude.ai） | UI 里的 Customize 页 | **1 步**（plugin 装完 connector 自动出现，首次用工具时走 OAuth 登录） |
| **OpenAI Codex** | 客户端 → "添加插件市场" | **1 步**（来源 + 稀疏路径 `.agents/plugins`） |

> Anthropic 官方说明：[claude.ai 上的 MCP 必须手动添加，没有自动注册功能](https://support.claude.com/en/articles/11175166-about-custom-integrations-using-remote-mcp)。Plugin 里的 `mcpServers` 字段**只在 Claude Code 自动生效**，不在 Claude.ai 网页生效。

---

## A. Claude Code 用户（开发者 / 技术运营）— 一步装

在终端或 VS Code 里敲：

```
/plugin marketplace add crevideo/crevideo-reach
/plugin install crevideo-reach@crevideo
```

装完会发生两件事：
1. **9 个 skill 自动加载** —— 用业务话术触发（"给这批达人打分"、"写条触达文案"）
2. **MCP 工具自动连接** —— 首次调用工具时弹浏览器走 OAuth → 跳到 app.crevideo.com 登录你的 Reach 账号 → 回来后 79 个工具都可以用

`/plugin list` 看装了什么，`/mcp` 看 MCP 连接状态。

---

## B. Claude.ai 网页版用户（运营同事）— 一步装

1. 打开 [claude.ai](https://claude.ai) → 左侧栏 → **Customize**
2. 点 **Plugins** tab → 右上角 "+" → **Add marketplace**
3. URL / 来源填：`crevideo/crevideo-reach`
4. 点 Add → 装好后插件名显示 "Crevideo Reach"
5. **Skills + connector 都自动加载**：
   - Skills tab → 9 个 skill 出现
   - Connectors tab → `crevideo-reach` 自动出现（指向 `https://mcp.crevideo.com/reach`）

### 首次用 MCP 工具

- 开个新对话 → 左下角 "+" → Connectors → 勾上 Crevideo Reach
- 问一句 "看下我有哪些店铺" → 触发 OAuth 登录（弹浏览器 → 跳 app.crevideo.com → 回来）
- 之后 `list_shops` 调用就返回你的真实店铺列表

> ⚠️ **如果 `crevideo-reach` 没有自动出现在 Connectors 里** → 你装的是旧版缓存。**先卸载插件 → 重装一次。** 只有 v2.0+ 才会把 MCP 自动注册到 Connectors。

---

## C. OpenAI Codex 用户 — 一步装

Codex 期望插件放在 `plugins/codex/` 子目录下（跟 Claude Code "插件放 repo 根" 的约定不同）。我们在同一个 repo 里同时维护两套结构。

在 Codex 客户端 → **插件** → 右上角 "+" → **添加插件市场**：

| 字段 | 填什么 |
|---|---|
| **来源** | `crevideo/crevideo-reach` |
| **Git ref** | main 分支（留空即可） |
| **稀疏路径** | `.agents/plugins` ← marketplace.json 所在目录（按 OpenAI 官方惯例） |

点 **添加市场** → 筛选里会出现 marketplace "Crevideo" → 找到 plugin "Crevideo Reach" → 安装 → 9 个 skill 加载完成。

> **MCP 注意**：Codex 当前对 HTTP+OAuth MCP transport 的文档比较薄。如果装好插件但 Codex 没自动连上 Crevideo Reach MCP，去 Codex 的 MCP 设置里手动加 URL `https://mcp.crevideo.com/reach`（跟 Claude.ai 网页版一样两步走）。

---

## 包含什么

### 🟢 Skills（9 个）

#### 基础 skill（1 个）

| Skill | 干什么 |
|---|---|
| `mcp-playbook` | MCP 工具地图 + 调用规则 + canonical 创建流程 + 错误码诊断 + 版本说明（v0.6.1 / 74 工具版） |

#### 业务场景 skill（8 个）

| Skill | 干什么 |
|---|---|
| `creator-fit-scoring` | 给一批达人按"能不能真带货"打分 + 推荐强度 |
| `creator-tier-resolver` | 判定达人分级（Open / T1 / T2 / T3·VIP）+ 该不该从 Open 毕业 |
| `offer-policy-checker` | 佣金 + 合规把关（佣金上限、免费样品、政策风险） |
| `brand-safety-compliance` | 文案合规扫描（敏感词、品牌雷区、平台风险） |
| `outreach-message-composer` | 起草触达文案（DM / 邮件 / TC 邀约） |
| `reply-triage` | 私信收件箱分诊（合作意向 / 拒绝 / 需要跟进） |
| `winback-and-pruning` | 唤回沉默达人 + 汰换不活跃达人 |
| `performance-diagnosis` | 自动化跑完后做效果诊断 + 给下一步建议 |

### 🟢 MCP Server: `crevideo-reach`（79 个工具）

通过 OAuth 连到 `https://mcp.crevideo.com/reach` —— 不需要手动配 API key。

能力覆盖：
- **官方定向邀约 TC（Target Collaboration）**：预览 / 创建 / 启停 / 删除 / 克隆 / 任务结果
- **DM 自动化**：单段 DM / TC+DM 组合 / 编辑活跃 DM 文案（`update_dm_automation_text`）
- **达人管理**：搜达人 / 找相似 / 详情 / 黑名单 / 标签 / 分群（segments）/ 漏斗（journeys）/ 达人列表
- **DM 私信**：会话 / 历史 / 发送
- **邮件**：模板列表 + 收件箱计数（发件/回复待后端 asset endpoint）
- **分销内容**：列表 / 商品
- **客户型达人（CA / Customer Advocates）**：列表 / 标签 / 备注 / 联系方式增强
- **店铺 / 商品 / 订单 / 报表**：店铺列表 / 商品列表+详情 / 店铺订单 / 分销报表（合作 / 商品 / 短视频）

### 🟡 Extras（不在插件标准内容里，需要手动配）

#### `extras/scheduled-tasks/` — 5 个定时任务模板

插件不自动装。Claude.ai 用户去 Settings → Scheduled Tasks 手动建 5 个任务，把每个 `.md` 正文贴进去：

| 模板 | 频率 | 用途 |
|---|---|---|
| `00-scheduled-task-safety-header-must-go-first.md` | 必读 | 所有定时任务的安全前置（放在每个任务正文最前） |
| `01-daily-morning-brief.md` | 每天早上 | 看昨天数据 / 今天 todo |
| `02-daily-midday-watch.md` | 每天中午 | 监控当天自动化 + 紧急回复 |
| `03-daily-end-of-day-handoff.md` | 每天晚上 | 当天结果汇总 + 给明天的人留言 |
| `04-weekly-optimization-review.md` | 周末 | 周维度复盘 + 调整下周策略 |

#### `extras/project-instructions/` — 2 个 Project Instructions

给 Claude.ai 的"Project"功能用。贴进两个独立的 Project：
- `monitor-project-instructions.md` —— 给"监控台"Project
- `action-project-instructions.md` —— 给"操作台"Project

#### `extras/brand-knowledge-pack-template.md`

客户自己填的品牌信息（品牌名、SKU 清单、品牌语气、避雷词等），9 个 skill 自动读。

**填好后放哪里**：
- **Claude.ai 网页版**：内容贴进 Project 的 Custom Instructions（Customize → Projects → 你的 Cowork Project → Custom Instructions）
- **Claude Code**：内容存到 `~/.claude/CLAUDE.md` 或项目级 `<project>/.claude/CLAUDE.md`

不填也能用，填了产出更贴品牌。

#### `extras/crevideo-reach-cowork-operations-handbook.md` + `extras/quickstart-card.md`

人看的文档，给运营同事自己读。

---

## 升级

### Claude Code

```
/plugin marketplace update crevideo
/plugin update crevideo-reach
```

升级后 MCP 连接保留；如果异常，`/mcp` 检查状态。

### Claude.ai 网页版

Customize → Plugins → 找 Crevideo Reach → 点 "Check for updates"（重新拉取） 或卸载重装。**Connector 是独立的** —— 升级 plugin 不会动 Connector 配置；如果 MCP URL 变了，去 Connectors tab 单独 disconnect + 重新 add 一次。

---

## 反馈

工具有问题或想加新场景，提 issue 给 Crevideo 团队。
