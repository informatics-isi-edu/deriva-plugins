# Deriva Plugins Marketplace

[Claude Code](https://claude.ai/claude-code) plugin marketplace for the [Deriva](https://github.com/informatics-isi-edu/deriva-py) ecosystem. Adds two plugins from a single marketplace add:

| Plugin | Tier | Purpose |
|--------|------|---------|
| [`deriva`](https://github.com/informatics-isi-edu/deriva-skills) | 1 — core | Schema operations, vocabulary management, query patterns, Chaise display customization, generic catalog troubleshooting. Works on any Deriva catalog. |
| [`deriva-ml`](https://github.com/informatics-isi-edu/deriva-ml-skills) | 2 — DerivaML | Dataset / execution / experiment lifecycles, features, asset management, Hydra-zen configs, model development. Depends on the `deriva` plugin and the `deriva-ml-mcp` MCP plugin. |

## Installation

Add the marketplace once, then install whichever plugins you need:

```bash
# Add the marketplace (one-time)
/plugin marketplace add informatics-isi-edu/deriva-plugins

# Install the tier-1 plugin (works on any Deriva catalog)
/plugin install deriva

# Optionally install the tier-2 DerivaML plugin
/plugin install deriva-ml
```

If you do DerivaML work, install both. If you only work with raw Deriva catalogs, the `deriva` plugin alone is sufficient.

## Updating

Enable `"autoUpdate": true` in `~/.claude/settings.json` for the `deriva-plugins` marketplace and restart Claude Code; new versions are picked up automatically.

## How this marketplace stays in sync with the plugins

Each plugin is independently versioned and released from its own repo:

- [`deriva-skills`](https://github.com/informatics-isi-edu/deriva-skills) — releases `deriva` plugin, tagged `v*.*.*`
- [`deriva-ml-skills`](https://github.com/informatics-isi-edu/deriva-ml-skills) — releases `deriva-ml` plugin, tagged `v*.*.*`

After a plugin release, the corresponding `version` field in [`.claude-plugin/marketplace.json`](.claude-plugin/marketplace.json) is bumped to match. The plugin repos still ship their own per-repo `marketplace.json` for users who want a single-plugin install path; this meta-marketplace is the recommended entry point for users who want both.

## Related Projects

- [`deriva-mcp-core`](https://github.com/informatics-isi-edu/deriva-mcp-core) — Core MCP framework + generic Deriva catalog tools
- [`deriva-py`](https://github.com/informatics-isi-edu/deriva-py) — Python SDK for Deriva scientific data management
- [`deriva-ml`](https://github.com/informatics-isi-edu/deriva-ml) — Core Python library for ML workflows on Deriva
- [`deriva-ml-mcp`](https://github.com/informatics-isi-edu/deriva-ml-mcp) — DerivaML MCP plugin (loaded by deriva-mcp-core)

## License

Apache 2.0
