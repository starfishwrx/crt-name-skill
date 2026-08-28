# crt.name 历史子域名查询 Skill / Historical Subdomain Search Skill

这是一个可移植的 Markdown Skill，用于查询 [crt.name](https://crt.name/) 的公开索引。给定一个根域名（apex domain / eTLD+1，如 `example.com` 或 `example.co.uk`），它可以返回 **crt.name 已索引的全部历史子域名记录**；需要时还可以返回每个名称的首次发现日期。

This is a portable Markdown skill for querying [crt.name](https://crt.name/)'s public index. Given an apex domain (eTLD+1, such as `example.com` or `example.co.uk`), it returns **all historical subdomain records indexed by crt.name** for that domain, optionally including each name's first-seen date.

> 返回的是 crt.name 已收录的完整历史索引，不等于互联网上实际存在过的全部子域名，也不证明当前可访问、归属或在线状态。
>
> Results cover crt.name's complete indexed history for the apex, not every subdomain that has ever existed on the Internet. A result does not prove current DNS resolution, reachability, ownership, or an exposed service.

## 核心能力 / Core capability

- 查询一个根域名下全部已索引的历史子域名。
- 可选 JSON 输出，包含 `sub` 和 `first_seen`。
- 区分非法输入、非根域名、限流、超时和 HTTP 错误，避免把失败误报成空结果。
- 不主动探测返回的主机，也不把历史发现结果当作当前资产验证。

- Query every historical subdomain currently indexed for one apex domain.
- Optionally request JSON records containing `sub` and `first_seen`.
- Keep malformed input, non-apex input, rate limits, timeouts, and HTTP failures diagnosable instead of treating them as empty results.
- Do not actively probe returned hosts or mistake historical discovery for current asset validation.

## 不依赖 Codex / Not Codex-specific

它不依赖 Codex。`SKILL.md` 是通用 Markdown 指令，可以交给任何能读取 Markdown、调用 HTTP API 的 AI agent 或自动化系统使用，例如 Codex、Claude Code、Cursor、OpenCode、自建 agent，或人工按文档调用 API。

It is not tied to Codex. `SKILL.md` is portable Markdown guidance for any AI agent or automation that can read Markdown and call an HTTP API, including Codex, Claude Code, Cursor, OpenCode, custom agents, or a person using the API directly.

## 快速开始 / Quick start

文本清单（每行一个名称）/ Plain-text list (one hostname per line):

```bash
curl "https://crt.name/v1/search?apex=example.com"
```

带首次发现日期的 JSON / JSON with first-seen dates:

```bash
curl "https://crt.name/v1/search?apex=example.com&format=json&dates=1"
```

查询参数 `apex` 必须是根域名（eTLD+1）；例如对 `www.example.com` 查询时，服务会返回 `400` 并指出其根域名是 `example.com`。

The `apex` parameter must be an eTLD+1. For example, querying `www.example.com` returns `400` and identifies `example.com` as the apex.

## 使用 Skill / Using the skill

将 [SKILL.md](./SKILL.md) 提供给你的 agent，或按其中的请求契约自行实现。它定义了输入边界、文本与 JSON 输出、错误处理和结果报告要求。

Give [SKILL.md](./SKILL.md) to your agent, or implement its request contract directly. It defines input boundaries, text and JSON output, error handling, and reporting requirements.

## 服务限制 / Service limits

crt.name 的公开 `GET /v1/search` 接口无需 token；当前免费额度为每 IP 每天 100 次请求。请以 [crt.name](https://crt.name/) 的最新说明为准。

crt.name's public `GET /v1/search` endpoint needs no token; the current free allowance is 100 requests per IP per day. Refer to [crt.name](https://crt.name/) for the current service terms.

## License

MIT. 本仓库只包含面向公开 API 的使用指令，不包含 crt.name 的数据或服务代码。

MIT. This repository contains guidance for a public API; it does not include crt.name data or service code.