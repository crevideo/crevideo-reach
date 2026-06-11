# 🚀 快速上手卡 · Crevideo Reach × Claude Cowork
### 一页纸 · 贴桌上 · 几分钟上手

> 🇨🇳 **中文版（本页）** · 🇺🇸 [English](./00-quickstart-card.md)

---

## ① 15 分钟首胜（零积分、零外发）

建好 Project、连好 MCP、把 Operations Code 粘进 Instructions 之后，把下面这段复制给 Claude：
```
We just connected Crevideo Reach. Please run a read-only "Customer-Creator First Win" — no outreach, no creates, no credit spend:
1. list_shops to confirm which shops I have; lock the shop_cipher.
2. List people who are both customers and creators (customer advocates); if empty, say "this account may not have the add-on enabled" and switch to list_affiliate_creators to find recently performing creators.
3. Pick 1 to prioritize: 3 reasons + 1 uncertainty + 1 ACTIVATE product to pair with.
4. Draft a sincere, zero-exaggeration outreach message — draft only, do not send.
Tell me which mode each step is in (should be 🟢 read-only + 🟡 draft).
```

你会看到：它认得你的店铺、挑出一个真实达人、写好触达文案、**但啥都没发** → 成功。

---

## ② 三档安全契约（最重要）

| 🟢 观察 Observe | 🟡 起草 Draft | 🔴 真做 Commit |
|---|---|---|
| 只读、免费 | 生成名单 / 文案，不外发不花钱 | 真发送 / 花积分 / 拉黑 / 删除 |
| 随便用 | 不需要确认 | **必须说"确认"才动手** |

> 🔴 任何 🔴 动作之前，Claude 必须给完整方案 + 一行 `[Pending · Awaiting your confirmation]` 然后停。
> **如果没看到这行字、或者它没停就动手 → 立刻发紧急停止咒语（G1）。**

---

## ③ 只记 5 个咒语就够了
```
A1 客户即达人（找最暖的线索）                  🟢🟡
B1 给一批达人打分排序                          🟢🟡
C2 发官方定向邀约 TC                            🔴 花钱 · 必须确认
D1 回复 5 桶分诊                                🟢🟡
E1 在跑自动化健康检查 + 效果                    🟢
```
（完整 16 个咒语在手册第 2 部分。）

---

## ④ 永远不会自动发生的事（放心）
- ❌ 不会自动发 DM 或邮件给达人
- ❌ 不会自动花积分
- ❌ 自动化不会自动启动（默认 draft，你说 "start" 才发）
- ❌ 不会自动拉黑 / 删除
- ✅ 以上动作都必须你**亲口**说"确认"

---

## ⑤ 三条保命规则
1. **别盯"发了多少条" — 盯"合格接受率 / 出视频率 / 净 GMV"。** 发太多会烧样品、烧佣金、烧账号信誉。
2. **自动化名字用简短英文**（比如 `grad_invite_v1`）—— 纯中文名会被平台归一成 "mcp"，多个自动化撞名。
3. **定时任务只在 Claude Desktop 开着、电脑不睡眠时才跑。** 工作时段保持开着。

---

## ⑥ 紧急情况怎么办

| 情况 | 立刻发给 Claude |
|---|---|
| 它好像没等确认就发出去了 | `Immediately pause all active automations created today; list anything started without my approval; estimate the damage; disable all scheduled tasks for the rest of today.` |
| 它在工具调用里转晕了 | `Reset. First list the tools you plan to use (≤5) + order + expected output. I'll review before you act.` |

---
*配套资料：完整 Handbook · 操作台/监控台 Project Instructions（Operations Code）· 5 个定时任务 · 专家模式（自动启用，免配置）。详见本目录其他文件。*
