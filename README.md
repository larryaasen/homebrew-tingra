# Tingra Homebrew Tap

A [Homebrew](https://brew.sh) tap for [Tingra](https://github.com/larryaasen/tingra) — native macOS live production and streaming: the Tingra app, its headless CLI and MCP server for AI agents, and Tingra Cameras.

## Tingra

The Tingra app: build shots from cameras, screens, windows, and media, mix their audio, and stream or record the program. Install it as a cask:

```sh
brew install --cask larryaasen/tingra/tingra
```

It installs `Tingra.app` into `/Applications`. Apple Silicon and macOS 15 (Sequoia) or later. macOS asks for camera, microphone, and screen recording access the first time Tingra uses each one.

The app does not include the CLI or the MCP server yet. To use them too, install the `tingra-cli` formula below.

## Tingra Cameras

A small macOS app for picking a camera and microphone, with a live preview and the controls of a USB webcam (exposure, focus, white balance, zoom, and more). Install it as a cask:

```sh
brew install --cask larryaasen/tingra/tingra-cameras
```

It installs `Tingra Cameras.app` into `/Applications`. Apple Silicon and macOS 26 (Tahoe) or later. The first time you open it, macOS asks for camera and microphone access.

## Install the CLI

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

Once that works, go live the same way:

> **Start streaming with Tingra to my Twitch channel.**

Claude will ask for your Twitch RTMP URL and stream key if it doesn't already have them, then call `stream_start`. The key goes straight into Tingra's Keychain-backed secure storage and is never echoed back. Say "stop the stream" when you're done.

See the [main README](https://github.com/larryaasen/tingra#getting-started-the-cli-and-mcp-server) for full setup and more Claude examples.

## What's here

- **`tingra`** — the Tingra app (a cask). Apple Silicon (arm64), macOS 15+. Signed, notarized, and stapled; the tap downloads the prebuilt disk image from the product's GitHub release, the same release that carries `tingra-cli`.
- **`tingra-cameras`** — the Tingra Cameras app (a cask). Apple Silicon (arm64), macOS 26+. Signed, notarized, and stapled; the tap downloads the prebuilt disk image from the app's GitHub release.
- **`tingra-cli`** — the headless engine front end (stream, record, and the MCP server). Apple Silicon (arm64), macOS 15+. Signed and notarized; the tap downloads the prebuilt binary and never builds from source.

## Upgrade / uninstall

```sh
brew update && brew upgrade --cask tingra
brew uninstall --cask tingra
brew uninstall --zap --cask tingra   # also removes Tingra's own settings, logs, and projects

brew upgrade tingra-cli
tingra-cli serve --install     # re-point the LaunchAgent after an upgrade

brew uninstall tingra-cli
tingra-cli serve --uninstall   # run before uninstalling to remove the LaunchAgent

brew upgrade --cask tingra-cameras
brew uninstall --cask tingra-cameras
```

`--zap` leaves the files `tingra-cli` and Tingra Cameras share with the app, and it never touches the Keychain, where Tingra keeps stream keys. To remove those, choose Remove All Data in Tingra's Data settings before uninstalling.

## License

[MIT](https://github.com/larryaasen/tingra/blob/main/LICENSE)
