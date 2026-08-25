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

---

## First deploy

Push to GitHub, then import the repo at <https://vercel.com/new>.

`vercel.json` declares the build and output directory, so the import needs no
manual configuration:

- Framework Preset: **Other**
- Build Command / Output Directory: leave them alone — `vercel.json` wins
- Root Directory: **/** (leave as-is)

Then Deploy.

If Vercel reports "No Output Directory named 'public' found", `vercel.json` is
missing from the repo root — it must sit next to `index.html`.

---

## Updating the site

Vercel is watching the repo. **Any push to the default branch redeploys
automatically** — there is no button to press and no import to redo.

### If you have a new zip from me

The zip is always the complete site, not a patch. Replace, don't merge.

**GitHub website, no terminal:**

1. Unzip it.
2. In the repo, open the file you want to replace (e.g. `site.html`) → the
   pencil icon → delete all the contents → paste the new file's contents →
   **Commit changes**.

   For a single-file change that is the fastest route. If several files changed,
   use **Add file → Upload files** and drag the new ones in — same filenames
   overwrite cleanly.

3. Vercel picks up the commit and redeploys in ~1 minute. Watch it in the
   Vercel dashboard under Deployments.

**Command line:**

```bash
# from inside your local clone
git pull                       # get anything others changed first
# copy the new files over the old ones, then:
git add .
git commit -m "Update case studies"
git push
```

### Which files actually change

Almost always just these two:

- `site.html` — all pitch site content: copy, sections, video embeds
- `index.html` — the login page and its password

`assets/` only changes when imagery changes. `support.js`, `vercel.json`, and
`package.json` are stable — you can leave them alone forever.

So for a copy change, replacing `site.html` alone is enough.

### Changing the password

It's in `index.html`. Search for `partnership` and replace it. It appears once,
in the logic block near the bottom.

Better: use Vercel's real password instead (see below) and leave this one as a
branded front door.

### Rolling back a bad change

Vercel dashboard → **Deployments** → find the last good one → ⋯ → **Promote to
Production**. Instant, and it does not touch the repo.

---

## Password protection

The in-page password box is cosmetic — it's readable in the page source, and
`site.html` opens directly without it.

For a real gate: **Project → Settings → Deployment Protection → Password
Protection**. Vercel then gates the site at the edge, before any HTML is
served. (Pro feature; on Hobby, Vercel Authentication restricts access to your
team's Vercel accounts instead.)

---

## One runtime dependency

`support.js` fetches React from `unpkg.com` on page load. Vercel hosting
doesn't change that — it runs in the viewer's browser. If a viewer's network
blocks public CDNs the page renders **blank**, not degraded.

Everything else is local: all six Futura weights, every image, every file
reference verified exact-case (Vercel's filesystem is case-sensitive, most
laptops aren't — this is a common way a site works locally and 404s live).

---

## Before sharing the link

- **Six Vimeo embeds** — the Machine reel plus five case studies (Con Edison,
  NFL, TIME, Microsoft, Amazon Ads). All but NFL are unlisted and carry privacy
  hashes in their URLs. Unlisted videos often also restrict embed domains: add
  the deployed domain to each video's allowed domains in Vimeo, or they show an
  error instead of playing.
- **Two deck buttons** → both Dropbox PDFs (Proposal, Follow-Up Deck). Set each
  link to "anyone with the link can view" or viewers hit a request-access wall.
- **`login-bg.png` and `quote-breaker.png`** are ~17MB combined. Vercel's CDN
  handles it; it's still slow on a phone.
- **Fonts** are served to every visitor — confirm the license covers web use.
- **Test on the network you'll present from**, not just your laptop.
