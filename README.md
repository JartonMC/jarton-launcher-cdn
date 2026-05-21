# jarton-launcher-cdn

Static config + assets served at `jarton.me/launcher/*` for Jarton Client.

## Layout

```
launcher/
  manifest.json         Canonical config consumed by JartonManifestService
  wallpapers/           Home-tab backdrop pool
  featured/             Phase 3 home-tab featured card images
```

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
