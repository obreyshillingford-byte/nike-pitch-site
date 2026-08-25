# Code and Theory for Nike — FY27 pitch site

Static site. No build step, no dependencies, no package manager. The repo root
IS the site.

```
index.html     password gate (in-page password: partnership)
site.html      the pitch site
support.js     rendering runtime, loaded by both pages
assets/        imagery, logos, six Futura weights
vercel.json    build + routing config
package.json   present only to satisfy Vercel's import check
```

There is nothing to install. `package.json` has no dependencies — Vercel's
import flow expects the file to exist, and its `build` script is a no-op echo.

## Deploy

Push to GitHub, then import the repo at <https://vercel.com/new>.

`vercel.json` declares the build and output directory, so the import needs no
manual configuration:

- Framework Preset: **Other**
- Build Command / Output Directory: leave them alone — `vercel.json` wins
- Root Directory: **/** (leave as-is)

Then Deploy. Every push to the default branch redeploys automatically; PRs get
preview URLs.

The build runs a no-op echo and serves the repo root. If Vercel reports
"No Output Directory named 'public' found", `vercel.json` is missing from the
repo root — it must sit next to `index.html`.

## Password protection

The in-page password box is cosmetic — it's readable in the page source, and
`site.html` opens directly without it.

For a real gate: **Project → Settings → Deployment Protection → Password
Protection**. Vercel then gates the site at the edge, before any HTML is
served. (Pro feature; on Hobby, Vercel Authentication restricts access to your
team's Vercel accounts instead.)

## One runtime dependency

`support.js` fetches React from `unpkg.com` on page load. Vercel hosting
doesn't change that — it runs in the viewer's browser. If a viewer's network
blocks public CDNs the page renders **blank**, not degraded.

Everything else is local: all six Futura weights, every image, all 28 file
references verified exact-case (Vercel's filesystem is case-sensitive).

## Before sharing the link

- **Deck button** → Dropbox PDF. Set the link to "anyone with the link can
  view" or viewers hit a request-access wall.
- **Machine reel** → Vimeo embed. If the video is private, add the deployed
  domain to its allowed embed domains.
- **`login-bg.png` and `quote-breaker.png`** are ~17MB combined. Vercel's CDN
  handles it; it's still slow on a phone.
- **Fonts** are served to every visitor — confirm the license covers web use.
