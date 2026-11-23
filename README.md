# Orbit Search

Standalone Scramjet browser proxy for Orbit, with an authenticated Wisp relay.
All production code is in this repository. It does not need the main site's
`orbitcraft/` or `chat/` folders.

## Render deployment

Connect **yualan480/Orbit-Search**, branch `main`, as a Node Web Service.

- Root Directory: leave blank
- Build: `npm ci --omit=dev`
- Start: `npm start`
- Node: 24 or newer (`NODE_VERSION=24`)
- Health check: `/health`
- Environment: `ORBIT_SEARCH_PARENT_ORIGIN=https://silkroadhistoryprojects.onrender.com`
  (use your actual main website's exact origin, with no path or trailing slash).

The server binds to `0.0.0.0` and Render's `PORT` automatically.
Set `ORBIT_SEARCH_URL` on your **main website service** to this Search service's
actual HTTPS address, then redeploy the main website. No Supabase migration is
needed. Open Orbit Search from the Orbit sidebar to connect your signed-in
account; visiting this service directly does not bypass login.

`auth.js` verifies the Orbit access token and profile directly with Supabase.
`auth-config.js` contains only the same public Supabase URL and anonymous key
used by the main Orbit site. It must point to that same project. No service-role
key is required, and the server never grants access merely from client claims.

## Local checks

`npm ci`, then `npm test`. Production-only installation also supports `npm test`.

`npm start` launches on port 8790 unless `PORT` is set. The main website and
Search need separate origins. Some browser checks exercise integration with the
main site: set `ORBIT_MAIN_REPO` to an absolute path to a full
Silk-Road-History-Project checkout when running those checks. They skip when that
checkout is not supplied. Browser checks also need the dev Playwright dependency
and Chromium at `/usr/bin/chromium`.

## Security and compatibility

The separate origin keeps proxied websites away from Orbit's own browser storage.
Account handoff checks both the parent origin and source window. Relay tickets
expire, streams are limited, and private/loopback/direct-IP destinations, UDP,
and ports other than 80/443 are blocked. No server-side browsing-history store
is implemented. Do not add analytics that record ticket URLs.

Third-party sites may refuse proxy access, require CAPTCHA or fail to load.
Hosting providers can impose proxy policies and bandwidth limits. Historical
`PROXY-AUDIT.md` and `REVIEWED-FILES.md` describe earlier monorepo snapshots;
use the actual current files and this README for standalone deployment.
