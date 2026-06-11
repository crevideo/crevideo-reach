# Security Policy

> 🇺🇸 **English (this page)** · 🇨🇳 [中文版 / Chinese version](#安全策略-中文)

## Reporting a Vulnerability

If you discover a security vulnerability in this plugin, **please do not open a public GitHub issue**. Send a private report to **support@crevideo.com** with:

- A description of the issue
- Steps to reproduce (minimal example preferred)
- Affected version (commit SHA or release tag)
- (Optional) suggested fix or mitigation

We aim to acknowledge reports within **3 business days**. Once a fix is released, we will credit reporters in the release notes unless they ask to remain anonymous.

## Supported Versions

The latest release on the `main` branch is actively maintained. Older versions are not supported — please upgrade before reporting.

## Scope

This plugin packages skills + instructions + the `.mcp.json` pointer that connects to the **Crevideo Reach MCP** service hosted at `https://mcp.crevideo.com/reach`.

- **In-scope (this repo):** skills logic, prompt-injection vectors in the `extras/` operations code, manifest / OAuth configuration in `.mcp.json`, leaked secrets in commit history.
- **Out-of-scope but still report it:** vulnerabilities in the **MCP server itself** (backend, OAuth flow, API endpoints). Same email — please put **"MCP server"** in the subject line so we route it correctly.

## What to expect

| Step | When |
|---|---|
| Acknowledgement | within 3 business days |
| Severity assessment + repro confirmation | within 7 business days |
| Patch released or mitigation deployed | depends on severity (critical: ≤ 14 days) |
| Public disclosure | coordinated with reporter, typically after the patch is shipped |

Please do **not** publicly disclose the issue until we have shipped a fix.

---

## 安全策略 (中文)

> 🇨🇳 **中文版（本节）** · 🇺🇸 [English version above](#security-policy)

## 漏洞上报

如果你在本插件里发现安全漏洞,**请不要直接开 GitHub public issue**。发邮件到 **support@crevideo.com**,内容包含:

- 漏洞描述
- 复现步骤(最小可复现示例最好)
- 受影响版本(commit SHA 或 release tag)
- (可选) 建议的修复方案

我们承诺 **3 个工作日内**回复。修复发布后,会在 release notes 致谢上报者(除非你希望匿名)。

## 支持的版本

`main` 分支上的最新 release 是当前维护的版本,旧版本不再维护 —— 请先升级再上报。

## 适用范围

本插件包含 skills + instructions + `.mcp.json` 配置,后者指向 `https://mcp.crevideo.com/reach` 上托管的 **Crevideo Reach MCP** 服务。

- **本仓适用范围:** skills 逻辑、`extras/` 里 operations code 的 prompt-injection 风险、`.mcp.json` manifest / OAuth 配置、commit 历史里的 secret 泄露。
- **不在本仓但同样请上报:** **MCP 服务端**(后端 / OAuth 流程 / API 接口)的漏洞 —— 同一个邮箱,邮件主题请注明 **"MCP server"**,方便我们分流。

## 处理时效

| 阶段 | 时间 |
|---|---|
| 确认收到 | 3 个工作日内 |
| 严重度评估 + 复现确认 | 7 个工作日内 |
| 补丁发布 / 临时缓解上线 | 看严重度(critical: ≤ 14 天) |
| 公开披露 | 跟上报者协调,通常在补丁发布之后 |

补丁发布前**请勿公开披露**。
