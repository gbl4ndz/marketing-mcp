# marketing-mcp

Connects Claude Code to Google's official marketing MCP servers so it can
query live Google Ads and Google Analytics data in natural language:

- [google-ads-mcp](https://github.com/googleads/google-ads-mcp) — campaigns,
  accounts, and performance metrics via the Google Ads API.
- [google-analytics-mcp](https://github.com/googleanalytics/google-analytics-mcp) —
  account info, standard/funnel reports, and real-time data via the Google
  Analytics Admin and Data APIs.

Both servers are registered project-wide in [`.mcp.json`](./.mcp.json), so
anyone who opens this repo in Claude Code gets them automatically (after
approving the servers and supplying credentials).

## Prerequisites

- [`pipx`](https://pipx.pypa.io/) installed and on `PATH` (both servers run
  via `pipx run`, no separate install step needed).
- A Google Cloud project with the following APIs enabled:
  - Google Ads API
  - Google Analytics Admin API
  - Google Analytics Data API
- A Google Ads **developer token** (Explorer access is enough for read-only
  querying): https://developers.google.com/google-ads/api/docs/get-started/dev-token
- Credentials with the right scopes, either:
  - Application Default Credentials — `gcloud auth application-default login`, or
  - A service account JSON key with:
    - `https://www.googleapis.com/auth/adwords`
    - `https://www.googleapis.com/auth/analytics.readonly`

## Setup

1. Copy the env template and fill in your values:
   ```bash
   cp .env.example .env
   ```
2. Populate `GOOGLE_APPLICATION_CREDENTIALS`, `GOOGLE_PROJECT_ID`,
   `GOOGLE_ADS_DEVELOPER_TOKEN`, and (if applicable)
   `GOOGLE_ADS_LOGIN_CUSTOMER_ID` in `.env`.
3. Open this project in Claude Code. It will pick up `.mcp.json` and prompt
   you to approve the `google-ads-mcp` and `google-analytics-mcp` servers.
4. Run `/mcp` inside Claude Code to confirm both servers are connected and
   see the tools/resources they expose.

## Notes

- `.env` is gitignored — never commit real credentials.
- The Google Ads server currently has no PyPI release, so it's installed
  straight from the GitHub repo via `pipx run --spec git+...`. The Analytics
  server is installed from PyPI (`analytics-mcp`).
- If `pipx` isn't available, either server can also be run directly with
  `uvx`/`pip` — swap the `command`/`args` in `.mcp.json` accordingly.
