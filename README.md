# jarton-launcher-cdn

Static config + assets served at `jarton.me/launcher/*` for Jarton Client.

## Layout

```
launcher/
  manifest.json         Canonical config consumed by JartonManifestService
  instance/             Per-version instance pack zips (Prism-export layout)
  wallpapers/           Home-tab backdrop pool
  featured/             Phase 3 home-tab featured card images
```

## Per-version instance packs

`instance.pack_url` / `instance.pack_version` is the legacy single-instance (1.21.4) path,
kept for back-compat with the canonical first-launch provisioning and the update prompt.

The `packs` object maps each supported Minecraft version to its curated pack:

```json
"packs": {
  "<mc-version>": {
    "fabric_version": "0.19.3",
    "pack_version": "1.0.0",
    "pack_url": "https://.../jarton-instance-<mc>-v<pack>.zip"
  }
}
```

The launcher reads `packs[chosenVersion]` when the user creates a Jarton instance and
imports `pack_url`. Each pack zip is a Prism instance export: top-level `Jarton/`, game
files under `Jarton/minecraft/` (mods + config; JartonUI is excluded because the launcher
force-injects the per-version jar on every launch). A version with no `packs` entry falls
back to "no curated mods" (plain Fabric instance).

Pack zips live under `launcher/instance/` and are published as GitHub release assets on
the `pack-<mc>-v<pack>` tag. Staging the zip in this repo does NOT make it live — the
`pack_url` must point at a published release asset for the launcher to fetch it.

## Admin workflow

1. Edit `launcher/manifest.json` (or add files under `launcher/wallpapers/`).
2. `git commit`, `git push`.
3. Cloudflare Pages picks it up on push; live in ~30 seconds.

The launcher polls the manifest every 15 minutes, so end-user impact follows the same window.

## Hosting

Cloudflare Pages project, custom domain `jarton.me` with `/launcher/*` routed here.

## Cache rules

See `_headers`:

- Manifest: 5-minute edge cache (launcher polls every 15m anyway; this just keeps Pages cheap).
- Wallpapers + featured: 1-day edge cache. Asset URLs are versioned via the `id` field in the manifest entry, so a content change means a URL change.
