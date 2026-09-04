# Jamie Lakritz — Playwright Site
## Hugo + GitHub Pages + Pages CMS — Setup Guide

This is a fully static Hugo site with no theme dependency — every
layout is custom-built to match the agreed design. Content is managed
through Pages CMS, the same tool used for the Brightlight Theatre site.

---

## What's in this folder

```
config.yaml               → Site settings
content/
  _index.md                → Homepage hero + press quote content
  plays/                   → One file per play (the core content type)
  notebook/                → Short reflective entries
layouts/
  _default/baseof.html     → Shared page skeleton (head, header, footer)
  index.html               → Homepage template
  plays/single.html        → Individual play page template
  partials/                → Header, footer, Now Playing banner
static/
  css/style.css            → All site styling
  uploads/                 → Images go here (via Pages CMS or manually)
.pages.yml                 → Pages CMS content schema
.github/workflows/hugo.yml → Auto-builds and deploys on every push
```

---

## Part 1 — Get the site live on GitHub Pages

### Step 1: Create the repository

1. Go to [github.com/new](https://github.com/new)
2. Name it `jamielakritz-site` (or whatever you prefer — the domain
   mapping happens later, so the repo name doesn't need to match)
3. Set it to **Public** (required for free GitHub Pages)
4. Don't initialise with a README — you're uploading this folder as-is

### Step 2: Upload this folder to the repository

If you're comfortable with git:
```bash
cd jamielakritz-site
git init
git add .
git commit -m "Initial site"
git branch -M main
git remote add origin https://github.com/YOUR-USERNAME/jamielakritz-site.git
git push -u origin main
```

If you'd rather not use the command line, GitHub's web interface lets
you drag and drop files directly — but for a folder this size, using
git (or GitHub Desktop, a free app with a normal drag-and-drop feel)
will be much less painful.

### Step 3: Enable GitHub Pages with Actions

1. In your repository, go to **Settings → Pages**
2. Under **Build and deployment → Source**, choose **GitHub Actions**
   (not "Deploy from a branch" — the included workflow handles the
   Hugo build for you)
3. That's it — the workflow in `.github/workflows/hugo.yml` will now
   run automatically every time you push to `main`, building the site
   with Hugo and publishing the result

### Step 4: Check the build

1. Go to the **Actions** tab in your repository
2. You should see a workflow run in progress (or already green ✓)
3. Once it's green, go back to **Settings → Pages** — you'll see a
   live URL like `https://YOUR-USERNAME.github.io/jamielakritz-site/`

### Step 5: Point your real domain at it (jamielakritz.co.uk)

1. In **Settings → Pages**, under **Custom domain**, enter
   `jamielakritz.co.uk` and save
2. GitHub will show you DNS records to add. With most UK registrars
   (123-reg, GoDaddy, Namecheap, etc.) you'll add:
   - An `A` record pointing `@` to GitHub's IP addresses (GitHub shows
     you these — currently `185.199.108.153`, `.109.153`, `.110.153`,
     `.111.153`, but always use whatever the Pages settings page shows
     you, as these can change)
   - A `CNAME` record pointing `www` to `YOUR-USERNAME.github.io`
3. DNS changes can take anywhere from a few minutes to 24 hours to
   take effect
4. Once it's working, tick **Enforce HTTPS** back in the Pages settings

---

## Part 2 — Connect Pages CMS for content editing

[Pages CMS](https://pagescms.org) is a free, git-based CMS — content
edits are saved as real commits to your repository, so nothing is ever
"in a database somewhere else." This is the same tool used for the
Brightlight Theatre site.

### Step 1: Connect the repository

1. Go to [app.pagescms.org](https://app.pagescms.org)
2. Sign in with GitHub
3. Authorise Pages CMS to access the repository (you can restrict this
   to just this one repo during the GitHub authorisation step)
4. Select `jamielakritz-site` from the list

Pages CMS automatically detects the `.pages.yml` file in the repo root
and builds the editing interface from it — you don't need to configure
anything further.

### Step 2: Editing plays

In the Pages CMS dashboard you'll see three sections, matching
`.pages.yml`:

- **Homepage** — the hero text, hero photo, and press quote
- **Plays** — add, edit, or reorder plays
- **Notebook** — short reflective entries

To add a new play:
1. Go to **Plays → Add new**
2. Fill in the fields — title, year, genre, logline are the minimum
3. Toggle **Feature on Homepage** if you want it in the "Selected
   Plays" list (shows up to 4, newest first, or manually ordered via
   **Sort Order**)
4. Toggle **Currently Playing** only while a production is genuinely
   on — this powers the "Now Playing" banner across the whole site,
   and the banner disappears automatically when nothing is marked on
5. Set **Licensing Status** — this controls both the callout box on
   the play page and which button appears in the sidebar (Request
   Script PDF / Register Interest / View on Lazy Bee or Stagescripts)
6. Save — Pages CMS commits this directly to your repository, which
   automatically triggers a new GitHub Actions build (usually live
   within 1–2 minutes)

### Step 3: Uploading images

Images uploaded through Pages CMS go into `static/uploads/` in the
repository automatically — you don't need to do anything extra. Just
use the image field in any form (Hero Photo, Thumbnail, Gallery
photos) and Pages CMS handles the upload.

**Recommended image sizes:**
- Hero photo: at least 1000px wide, roughly 4:3 ratio
- Play thumbnails: at least 400px wide, roughly 4:3 ratio
- Gallery photos: at least 800px wide, roughly 16:10 ratio

---

## Part 3 — Ongoing content workflow

Once set up, day-to-day content changes need no technical steps at
all:

1. Log into Pages CMS
2. Edit or add content
3. Save
4. Wait ~1–2 minutes for the GitHub Actions build to complete
5. Refresh the live site

To check a build succeeded, the **Actions** tab in GitHub shows every
build with a green tick (success) or red cross (failed — click into it
to see the error, usually a YAML formatting issue in a content file).

---

## Notes on specific features

**Now Playing banner**: entirely automatic. It's driven by the
`now_playing` toggle on each play. If no play has it switched on, the
banner section doesn't render at all — no empty box, nothing to hide
manually.

**Featured plays on homepage**: controlled by the `featured` toggle
and `weight` field on each play. If nothing is marked featured, the
homepage falls back to showing all plays (newest first) so the site
never shows an empty "Selected Plays" section.

**Licensing button logic**: the sidebar button on each play page
changes automatically based on `licensing_status`:
- `direct` → "Request Script PDF" (opens an email)
- `development` → "Register Interest" (opens an email)
- `licensed` → links out to Lazy Bee Scripts or Stagescripts, based on
  the `licensor` field

**Notebook**: currently shows as short inline entries on the homepage
only (title + summary, no full article pages). If you want full
notebook article pages later, that's a small template addition — flag
it and it can be added without disrupting anything else.
