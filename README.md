# Creatify Agent Plugin

The Creatify Agent Plugin connects Codex to [Creatify](https://creatify.ai) so you
can create and edit video content with AI assistance.

Use it to generate AI avatar videos, clone voices and generate voiceovers,
generate image and video assets, run the Creatify creative agent for ad
creatives, manage products and brands, and upload media — all through the
Creatify MCP tools.

## What Is Included

- `creatify/` - the Creatify Codex plugin package.
- `creatify/.codex-plugin/plugin.json` - plugin metadata used by Codex.
- `creatify/.mcp.json` - MCP server configuration for Creatify.
- `creatify/skills/` - workflow skills for common Creatify tasks.

## Requirements

- A Creatify account.
- Codex with plugin support.

## Install

```text
<BUNDLED_CODEX> plugin marketplace add https://github.com/creatify-ai/Creatify-agent-plugin.git --ref main
```

Then authenticate the Creatify MCP server (`codex mcp login creatify`) and follow
the sign-in flow shown by Codex.

## Authentication

The plugin connects to Creatify through the hosted Creatify MCP endpoint:

```text
https://api.creatify.ai/mcp
```

Codex handles authentication (OAuth 2.1 + PKCE) when the plugin is installed or
first used. Follow the sign-in flow shown by Codex to connect your Creatify
account. No tokens are stored in this repository.

## Example Prompts

After installing and authenticating the plugin, try prompts like:

- `Create an AI avatar video from this script.`
- `Clone this voice and generate a voiceover.`
- `Generate a product ad creative in Creatify.`
- `Generate an image or video asset in Creatify.`

## Repository

The public plugin repository is:

```text
https://github.com/creatify-ai/Creatify-agent-plugin.git
```

## Support

For product information, visit [creatify.ai](https://creatify.ai).
