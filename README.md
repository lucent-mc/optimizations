# Lucent Optimizations

_Your game has never felt this fast before._

Lucent Optimizations is the performance-focused base Layer for Lucent modpacks. It combines a tested
set of optimization mods with defaults that downstream Layers can inherit and override.

## The playable instance is the working copy

This repository lives directly inside the Modrinth App profile used to playtest the Layer. Minecraft,
the launcher, and installed mods can write into this directory normally; `lay` separates that local
runtime state from the portable Layer state committed to Git.

- `inlay.index.json` declares this Layer's exact content and runtime target.
- Downloaded mods, resource packs, shaders, and other third-party payloads remain in the playable
  instance but never enter Git.
- Authored configuration defaults live under `configureddefaults/` and are the only instance content
  stored directly in Git.
- `.layignore` is committed policy for suppressing unwanted implicit Layer candidates.
- `.git/info/exclude` is generated local policy for launcher files, caches, downloads, and other
  materialized runtime state.

## Setup

The repository pins pnpm and installs `lay` as a development dependency. Use Node.js 24 or newer:

```sh
corepack enable
pnpm install
pnpm lay --version
```

The lockfile pins the exact Inlay release used by local commands and CI.

## Authoring

Make changes through the Modrinth App and Minecraft as usual, then reconcile the playable instance:

```sh
pnpm lay status
```

The status tree shows unresolved additions, edits, and removals. Select a file or directory and choose
the action to apply. Reconciled remote content is recorded as immutable Modrinth downloads in the
Layer Manifest; only configuration sources are staged as repository files. Leaving status offers to
commit the staged portable Layer state.

Common commands:

```sh
pnpm lay check                    # validate the Layer and its resolved lineage
pnpm lay list --resolved          # inspect the complete inherited Pack
pnpm lay update --check           # discover viable updates without changing files
pnpm lay update "Sodium"          # adopt one exact owned update
pnpm lay materialize              # restore manifest-managed non-Git content
pnpm lay build                    # build a local deterministic mrpack
```

Provider compatibility metadata is advisory. The playable instance is the final test environment, and
Sinytra Connector with Forgified Fabric API is recognized as satisfying Fabric API dependencies on
NeoForge.

## Releases

For a release-worthy reconciliation, decline the automatic commit when leaving status, record the
staged change intent, and then commit it:

```sh
pnpm lay changes
pnpm lay commit
```

When the accumulated changes are ready to release, consume those fragments to update the Layer
version and changelog:

```sh
pnpm lay version --docs --licenses
pnpm lay commit -m "Prepare the next release"
```

Commit and push the resulting manifest, changelog, and documentation before releasing. The manual
**Release Layer** GitHub Actions workflow then:

1. installs the lockfile-pinned `lay`;
2. validates and materializes the Layer;
3. builds a publishable `.mrpack` with the GitHub publication limit enforced; and
4. creates a draft GitHub release containing the artifact, checksums, and build record.

The workflow refuses to replace an existing release for the same Layer version.

## Updates

The manual **Check Layer updates** workflow runs `lay update --check --json`. It writes every viable
Modrinth candidate to the Actions job summary and uploads the complete machine-readable report without
changing the Layer or opening issues automatically.

## About the modpack

For more information about Lucent Optimizations, see the
[Lucent Optimizations wiki](https://github.com/lucent-mc/adventures/wiki/Lucent-Optimizations).
