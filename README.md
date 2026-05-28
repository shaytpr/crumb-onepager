# Crumb - One-pager

Static landing page for **Crumb**, the AI voice receptionist by Apps Bakery.

Single-file HTML + one audio asset. No build step, no framework, no dependencies.

## Structure

```
.
├── index.html              # Self-contained markup + CSS + JS
├── assets/
│   └── CrumbAI_intro.mp3   # Demo call recording (~2 min)
└── README.md
```

## Local dev

The page references `assets/CrumbAI_intro.mp3` via a relative path, so opening `index.html` directly with `file://` works in most browsers. If your browser blocks audio over `file://`, serve it:

```bash
python3 -m http.server 8000
# then http://localhost:8000
```

## Editing

- **Copy / styling** is inline in `index.html`. Search for the section heading text to jump to it.
- **Transcript** lives in two places that must stay in sync:
  1. The `<div class="transcript">` block (visible transcript lines)
  2. The `lineTimings` array in the `<script>` at the bottom (sync points for highlighting)
- **Demo audio**: replace `assets/CrumbAI_intro.mp3`. If the new recording has different pacing, re-derive timestamps with:

  ```bash
  ffmpeg -hide_banner -i assets/CrumbAI_intro.mp3 \
    -af silencedetect=noise=-30dB:d=0.5 -f null - 2>&1 \
    | grep -E "silence_(start|end)"
  ```

  Use silences >1s as turn boundaries, then update the `start` values in `lineTimings`.

## Publishing options

| Option                 | Cost | Custom domain | Setup time | Best for                                                         |
|------------------------|------|---------------|------------|------------------------------------------------------------------|
| **GitHub Pages**       | Free | Yes (CNAME)   | 2 min      | Simplest path. Repo settings → Pages → deploy from `main`/root.  |
| **Cloudflare Pages**   | Free | Yes           | 5 min      | Faster edge, better analytics, instant cache purge.              |
| **Vercel / Netlify**   | Free | Yes           | 3 min      | Preview deploys per PR, useful if you iterate with prospects.    |

### GitHub Pages (recommended start)

1. Push this repo to GitHub.
2. Settings → Pages → Source: `Deploy from a branch` → Branch: `main` / `/ (root)`.
3. Site goes live at `https://<user>.github.io/<repo>/` within ~1 min.
4. For a custom domain (e.g. `crumb.appsbakery.net`):
   - Add a `CNAME` file at the repo root containing the domain.
   - Add a CNAME DNS record on `appsbakery.net` pointing to `<user>.github.io`.

### Cloudflare Pages

1. Cloudflare dashboard → Workers & Pages → Create → connect Git repo.
2. Framework preset: **None**. Build command: empty. Output directory: `/`.
3. Custom domain: Pages project → Custom domains → add.

## Notes

- The audio element uses `preload="auto"`, which means the MP3 starts downloading on page load. On a hosted site this is fine (~820 KB, gzipped barely smaller since MP3 is already compressed). Change to `preload="metadata"` if you want to defer the download until the user clicks play.
- Transcript line timings are derived from silence detection in the current recording. Re-derive them whenever you swap the MP3.
- No analytics or tracking is wired in. Drop a tag into `<head>` if you want Plausible / GA / etc.
