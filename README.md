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

- [`deriva-skills`](https://github.com/informatics-isi-edu/deriva-skills) — releases the `deriva` plugin, tagged `v*.*.*`
- [`deriva-ml-skills`](https://github.com/informatics-isi-edu/deriva-ml-skills) — releases the `deriva-ml` plugin, tagged `v*.*.*`

This marketplace's [`marketplace.json`](.claude-plugin/marketplace.json) **pins** each plugin to a specific version. Claude Code reads that pin when it installs or updates a plugin from the marketplace, so the pin determines what version users on `autoUpdate: true` actually get — not the latest tag in the plugin repo.

The plugin repos themselves no longer carry a per-repo `marketplace.json`; this is the only marketplace for both plugins.

Skill discovery is automatic: Claude Code clones each plugin repo and walks its `skills/*/SKILL.md` files. Neither this `marketplace.json` nor the plugin repo's `plugin.json` enumerates individual skills.

### Bumping the version pin after a plugin release

After someone runs `bump-version` in `deriva-skills` or `deriva-ml-skills`, this marketplace's pin must be updated **by hand** for users to see the new release. (Automation deferred — see "Future improvement" below.)

```bash
# 1. Note the new version from the plugin's GitHub Releases page,
#    or by reading its plugin.json on main.
#    Example: deriva-skills just released v1.2.2.

# 2. In a checkout of THIS repo (informatics-isi-edu/deriva-plugins):
git pull

# 3. Bump the right plugin's version pin:
#    For deriva (tier-1):
jq '(.plugins[] | select(.name == "deriva") | .version) = "1.2.2"' \
  .claude-plugin/marketplace.json > /tmp/m.json && \
  mv /tmp/m.json .claude-plugin/marketplace.json

#    For deriva-ml (tier-2):
# jq '(.plugins[] | select(.name == "deriva-ml") | .version) = "1.3.2"' \
#   .claude-plugin/marketplace.json > /tmp/m.json && \
#   mv /tmp/m.json .claude-plugin/marketplace.json

# 4. Sanity-check the diff (jq rewrites the whole file; the meaningful
#    change should be exactly one line).
git diff .claude-plugin/marketplace.json

# 5. Commit and push:
git add .claude-plugin/marketplace.json
git commit -m "Bump deriva to 1.2.2"
git push
```

**Failure mode if skipped:** users on `autoUpdate: true` stay on whatever version the pin still points to. There is no error or warning — the install just delivers the old version. The plugin repo's GitHub Release will exist but no user reaches it through this marketplace.

**Mirroring docs:** the same procedure (with the corresponding plugin name) appears in each plugin repo's `CLAUDE.md` under "Bumping the meta-marketplace." When updating the workflow here, also update the plugin-side docs.

### Future improvement (deferred)

Each plugin repo could carry a GitHub Actions workflow that fires on `v*.*.*` tag push and opens a PR against this repo with the version-pin update. Auth would be a fine-grained PAT (or GitHub App) with `Contents: write` + `Pull requests: write` on `deriva-plugins`. Closes the only real drift gap. Not implemented yet — the manual procedure above is the current state.

## Related Projects

- [`deriva-mcp-core`](https://github.com/informatics-isi-edu/deriva-mcp-core) — Core MCP framework + generic Deriva catalog tools
- [`deriva-py`](https://github.com/informatics-isi-edu/deriva-py) — Python SDK for Deriva scientific data management
- [`deriva-ml`](https://github.com/informatics-isi-edu/deriva-ml) — Core Python library for ML workflows on Deriva
- [`deriva-ml-mcp`](https://github.com/informatics-isi-edu/deriva-ml-mcp) — DerivaML MCP plugin (loaded by deriva-mcp-core)

## License

Apache 2.0
