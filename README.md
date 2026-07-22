# Creatify Agent Plugin

The Creatify Agent Plugin connects Codex to [Creatify](https://creatify.ai) so
you can create AI avatar videos, voices, image/video assets, and full ad
creatives with AI assistance — with live Creatify pages opening in the host's
in-app browser as the work runs.

## Install (humans)

Paste this into a new conversation in the ChatGPT/Codex desktop app:

```text
Read github.com/creatify-ai/Creatify-agent-plugin to install the Creatify plugin and create my first ad.
```

That's it — the agent reads the guide below and does the rest, including the
sign-in flow.

---

## Agent install guide

If you are a Codex agent reading this for a user: read this whole guide before
running commands, then complete every step in order. Do not stop after
install, login, or verification — the required final step is starting the
user's first Creatify task.

### 0. Host environment gate

If you are running in the web app or any isolated remote workspace, stop
before running install commands: this conversation cannot modify the desktop
app's local plugin directory or bundled Codex configuration. Tell the user to
open the ChatGPT/Codex desktop app on the machine where they want to work,
start a new conversation there, and paste the install prompt above.

### 1. Use the bundled Codex CLI

Find the bundled Codex CLI path for this desktop-app installation before
running commands. Do not use a standalone Codex CLI from npm, Homebrew,
another package manager, or a random `codex` found in `PATH`.

### 2. Install the plugin

```text
"<BUNDLED_CODEX>" plugin marketplace add https://github.com/creatify-ai/Creatify-agent-plugin.git --ref main
```

If the command fails because no usable `git` executable is available, locate
the Git binary this machine already has and prepend its directory to `PATH`
for the install command, then rerun it. Only as a last resort install Git,
open a new terminal, confirm `git --version`, and rerun the command.

### 3. Authenticate

Do not open a new thread yet — complete login and verification first.

```text
codex mcp login creatify
```

This opens the Creatify browser OAuth page automatically. Follow the sign-in
flow. When the command completes, Creatify stays logged in for future Codex
sessions. No tokens are stored in this repository.

### 4. Verify

- Plugin status shows the `creatify` row as installed and enabled.
- `codex mcp get creatify` shows the MCP server registered.
- `mcp__creatify__*` tools are visible in a NEW conversation (the install
  thread has already captured its tool list — do not keep testing there).
- Browser-control capability is discoverable (`browser:control-in-app-browser`
  / `node_repl js`): Creatify tool results include live page handoffs that
  open in the in-app browser. If browser controls are unavailable on this
  machine, the plugin still works — present the `page_url` links from tool
  results instead.

### 5. Required final step: start the user's first Creatify task

Do not end at "installed successfully". Ask the user what product or brand
they want to promote, then:

- If they have a product URL, import it with `product_create`; otherwise pick
  an existing product via `product_list`.
- Create their first ad with `creative_create_session` — a live Creatify
  session page opens in the in-app browser and updates as the ad generates.

## What is included

- `creatify/` — the Creatify Codex plugin package.
- `creatify/.codex-plugin/plugin.json` — plugin metadata used by Codex.
- `creatify/.mcp.json` — MCP server configuration (hosted endpoint:
  `https://api.creatify.ai/mcp`, OAuth 2.1 + PKCE handled by Codex).
- `creatify/skills/` — workflow skills for common Creatify tasks.

## Requirements

- A Creatify account.
- Codex with plugin support.

## Support

For product information, visit [creatify.ai](https://creatify.ai).
