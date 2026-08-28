# crt.name Subdomain Search Skill

A self-contained agent skill for querying [crt.name](https://crt.name/), a public index of certificate-transparency subdomain records.

## Included capability

- List indexed historical subdomains for one apex domain.
- Request JSON records with optional first-seen dates.
- Validate request shape and preserve actionable API failures.
- Keep historical discovery separate from DNS, HTTP, port, and ownership validation.

Read [SKILL.md](./SKILL.md) when an agent needs this capability. It documents the API contract, rate-limit behavior, input constraints, and a PowerShell example.

## Upstream service

crt.name provides a no-token public `GET /v1/search` endpoint. Its free tier is limited to 100 requests per IP per day. See [crt.name](https://crt.name/) for the current service contract.

## License

MIT. This repository contains agent guidance written for crt.name's public API; it does not include crt.name data or service code.