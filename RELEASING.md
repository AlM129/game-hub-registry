# Releasing Games via the Registry

This document describes the workflow for publishing game releases through the Game Hub Registry.

## Overview

When a game developer creates a new release of their game, the registry must be updated to reflect the new version. This repository contains only **metadata** — the actual game packages are hosted elsewhere (e.g., GitHub Releases, a CDN, or a private server).

## Steps to Publish a Release

### 1. Tag and Package the Game

In the game's source repository (e.g., `games/tactical-drone-defense/`):

1. Create a distributable package (ZIP archive) of the game.
2. Upload the package to a hosting location (GitHub Releases, S3, etc.).
3. Note the download URL, file size, and compute the SHA-256 checksum:
   ```bash
   shasum -a 256 tactical-drone-defense-v1.4.0.zip
   ```

### 2. Update the Registry

In this repository, edit the game's metadata file under `games/{id}.json`:

#### For a new stable release:

```json
{
  "channels": {
    "stable": {
      "version": "1.5.0",
      "releaseDate": "2026-08-01",
      "download": {
        "url": "https://github.com/MCMGames/tactical-drone-defense/releases/download/v1.5.0/tactical-drone-defense-v1.5.0.zip",
        "format": "zip",
        "size": 52428800,
        "checksum": {
          "algorithm": "sha256",
          "hash": "e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855"
        }
      },
      "releaseNotes": {
        "version": "1.5.0",
        "date": "2026-08-01",
        "changes": [
          "Added new enemy types",
          "Performance improvements"
        ]
      }
    }
  }
}
```

#### For a beta pre-release:

```json
{
  "channels": {
    "stable": {
      "version": "1.4.0"
    },
    "beta": {
      "version": "2.0.0-beta.1",
      "releaseDate": "2026-08-15",
      "download": {
        "url": "https://github.com/.../tactical-drone-defense-v2.0.0-beta.1.zip",
        "format": "zip",
        "size": 55000000,
        "checksum": {
          "algorithm": "sha256",
          "hash": "abc123..."
        }
      }
    }
  }
}
```

### 3. Update registry.json

Only needed if adding a new game. If updating an existing game's version, only the per-game file needs to change.

### 4. Validate

Run schema validation:

```bash
npx ajv-cli validate -s schema/registry.schema.json -d registry.json
npx ajv-cli validate -s schema/registry.schema.json -d games/*.json
```

### 5. Commit and Push

```bash
git add games/tactical-drone-defense.json
git commit -m "chore: update tactical-drone-defense to v1.5.0"
git push
```

## Channel Guidelines

| Channel | When to use |
|---------|-------------|
| `stable` | Production-ready. All users see this by default. |
| `beta` | Feature-complete but needs testing. Users opt in. |
| `development` | Work in progress. Not packaged for distribution. Link to source repo. |

- **Never** promote a development build to `stable`.
- Development versions should use suffixes like `-dev`, `-alpha`, `-beta.1`.
- The launcher will compare versions and prompt users to update as needed.

## When to Update registry.json

Only modify `registry.json` when:
- Adding a brand new game to the registry.
- Changing a game's display name, developer, or featured status.
- Removing a game from the registry.

Do **not** modify `registry.json` for version bumps — that's what the per-game files are for.