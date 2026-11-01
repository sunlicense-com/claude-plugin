# SunLicense plugin for Claude

Lets [Claude](https://claude.com) (Claude Code, claude.ai and Cowork) add [SunLicense](https://sunlicense.com)
license checks to your software and manage your licenses for you.

- **`sunlicense-integrate` skill**: how to add a license check the right way (official SDKs, API key
  outside source control, stable machine ids, fail open when SunLicense can't be reached, masked keys,
  no bypasses), reading [the docs written for assistants](https://docs.sunlicense.com/llms.txt).
  Works on every SunLicense plan.
- **`sunlicense` MCP server**: SunLicense's remote MCP server at `https://api.sunlicense.com/mcp`. List
  products, policies, licenses and customers, issue a test license, create an API key, free a machine,
  block a leaked key's machine, read licensing and validation stats, and search the docs. The first time a
  tool runs, Claude opens SunLicense in your browser: sign in, choose what Claude may do, and choose
  **Connect**. MCP is included in every plan: **Free** (50 tool calls a day), **Lite** (300) and
  **Standard** (3,000).

## Install

In Claude Code:

```text
/plugin marketplace add sunlicense-com/claude-plugin
/plugin install sunlicense@sunlicense
```

Then ask, for example: *"Add SunLicense to this Paper plugin and test it with a new license for me"*.

Only want the skill? Copy `skills/sunlicense-integrate/` into your project's `.claude/skills/`. Prefer the
CLI with a personal access token (scripts, CI, no browser)? Remove this plugin's server and run
`claude mcp add sunlicense -- sunlicense mcp` (see [AI assistants (MCP)](https://docs.sunlicense.com/cli/mcp)).

## What it sends where

- **The MCP server** is SunLicense's own API. Each tool call sends the tool's arguments (for example a
  product name or a customer's email) to `api.sunlicense.com` and returns what your organization holds;
  SunLicense receives tool calls only, never your prompts, chats or code. Each call counts toward your
  organization's daily MCP allowance. Disconnect any time under **Settings → Access tokens → Connected AI
  apps** in the dashboard.
- **The skill** tells Claude to read SunLicense's public docs at `docs.sunlicense.com`.
- The plugin runs nothing on your machine: no hooks, scripts or local servers.

How SunLicense handles the data: [connected AI apps](https://docs.sunlicense.com/guide/remote-mcp#privacy-and-data)
and the [privacy policy](https://docs.sunlicense.com/legal/privacy).

## Good to know

- Claude acts as you: it can do what your role and the permissions you approved allow. Untick
  permissions you don't want to give when you connect.
- Keys stay masked in answers; Claude reveals a full key only when you ask. Tools that change what
  customers can do (revoke, suspend, block, merge, delete) are marked destructive, so Claude asks first.

## Support

[docs.sunlicense.com](https://docs.sunlicense.com) · hello@sundevs.com · [status.sunlicense.com](https://status.sunlicense.com)

## License

[MIT No Attribution](LICENSE).
