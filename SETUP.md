# Launch guide — `kvmy666` GitHub profile

Everything in this folder is ready to publish. Work top to bottom; total time is about 45 minutes, plus optional media passes.

---

## Step 0 — Create the profile repository

The README only renders on your profile when it lives in a **public** repo named exactly `kvmy666`.

```bash
# from this folder
git init
git add README.md assets/hero.svg
git commit -m "Add profile README and hero card"

gh repo create kvmy666 --public --source=. --remote=origin --push
```

Open `https://github.com/kvmy666` and confirm the hero and README render. If they do not, the repo name, visibility, or file path is wrong.

---

## Step 1 — Profile settings

Paste these values into `https://github.com/settings/profile`.

| Field | Value |
| --- | --- |
| **Name** | `Abdulkarim Alshudukhi` |
| **Bio** | `Software Engineering student @ University of Ha'il · Android systems: LSPosed/Zygisk, Kotlin & Compose · 14k+ downloads · Open to co-op Jan 2027` |
| **Company** | `University of Ha'il` |
| **Location** | `Ha'il, Saudi Arabia` |
| **Website** | `https://www.linkedin.com/in/abdulkarim-alshudukhi/` until `abdulkarim.dev` is live, then switch to `https://abdulkarim.dev` |
| **Social links** | `https://t.me/kvmy1`, `mailto:a.alshdukhi@gmail.com` |

- **Bio length:** the string above is 144 characters, under the 160-character cap. If GitHub trims it, cut `· Open to co-op Jan 2027` first (the README already states it).
- **Avatar:** use the portfolio portrait, cropped square, placed on `#050505`. Keep the face inside the circle GitHub applies; do not use a transparent cutout that disappears on dark and light themes.

---

## Step 2 — Pin six repositories

`https://github.com/kvmy666` → **Customize your pins** → check exactly these, in this order:

1. `duoStatusBar`
2. `-AutoExpandNotifications`
3. `iosEmojis-Font-module`
4. `Gboard`
5. `portfolio-template`
6. The best public repo for the Puzzle for Geniuses Play app, if one exists — otherwise pin `Gboard+` or leave the slot empty. Do not pad the list with unrelated repos.

---

## Step 3 — Repository polish

- [x] Repo name intentionally kept as `-AutoExpandNotifications` (owner preference). Do not rename it; update links and badges to use the leading hyphen.
- [ ] Topics for `duoStatusBar`: `android`, `kotlin`, `lsposed`, `xposed`, `statusbar`, `oneplus`, `magisk-module`
- [ ] Topics for `AutoExpandNotifications`: `android`, `kotlin`, `lsposed`, `notifications`, `magisk`, `xposed-module`
- [ ] Topics for `Gboard`: `android`, `kotlin`, `gboard`, `lsposed`, `keyboard`, `module`
- [ ] Descriptions for every repo whose description is null — one sentence, no marketing adjectives:
  - `duoStatusBar` — "Runtime status-bar layout for dual-screen OnePlus devices, built as a reversible LSPosed module."
  - `AutoExpandNotifications` — "Keeps the notification shade expanded and heads-up alerts intact across OEM notification stacks."
  - `iosEmojis-Font-module` — "Systemless iOS emoji font module for Magisk/KernelSU with a clean uninstall path."
  - `portfolio-template` — "The MIT-licensed Next.js template behind abdulkarim.dev — dark theme, GSAP, and Three.js."
- [ ] Set each homepage to the matching project page on `abdulkarim.dev` (temporarily `https://abdulkarim.dev/#projects`).
- [ ] Social preview: 1280×630 PNG per repo, built from the existing logo in `../assets/logos/` on `#050505` with the repo name in Syne. Upload under **Settings → Social preview**.
- [ ] Retire the duplicate `io.github.kvmy666.autoexpand` artifact: archive that repo and point its README at `AutoExpandNotifications`, or delete it if nothing links to it. Two active copies of one module costs you trust.
- [ ] Confirm `README.md` links still resolve after any rename; badges use repo paths and break silently.

---

## Step 4 — Optional project GIFs

GitHub renders GIF, SVG, and PNG reliably in READMEs; WebM and repo-hosted MP4 do not autoplay there, so GIF is the safe format. Convert clips to short, silent, looping GIFs under 3 MB.

Install the tools once: `brew install ffmpeg gifsicle` (macOS) or `sudo apt install ffmpeg gifsicle` (Debian/Ubuntu).

```bash
mkdir -p github-profile/assets/gifs

# one clip -> palette -> GIF -> optimized GIF
convert() {
  ffmpeg -y -ss 0 -t 6 -i "$1" \
    -vf "fps=12,scale=480:-1:flags=lanczos,palettegen=stats_mode=diff" /tmp/pal.png
  ffmpeg -y -ss 0 -t 6 -i "$1" -i /tmp/pal.png \
    -lavfi "fps=12,scale=480:-1:flags=lanczos[x];[x][1:v]paletteuse=dither=bayer:bayer_scale=3" \
    -loop 0 "/tmp/$2.gif"
  gifsicle -O3 --lossy=80 --colors 128 "/tmp/$2.gif" -o "github-profile/assets/gifs/$2.gif"
  du -h "github-profile/assets/gifs/$2.gif"
}

convert "my projects files /duo/VID_20261018_235355.mp4" duoStatusBar
convert "my projects files /ae/VID_20261019_000635_1.mp4" AutoExpandNotifications
convert "my projects files /gboard+/Record_2026-10-19-00-20-20_8218ad734a8157e87572f0f6e2674adf.mp4" Gboard
convert "my projects files /تسجيله بلوت /Record_2026-10-18-23-42-46_f26c550006a3e42dbeee140b52ffd01b.mp4" Baloot
```

For the portfolio WebMs already in `portfolio-instance/public/media/`:

```bash
convert ../portfolio-instance/public/media/gboard/clipboard.webm Gboard-clipboard
convert ../portfolio-instance/public/media/ae/test-harness.webm AutoExpand-snapper
```

Rules: 6 seconds or less, 480 px wide, under 3 MB, no audio, no text overlays. Drop to `--colors 96` or `scale=400:-1` if a file exceeds 3 MB. Reference from a repo README as `![demo](assets/gifs/duoStatusBar.gif)` on its own line, after the one-sentence description.

---

## Step 5 — Optional automation (maximalism only)

Neither workflow is part of the main README. Add them only if you want the extra cards; each adds a scheduled job you have to keep alive.

### 5a — `lowlighter/metrics` (`metrics.svg`, daily)

Create `.github/workflows/metrics.yml` in the `kvmy666` repo:

```yaml
name: Metrics
on:
  schedule:
    - cron: "0 3 * * *"
  workflow_dispatch:
  push:
    branches: [main]
jobs:
  metrics:
    runs-on: ubuntu-latest
    permissions:
      contents: write
    steps:
      - uses: lowlighter/metrics@latest
        with:
          token: ${{ secrets.GITHUB_TOKEN }}
          filename: metrics.svg
          base: header, activity, community
          config_timezone: Asia/Riyadh
          plugin_languages: yes
          plugin_languages_analysis_timeout: 15
          plugin_habits: yes
          plugin_habits_days: 14
          plugin_achievements: yes
          plugin_achievements_threshold: B
          config_theme: dark
          config_display: large
```

### 5b — `Platane/snk` snake

Create `.github/workflows/snake.yml`:

```yaml
name: Snake
on:
  schedule:
    - cron: "0 0 * * *"
  workflow_dispatch:
jobs:
  generate:
    runs-on: ubuntu-latest
    permissions:
      contents: write
    steps:
      - uses: Platane/snk/svg-only@v3
        with:
          github_user_name: kvmy666
          outputs: |
            dist/snake.svg
            dist/snake-dark.svg?palette=github-dark
      - uses: crazy-max/ghaction-github-pages@v4
        with:
          target_branch: output
          build_dir: dist
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

Then embed `https://raw.githubusercontent.com/kvmy666/kvmy666/output/snake-dark.svg` if you want it.

---

## Step 6 — Pre-publish checklist

1. [ ] Repo `kvmy666` is public and the README renders on your profile page.
2. [ ] `assets/hero.svg` renders on GitHub, animates once, and is under 15 KB.
3. [ ] LinkedIn URL in the README resolves to your profile (or the HTML comment has been resolved).
4. [ ] `abdulkarim.dev` either loads or the website field points at LinkedIn.
5. [ ] Bio is under 160 characters and reads correctly in the profile sidebar.
6. [ ] Every badge loads — no broken shields, no 404 repo paths.
7. [ ] Stats and streak cards render with the dark theme and are readable at 48% width.
8. [ ] Six pins are set and ordered; the autoexpand duplicate is archived.
9. [ ] No claim exceeds the evidence: no Play Store install counts, no "published" for Baloot.
10. [ ] Read the README once on mobile — table columns must not overflow the viewport.

---

## Notes

- The numbers `14,000+`, `79★`, `6 products` are snapshots. Refresh them here and in `assets/hero.svg` after a release, or drop the figures from the hero and let the stats cards carry them.
- `github-readme-stats.vercel.app` is a shared public deployment and is rate-limited during peak hours. If the cards go blank, self-host the same project on your own Vercel account and swap the hostname; the query parameters stay identical.
- `streak-stats.demolab.com` is a third-party deployment with the same caveat. Both are decorative: the profile must read fine without them.
