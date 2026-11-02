# Corterra Book plugin

The Corterra Book plugin for Claude and ChatGPT: eight skills that guide the AI through
running a Corterra Book booking business, and the connection to your own Corterra Book
account. The plugin itself is in [`corterra-book/`](corterra-book/); its README says what it
does and what data it sends.

## Install

- **Claude**: find Corterra Book in the plugin directory, or add this repository as a
  plugin marketplace in Claude Code (`/plugin marketplace add <this repository>`), then
  install `corterra-book` and connect it from its Connectors tab.
- **ChatGPT**: find Corterra Book in the plugin directory and press Connect.

Either way you sign in with your Corterra Book dashboard email and password. The plugin
never stores a key: signing in is what connects it, and you can disconnect at any time from
your dashboard.

Licensed under the MIT License (see `LICENSE`).
