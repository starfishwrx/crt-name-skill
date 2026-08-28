<p align="center"><img src="./assets/readme/hero.png" alt="crt.name 历史子域名查询" width="100%"></p>

# 历史子域名查询 Skill

[English README](./README.en.md)

`crt-name-skill` 是一个通用的 Markdown Skill。输入一个根域名（apex domain / eTLD+1，例如 `example.com` 或 `example.co.uk`），它会通过 [crt.name](https://crt.name/) 查询该根域名下 **所有已被 crt.name 索引的历史子域名记录**，需要时也会提供首次发现日期。

它不绑定 Codex。任何能读取 Markdown 指令并发起 HTTP 请求的 AI agent、自动化流程，或人工 API 调用都可以使用它。

## 能做什么

| 输入 | 输出 |
|---|---|
| `example.com` | crt.name 已索引的全部历史子域名；默认每行一个名称 |
| `example.com` + 日期模式 | JSON 记录，包含 `sub` 与 `first_seen` |

结果是 crt.name 的历史索引，不代表该名称当前可解析、可访问、归用户所有或开放服务。

## 使用时会处理什么

- 只接受根域名；`www.example.com` 这类子域名会得到服务端返回的根域名纠正信息。
- 保留 `400`、`429`、超时、DNS/TLS 和其他 HTTP 错误的诊断信息，不把失败写成无结果。
- 不主动探测查询结果，也不将历史发现误作当前 DNS、HTTP、端口或归属验证。
- 响应很大时先统计和筛选，再输出摘要或文件，而不是把所有记录塞进对话。

## 安装与使用

克隆仓库后，将 `skills/crt-subdomain-search/SKILL.md` 提供给任意 agent，或直接按照它定义的 API 契约实现调用：

```bash
git clone https://github.com/starfishwrx/crt-name-skill.git
```

在 Codex 中，可用内置安装脚本安装该目录：

```bash
python3 ~/.codex/skills/.system/skill-installer/scripts/install-skill-from-github.py \
  --repo starfishwrx/crt-name-skill \
  --path skills/crt-subdomain-search
```

安装后，调用 `$crt-subdomain-search`；在其他 agent 中，直接加载同一个 `SKILL.md` 即可。

## 快速查询

文本清单：

```bash
curl "https://crt.name/v1/search?apex=example.com"
```

带首次发现日期的 JSON：

```bash
curl "https://crt.name/v1/search?apex=example.com&format=json&dates=1"
```

crt.name 的公开 `GET /v1/search` 接口无需 token。当前免费额度为每 IP 每天 100 次请求，以 [crt.name](https://crt.name/) 的最新说明为准。

## 仓库结构

```text
crt-name-skill/
├── skills/
│   └── crt-subdomain-search/
│       └── SKILL.md
├── README.md
├── README.en.md
└── LICENSE
```

## License

MIT。本仓库只包含公开 API 的使用指令，不包含 crt.name 的数据或服务代码。
