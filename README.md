# Tingra Homebrew Tap

A [Homebrew](https://brew.sh) tap for [Tingra](https://github.com/larryaasen/tingra) — native macOS live streaming, and its MCP server for AI agents.

## Install

```sh
brew install larryaasen/tingra/tingra-cli
```

Then register the MCP server (a launchd LaunchAgent, so camera/mic prompts are attributed to Tingra):

```sh
tingra-cli serve --install
```

### Verify it works

Connect an agent to the server, then ask it to list your devices.

**Claude Desktop** — Claude Desktop doesn't yet have a form for adding a local MCP server by command, so this still means editing a config file, but Claude opens it for you:

1. Open **Claude → Settings → Developer**.
2. Click **Edit Config** — this opens `claude_desktop_config.json` in your default editor (creating it if it doesn't exist yet).
3. Add the `tingra` entry below (merge it into the existing `mcpServers` object if there is one), save, and quit and reopen Claude Desktop:

```json
{
  "mcpServers": {
    "tingra": {
      "command": "/opt/homebrew/bin/tingra-cli",
      "args": ["mcp"]
    }
  }
}
```

**Claude Code** — run:

```sh
claude mcp add tingra -- /opt/homebrew/bin/tingra-cli mcp
```

Then paste this to your agent:

> **Using Tingra, list my cameras and microphones.**

It should call Tingra's `devices_list` tool and return your devices — confirming the MCP server is live. Listing devices needs no camera permission and no streaming key, so it's a safe first check.

See the [main README](https://github.com/larryaasen/tingra#getting-started-the-cli-and-mcp-server) for full setup and more Claude examples.

## What's here

- **`tingra-cli`** — the headless engine front end (stream, record, and the MCP server). Apple Silicon (arm64), macOS 15+. Signed and notarized; the tap downloads the prebuilt binary and never builds from source.

## Upgrade / uninstall

```sh
brew update && brew upgrade tingra-cli
tingra-cli serve --install     # re-point the LaunchAgent after an upgrade

brew uninstall tingra-cli
tingra-cli serve --uninstall   # run before uninstalling to remove the LaunchAgent
```

## License

[MIT](https://github.com/larryaasen/tingra/blob/main/LICENSE)
