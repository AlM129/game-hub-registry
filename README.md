# Game Hub Registry

Metadata registry for the [Game Hub](https://github.com/AlM129/game-hub) Electron launcher.

This repository contains **only metadata** — no game source code. It provides the launcher with information needed to discover, download, and verify games.

## Structure

```
game-hub-registry/
├── registry.json              # Root index — lightweight list of all games
├── schema/
│   └── registry.schema.json   # JSON Schema for validation
├── games/
│   ├── tactical-drone-defense.json
│   ├── sky-ace.json
│   └── neon-survival.json
├── README.md
└── RELEASING.md
```

### registry.json

The root index. Fast to fetch, contains only essential game metadata (ID, name, developer, thumbnail, and a link to the full metadata file).

```json
{
  "registryVersion": 1,
  "lastUpdated": "2026-07-24",
  "games": {
    "tactical-drone-defense": {
      "id": "tactical-drone-defense",
      "name": "Tactical Drone Defense",
      "metaUrl": "games/tactical-drone-defense.json"
    }
  }
}
```

### games/{id}.json

Per-game metadata files. These contain the full game information:

- Game details (name, description, developer, genre, tags)
- Media (thumbnail, screenshots)
- Release channels (stable, beta, development)
- Download package info (URL, format, size, checksum)
- Requirements (OS, RAM, storage)
- Changelog

## Release Channels

Each game supports multiple release channels:

| Channel | Purpose |
|---------|---------|
| `stable` | Production-ready releases. Recommended for all users. |
| `beta` | Pre-release builds for testing. May contain bugs. |
| `development` | Development builds. Unstable, frequent changes. |

## Adding a New Game

1. Create `games/{game-id}.json` following the schema.
2. Add an entry to `registry.json` under the `games` object.
3. Validate against the schema.

## Publishing a Release

See [RELEASING.md](RELEASING.md) for the release workflow.

## Schema Validation

The registry and all game files reference `schema/registry.schema.json`. Validate with:

```bash
npx ajv-cli validate -s schema/registry.schema.json -d registry.json
npx ajv-cli validate -s schema/registry.schema.json -d games/*.json
```

## How Game Hub Uses This Registry

1. **Fetch** `registry.json` to get the list of available games.
2. **Load** per-game metadata from `games/{id}.json` on demand.
3. **Compare** installed versions against registry versions.
4. **Download** packages from the channel's download URL.
5. **Verify** checksums for integrity.
6. **Extract** and install.
7. **Launch** from the installed directory.