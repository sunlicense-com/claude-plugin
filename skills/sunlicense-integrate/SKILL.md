---
name: sunlicense-integrate
description: Add SunLicense license checks to the user's software (Minecraft plugins and mods, FiveM resources, Discord bots, desktop apps, games, tools) with an official SDK, test them with a real license, and manage SunLicense products, licenses and API keys. Use when the user asks to add licensing, license keys, activation or anti-piracy checks with SunLicense, to protect a plugin or app they sell, or mentions SunLicense, sunlicense.com or an slk_/slp_ key.
---

# Add SunLicense to a project

SunLicense issues license keys for software people sell, and the software checks its key with one HTTP
call. Your job: wire that check into the user's project the way the official docs and SDKs do it,
without ever weakening it or leaking a key.

## 1. Read the docs first

Fetch what you need instead of guessing (they're written for assistants):

- Index: https://docs.sunlicense.com/llms.txt
- Every page in one file: https://docs.sunlicense.com/llms-full.txt
- Any page as Markdown: add `.md`, e.g. https://docs.sunlicense.com/guide/sdks.md,
  https://docs.sunlicense.com/api/validation.md, https://docs.sunlicense.com/guide/offline-licenses.md

If the `sunlicense` MCP server is connected, its `search_docs` tool searches the same text.

## 2. Pick the starting point

- **A template fits?** Start from it rather than from scratch (each has CI and an `AGENTS.md`):
  Paper and Velocity plugins, a Fabric mod, a FiveM resource, Discord bots in JavaScript and Java.
  List and links: https://docs.sunlicense.com/guide/templates.md
- **Otherwise use the official SDK** for the project's language (https://github.com/sunlicense-com):
  Java `com.sunlicense:sunlicense`, JavaScript `npm install sunlicense`, Python `pip install sunlicense`,
  .NET `dotnet add package SunLicense`, PHP `composer require sunlicense/sunlicense`, Lua
  `luarocks install sunlicense`. Read the SDK's README for the current API.
- **No SDK** (Rust today, or another language): `POST https://api.sunlicense.com/api/v2/license/validate`
  with JSON `{"license": "<key>", "host": "<stable machine id>"}` and `Authorization: Bearer <API key>`.

## 3. Rules for the check

1. **Two different secrets.** The seller's **API key** (`slk_…`) goes in the build or an environment
   variable (`SUNLICENSE_API_KEY`), never in source control. The **customer's license key** comes from the
   app's config file, a launch argument or a store placeholder. A personal access token (`slp_…`) is for
   the CLI and MCP only: never put one in shipped code.
2. **Send a stable machine id as `host`** (a stored random id or a hash of hardware ids, never the IP), so
   each machine counts as one activation.
3. **HTTP 200 = valid. 400 = a definite no**: read the body's `code` (`EXPIRED`, `REVOKED`, `SUSPENDED`,
   `TOO_MANY_MACHINES`, `NOT_FOUND`, `BLACKLISTED`) and show a clear message. **No answer is not a no**:
   on a network error, 5xx or 429, fall back to the signed offline license file if the project has one,
   otherwise keep working and retry later (fail open).
4. **Check off the main/UI thread** and once at startup (plus an occasional re-check for long-running
   servers). Paid features start only after a valid answer.
5. **Mask license keys** in logs and messages (`AX8P-XXXX-XXXX-XXXX`). Never print the full key.
6. **Never add a bypass**, debug flag or "offline mode" that skips the check. If the user wants their own
   machine left alone, tell them to add an *exclusion* rule in SunLicense (Blacklists and exclusions).
7. Store placeholders (`%%__SUNLICENSE__%%` on BuiltByBit and Voxel Shop) must appear exactly once in
   the code, as the constant the store replaces.

## 4. Test it for real

With the SunLicense MCP server connected (the remote server at https://api.sunlicense.com/mcp, or
`sunlicense mcp` from the CLI; every plan, Free has 50 tool calls a day) you can do the whole loop without
leaving the editor: `list_products` / `create_product` and `create_policy`, `issue_license` to the user's own
email, `create_api_key` for the app (shown once: put it in the env var, not in a file you commit),
`sdk_snippet` for the language, and `test_validate` (CLI server only) or `sunlicense validate <key>` to check
a key the way the app does. Destructive tools (revoke, suspend, block, merge, deactivate, revoke API key)
need the user's yes first.

Without MCP, the CLI does the same: `sunlicense validate <key>`, `sunlicense licenses issue`,
`sunlicense keys create` (https://docs.sunlicense.com/cli/commands.md). Ask the user for a test key if
neither is set up.

Finish by telling the user where the API key must be set at build or run time and which store
integration issues keys automatically (https://docs.sunlicense.com/integrations.md).
