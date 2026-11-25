# agent-batteries

A Claude Code plugin marketplace. `.claude-plugin/marketplace.json` lists every plugin.

## Two kinds of plugin

- **Skill plugin**: a marketplace entry with `"source": "./"` and `"skills": ["./skills/<name>"]`. `skills/<name>/` holds skill files only, because `npx skills` copies the folder whole into consumers' projects. All skill plugins share the root `.claude-plugin/plugin.json` as their manifest. One skill per plugin, so each installs on its own.
- **Composite plugin**: `plugins/<name>/` with the standard plugin layout. Use it for anything with hooks, agents, MCP servers or a mod, and for bundles.

## Conventions

- The shared root manifest's `name` is `zonterone-skills`. The manifest name is the namespace, so skills load as `zonterone-skills:<skill>`. The marketplace entry name is the install key and equals the folder name; `displayName` repeats it for the `/plugin` UI.
- The marketplace `name` stays `zonterone-cc-batteries`, even though the repo is `zonterone/agent-batteries`. Every install is keyed `<plugin>@zonterone-cc-batteries` in users' settings, so a new name orphans existing installs.
- A composite plugin takes its own unique manifest `name`. Claude Code registers hooks for one plugin per manifest name.
- Leave `version` out of every manifest and entry. The commit SHA is the version, and each push to `main` reaches users as an update; a written `version` pins users until it is bumped.
- Display fields shared by all skill plugins (`author`, `repository`, `license`) live in the root `plugin.json`. A skill plugin's entry carries its own `displayName` and `description`, and `homepage` when the skill is adapted from an upstream; entry display fields override the manifest's. A composite plugin keeps all its display fields in its own `plugin.json`, and its entry holds `name` and `source` only.
- A bundle is a manifest with `dependencies` and no components. `dependencies` lists marketplace entry names.

## Adding a skill plugin

1. Create `skills/<name>/SKILL.md`.
2. Add an entry to `.claude-plugin/marketplace.json`, copying the shape of a sibling skill entry: `name`, `"source": "./"`, `"skills": ["./skills/<name>"]`, `displayName`, `description`.
3. Add a row to the Plugins table in `README.md`.
4. Run `claude plugin validate .` and fix every error. Expected warnings: one `No version specified` per plugin and one for the root `plugin.json`, plus `CLAUDE.md at the plugin root is not loaded` and a missing icon, because validate also checks the repo root as a plugin.
