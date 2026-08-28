# crt.name Historical Subdomain Search Skill

[Chinese README](./README.md)

`crt-name-skill` is a portable Markdown skill. Given an apex domain (eTLD+1, such as `example.com` or `example.co.uk`), it queries [crt.name](https://crt.name/) for **every historical subdomain record currently indexed by crt.name** for that apex, with optional first-seen dates.

It is not tied to Codex. Any AI agent, automation workflow, or person that can read Markdown instructions and make HTTP requests can use it.

## What it does

| Input | Output |
|---|---|
| `example.com` | Every historical subdomain currently indexed by crt.name, one name per line by default |
| `example.com` with date mode | JSON records containing `sub` and `first_seen` |

Results are crt.name's historical index. They do not prove that a name currently resolves, is reachable, belongs to the user, or exposes a service.

## What the skill handles

- Accepts only an apex domain. A subdomain such as `www.example.com` receives the API's authoritative apex correction.
- Retains diagnostics for `400`, `429`, timeouts, DNS/TLS failures, and other HTTP errors instead of reporting failures as empty results.
- Does not actively probe returned hosts or treat historical discovery as current DNS, HTTP, port, or ownership validation.
- Counts and filters large responses before reporting them, rather than pasting every record into chat.

## Installation and use

Clone the repository, then give `skills/crt-subdomain-search/SKILL.md` to any agent or implement its API contract directly:

```bash
git clone https://github.com/starfishwrx/crt-name-skill.git
```

For Codex, install the same directory with the bundled installer:

```bash
python3 ~/.codex/skills/.system/skill-installer/scripts/install-skill-from-github.py \
  --repo starfishwrx/crt-name-skill \
  --path skills/crt-subdomain-search
```

After installation, invoke `$crt-subdomain-search`. Other agents can load the same `SKILL.md` directly.

## Quick query

Plain-text list:

```bash
curl "https://crt.name/v1/search?apex=example.com"
```

JSON with first-seen dates:

```bash
curl "https://crt.name/v1/search?apex=example.com&format=json&dates=1"
```

crt.name's public `GET /v1/search` endpoint requires no token. Its current free allowance is 100 requests per IP per day; refer to [crt.name](https://crt.name/) for current terms.

## Repository layout

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

MIT. This repository contains guidance for a public API; it does not include crt.name data or service code.