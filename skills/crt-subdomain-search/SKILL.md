---
name: crt-subdomain-search
description: Query crt.name's public certificate-transparency index for historical subdomains of a registrable domain. Use for subdomain inventory and asset leads, not DNS, HTTP, port, or ownership validation.
---

# crt.name Subdomain Search

## Goal

Query crt.name with one confirmed apex domain (eTLD+1, such as `example.com` or `example.co.uk`) and return its indexed historical subdomains. When requested, include each name's first-seen date.

## Input and boundaries

- Accept a bare domain or an `http://` / `https://` hostname. Strip the scheme and normalize case; reject user info, ports, paths, queries, and fragments.
- Do not infer an apex by removing labels from a supplied subdomain. The user must confirm the eTLD+1.
- Reject malformed input before making a request. For a syntactically valid non-apex, use crt.name's authoritative `400 invalid apex` response; for example, `www.example.com` identifies `example.com` as the apex.
- The index is historical certificate-transparency and other public-source data. A returned name is not evidence that it currently resolves, is reachable, is owned by the user, or exposes a service.
- Use only the public `GET https://crt.name/v1/search` endpoint. Do not use the closed-beta `/v1/find`, `/v1/top`, or `/v1/stream` endpoints, and do not actively probe returned hosts.

## Query contract

| Need | Request | Success output |
|---|---|---|
| Subdomain list | `GET /v1/search?apex=APEX` | One hostname per `text/plain` line |
| First-seen dates | `GET /v1/search?apex=APEX&format=json&dates=1` | JSON records: `{ "sub", "first_seen" }` |

No token is required. crt.name's free allowance is 100 requests per IP per day. Responses can be large: count and filter before reporting, and provide the full result as a file when needed instead of pasting it into chat.

## Execution and reporting

1. Parse input to a single hostname. For a URL, use a URI parser; for a bare input, reject `/`, `?`, `#`, `@`, and `:`. Convert internationalized domains to ASCII/Punycode and URL-encode the query parameter.
2. Use text output for ordinary lists. Add `format=json&dates=1` only when dates or structured processing are needed.
3. Preserve HTTP status, content type, and a short response excerpt for diagnostics. On success, report the queried apex, result count, format, and any applied filter. `first_seen` may be `null`.
4. Treat responses as follows:
   - `200`: parse and return; an empty body or array means no indexed records.
   - `400`: report the API's apex correction; do not retry.
   - `429`: report rate limiting and `Retry-After` when present; do not automatically retry.
   - Other `4xx/5xx`, timeout, DNS/TLS failure, or unexpected content type: retain status, error type, and a short body excerpt. Never turn a failure into an empty result or silently substitute another data source.

PowerShell example for JSON with dates:

```powershell
$apex = 'example.com'
$uri = "https://crt.name/v1/search?apex=$([uri]::EscapeDataString($apex))&format=json&dates=1"
$response = Invoke-WebRequest -UseBasicParsing -SkipHttpErrorCheck -Uri $uri -TimeoutSec 30
if ($response.StatusCode -ne 200) {
  throw "crt.name HTTP $($response.StatusCode): $($response.Content)"
}
$response.Content | ConvertFrom-Json | Select-Object sub, first_seen
```

## Acceptance criteria

- Every request contains one URL-encoded apex and reports whether it used text or JSON-with-dates output.
- Text results parse into newline-delimited names; JSON-with-dates results parse into `sub` and `first_seen` records.
- Non-apex input, rate limits, timeouts, and HTTP errors remain diagnosable and are never reported as no results.
- Reports distinguish historical index records from currently reachable assets.