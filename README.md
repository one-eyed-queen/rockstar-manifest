# rockstar-manifest

Steam depot **manifest** files for the Rockstar Games library, the raw
`<depot>_<gid>.manifest` payloads Steam serves from `manifest.steamcontent.com`.
`femdl` fetches them from here instead of Steam when a user asks for a build
that is no longer on Steam's public branch.

Pair with [`rockstar-lua`](https://github.com/one-eyed-queen/rockstar-lua), which
has the matching depot keys and build index.

## Layout

```
<steam-appid>/
  <build-id>/
    <depot-id>_<manifest-gid>.manifest
    <depot-id>_<manifest-gid>.manifest
    ...
  <another-build>/
    ...
```

- **Per-app folder**, then **per-build folder**. The build id matches the
  `-- Build <id>` comment in the companion `.lua`.
- Each file is a Steam depot manifest exactly as Steam serves it (zstd-wrapped
  protobuf; `femdl`'s `manifest.py` parses it).

## How the launcher uses it

- When the user picks a build in the install popup, the launcher takes the
  depot ids and manifest gids off the `.lua` and fetches
  `<appid>/<build>/<depot>_<gid>.manifest` from this repo.
- If a build directory is missing, the launcher falls back to the manifest
  CDN that the signed-in Steam account (anonymous or not) would use — which
  only works for the current public build.

## Adding a build

1. Install the game once with `femdl` or Steam.
2. The manifest files land next to the game data; copy the ones you want to
   keep under `<appid>/<new-build>/` here and commit.
3. Add a `-- Build <id> — <YYYY-MM-DD> — v<version>` comment above the
   matching `setManifestid` in `rockstar-lua`.
