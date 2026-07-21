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
- Credentials, either:
  - Application Default Credentials — `gcloud auth application-default login`
    with the `adwords` and `analytics.readonly` scopes, or
  - **(Recommended for headless/server use)** A service account JSON key.
    No scopes are set on the key itself — instead, grant the service
    account's email access directly:
    - **Google Ads**: Tools & Settings → Access and Security → Users → add
      the service account email with Standard access.
    - **Google Analytics**: Admin → Property Access Management → add the
      service account email as Viewer, for each property you want to query.

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
   see the tools/resources they expose. If you edit `.env` after the
   servers already connected, run `/mcp reconnect` to pick up the change.

## Notes

- `.env` is gitignored — never commit real credentials.
- `.mcp.json` doesn't use `${VAR}`-style substitution (that only expands
  from Claude Code's own process environment, not from `.env`). Instead,
  each server's `command` is a small `bash -c` wrapper that sources `.env`
  from the project root before exec'ing the real server, so plain `.env`
  edits take effect on next connect/reconnect.
- The Google Ads server currently has no PyPI release, so it's installed
  straight from the GitHub repo via `pipx run --spec git+...`. The Analytics
  server is installed from PyPI (`analytics-mcp`).
- Google Ads MCP tools are namespaced by default, e.g.
  `customers_list_accessible_customers`, not `list_accessible_customers`.
