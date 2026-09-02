# Crevideo Reach Test Plugin

This repository includes an isolated pre-release plugin for multi-market acceptance testing.

- Plugin ID: `crevideo-reach-test`
- Version: `3.3.0-test.1`
- MCP: `https://test-mcp.crevideo.com/reach`
- OAuth metadata: `https://test-mcp.crevideo.com/.well-known/oauth-authorization-server`
- Production plugin and production MCP are unchanged.

## Install locally

Disable `crevideo-reach` during acceptance so only the test skills and connector can trigger.

### Claude Code

```text
/plugin marketplace add /Users/a1234/Desktop/crevideo-reach
/plugin install crevideo-reach-test@crevideo-test
```

### OpenAI Codex

Add `/Users/a1234/Desktop/crevideo-reach/.agents/plugins` as a local plugin marketplace, then install `crevideo-reach-test` from marketplace `crevideo-test`.

Start a new conversation after installation so the client reloads skills and MCP tools.

## Read-only acceptance

1. Ask: `列出我的 Reach 店铺，并按国家显示 region、shop_cipher 和币种。`
2. For every authorized market, ask for three creators without filters and confirm returned creator region matches the selected market.
3. For US, JP, VN, ID and one EUR market, apply the lowest GMV and GPM preset shown by the test plugin. Confirm labels and values use that market's convention rather than US defaults.
4. Ask for creator GMV/GPM display in JP, VN, ID, EUR, BR and MX. Expected examples include `¥0`, `0₫`, `Rp0`, `0 €`, `R$0`, and `MX$0`.
5. Ask for products in two different markets and confirm `region`, `shop_cipher`, currency and stock are not mixed across shops.
6. Ask for newly published affiliate videos using a date range and pagination. Confirm the plugin returns the next cursor/token when more data exists.
7. Trigger `creator-fit-scoring`, `performance-diagnosis`, and `outreach-message-composer` in draft-only mode. Confirm each skill selects the test connector and preserves the chosen market context.

Do not run send/create/update/delete tools during read-only acceptance. Test write tools only in a separate account and only after explicit confirmation.
