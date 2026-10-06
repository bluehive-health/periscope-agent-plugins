# Periscope agent plugins

The `periscope` plugin marketplace. Its source lives in
[bluehive-health/periscope](https://github.com/bluehive-health/periscope) under `agent/plugins/`;
change it there and copy the directory here.

## periscope-ticket-gate

VS Code's Copilot agent reads hooks from plugins, not from its managed settings. This plugin
registers `UserPromptSubmit` and `PreToolUse` hooks that call the root-owned ticket supervisor
installed by `Periscope --install-managed-hooks` (`copilot-managed` registration). It contains
no code of its own: a machine without the managed Periscope install has nothing for it to run.

The tray's managed installer force-enables it in
`/Library/Application Support/GitHubCopilot/managed-settings.json`
(`/etc/github-copilot/managed-settings.json` on Linux):

```json
{
  "extraKnownMarketplaces": {
    "periscope": {
      "source": {
        "source": "github",
        "repo": "bluehive-health/periscope-agent-plugins"
      },
      "autoUpdate": true
    }
  },
  "enabledPlugins": { "periscope-ticket-gate@periscope": true }
}
```

The same managed file applies to Copilot CLI. The supervisor gates VS Code's payloads (snake_case
`session_id` and `hook_event_name`), passes Copilot CLI's camelCase payloads through ungated, and
blocks anything else. VS Code's `ChatHooks` policy must also be on (see the Periscope tray README).

The hook commands must match the supervisor registration in the Periscope tray
(`agent/tray/src-tauri/src/managed_hooks.rs`); a tray test checks the source copy.
