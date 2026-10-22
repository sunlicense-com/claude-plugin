# SunLicense plugin for Claude Code

Lets [Claude Code](https://claude.com/claude-code) add [SunLicense](https://sunlicense.com) license
checks to your software and manage your licenses for you.

- **`sunlicense-integrate` skill**: how to add a license check the right way (official SDKs, API key
  outside source control, stable machine ids, fail open when SunLicense can't be reached, masked keys,
  no bypasses), reading [the docs written for assistants](https://docs.sunlicense.com/llms.txt).
  Works on every SunLicense plan.
- **`sunlicense` MCP server**: list products and licenses, issue a test license, create an API key, block
  a leaked key's machine and test a key the way your app does. It runs `sunlicense mcp` from the
  [SunLicense CLI](https://docs.sunlicense.com/cli/) on your machine. MCP is included in the **Lite**
  (300 tool calls a day) and **Standard** (3,000 a day) plans; on Free the skill still works and the
  server stops with an upgrade link.

## Install

1. [Install the SunLicense CLI](https://docs.sunlicense.com/cli/) (0.3.0 or later) and sign in with
   `sunlicense login`, or create a personal access token under **Settings → Personal access tokens** and
   export it before starting Claude Code: `export SUNLICENSE_TOKEN=slp_...`
2. In Claude Code:

   ```text
   /plugin marketplace add sunlicense-com/claude-plugin
   /plugin install sunlicense@sunlicense
   ```

3. Ask, for example: *"Add SunLicense to this Paper plugin and test it with a new license for me"*.

Only want the skill? Copy `skills/sunlicense-integrate/` into your project's `.claude/skills/`. Only want
the MCP server? `claude mcp add sunlicense -- sunlicense mcp` (see
[AI assistants (MCP)](https://docs.sunlicense.com/cli/mcp)).

## Good to know

- The assistant acts as you: it can do what your role and token scopes allow. Give a token only the
  scopes it needs; a `*.read` token can look but not change anything.
- Keys stay masked in answers; destructive tools (revoke, suspend, block, merge) ask you first.
- SunLicense receives the API calls the CLI makes, never your prompts or code.

## License

[MIT No Attribution](LICENSE).
