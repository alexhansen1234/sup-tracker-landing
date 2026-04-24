# sup-tracker-landing

Landing page for [suptracker.app](https://suptracker.app). Serves the public-facing pages:

- `/` — marketing landing (TestFlight + Firebase App Distribution beta links during beta, App Store + Play Store badges post-launch)
- `/privacy` — privacy policy (required for Play Store + App Store compliance)
- `/.well-known/apple-app-site-association` — iOS Universal Links manifest

Replaces the sunset `sup-tracker-pwa` repo as the owner of `suptracker.app`. See spec issues for migration tracking.

## Stack

- Static HTML + CSS, no build step.
- Hosted on DigitalOcean VPS `165.227.178.235` at `/var/www/demos/suptracker/`.
- Cloudflare in front (DNS + proxy + caching).

## Deploy

Pushes to `main` trigger `.github/workflows/deploy.yml` which rsyncs `public/` to the VPS and purges the Cloudflare cache.

For manual deploys during bootstrap:

```bash
rsync -av --no-delete public/ root@165.227.178.235:/var/www/demos/suptracker/
curl -sf -X POST "https://api.cloudflare.com/client/v4/zones/d588f714a379882e64051d3da92c432f/purge_cache" \
  -H "Authorization: Bearer $CLOUDFLARE_API_TOKEN" \
  -H "Content-Type: application/json" \
  --data '{"purge_everything":true}'
```

## Files to preserve

| File | Owner | Notes |
|------|-------|-------|
| `privacy.html` | this repo | Play Store + App Store require a public URL. |
| `apple-app-site-association` | this repo | iOS Universal Links manifest. |
| `icon.png` | this repo | Favicon / app icon. |
| `index.html` | this repo | Marketing landing. |

## Not in scope

No React, no PWA, no service workers. If a user lands on `suptracker.app` and isn't signed in, they get this static page with store links. Signed-in users are already on the native apps and don't hit this site.
