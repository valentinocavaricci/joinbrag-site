# joinbrag.app — Instructions for AI assistants

You are the web team for BRAG's marketing site. Valentino Cavaricci is a
solo founder learning as he goes — explain decisions in PLAIN language,
step by step, never assume expertise. He gets overwhelmed by multi-step
instructions: give ONE step at a time and wait for "done".

The iOS app lives in a separate repo/folder: `~/Desktop/bragofficial`.
Its `documentation/` folder is the source of truth for brand, voice,
and product decisions (00-VISION, 04-DECISIONS, 06-SHIP-BAR). When in
doubt about what BRAG is or how it speaks, read those first.

## What this site is

- **One static page + privacy policy**, hosted FREE on GitHub Pages.
- Repo: `valentinocavaricci/joinbrag-site` (PUBLIC — required for free
  Pages). Live at **https://joinbrag.app** (HTTPS enforced).
- Purpose per the ship bar: say what BRAG is, look like the app, host
  the privacy policy (App Store requirement), collect beta emails.
- NOT a web app. No frameworks, no build step, no npm. One HTML file
  with inline CSS/JS. Keep it that way.

## Deploy flow (memorize this)

1. Edit files in this folder.
2. Valentino commits + pushes in **GitHub Desktop** (never assume a
   sandboxed `git push` works — there is no network access).
3. GitHub Pages auto-rebuilds in ~2 minutes. Check with an incognito
   window (browsers cache this site hard; Cmd+Shift+R).

⚠️ **Sandbox git quirk**: AI-run `git` commands in this folder leave
stale `.lock` files it cannot delete (mount permissions). If git says
"another process is running", have Valentino run:
`rm -f ~/Desktop/joinbrag-site/.git/index.lock ~/Desktop/joinbrag-site/.git/HEAD.lock`
(zsh: NO wildcards — they abort on no-match). Better: make file edits
only and let him commit via GitHub Desktop.

## Infrastructure

- **Domain**: joinbrag.app, bought at Porkbun (07-18). DNS: four A
  records → GitHub Pages IPs (185.199.108–111.153) + CNAME www →
  valentinocavaricci.github.io. The `CNAME` file in this repo must
  always contain `joinbrag.app` — deleting it breaks the domain.
- **HTTPS**: enforced in repo Settings → Pages (required for .app TLD;
  the site won't load without it).
- **Beta signups → Supabase** (BRAG's own backend):
  - URL: `https://siwmnjiqgzboudnlmrue.supabase.co`
  - Publishable key lives in index.html's JS (safe by design).
  - Table `public.beta_signups` (email unique, first_name, last_name)
    — INSERT-only for the public key; nobody can read the list without
    the dashboard. Created by migration `0026` (+ heir table logic is
    unrelated) in bragofficial/supabase/migrations/.
  - Duplicate email (409) is treated as success in the form JS.
  - Reading the list: Supabase dashboard → Table Editor. TestFlight
    invites are sent manually from App Store Connect (needs first,
    last, email — that's why the form collects names).

## Anatomy of index.html

- **Hero**: full-viewport, two stacked `<video>` layers crossfading
  through `media/clip-1..13.mp4` every 8s (random, never repeating
  back-to-back) — mirrors the APP's login screen. Dark scrim gradient.
- **Glass card** holds headline + beta form: heavy backdrop blur (34px)
  + saturate(1.6), light top-rim inset highlight, max 480px. Tuning
  dials: tint opacity (0.30) and blur px.
- **Headline**: `This is "brag", your real life.` — "your real life."
  wears an animated chrome shine (blue→violet, 6s loop).
- **Top bar**: app icon tile (logo.png, rounded) + lowercase `brag`
  wordmark + "Get the app" pill.
- **Sections**: story line → four pillar cards (Boards/Brags/Arcs/
  Clique, hover lift) → manifesto → footer (Privacy, Support mailto).
  All wear `.reveal` (IntersectionObserver fade-up; respects
  prefers-reduced-motion).
- **Icons**: favicon-32, icon-192, apple-touch-icon, og.png (1200×630
  share card) — all generated from the app icon (chrome "br",
  image22.png on the Desktop is the master).

## Media rules

- GitHub hard-rejects files >100MB; keep the repo lean regardless.
- Desktop stock originals are 25–135MB — NEVER commit them. Compress
  first (ffmpeg is available in the sandbox):
  `ffmpeg -i IN.mp4 -t 12 -vf "scale=w=-2:h=810" -c:v libx264 -crf 30
  -preset ultrafast -an -movflags +faststart OUT.mp4`
- Current deck: 13 clips, ~30MB total. Old `auth-login-*.mp4` files in
  media/ are gitignored leftovers Valentino can trash.

## Settled decisions (don't relitigate)

- **No tab bar / no nav** — one-pager with footer links. (07-18)
- Naming law (from bragofficial 04-DECISIONS): lowercase `brag` =
  wordmark; `BRAG` caps = the name in sentences; never "Brag".
- Voice: cute, fun, fresh, plain — never corporate. "Proof over
  polish." No follower counts, no performing for strangers.
- The site is DONE for 1.0 beyond the queue below — resist polish
  loops; the app is the product.

## Queue (add when their moment arrives)

1. **Screenshots section** — build the same week App Store screenshots
   are made (phone-frame strip; ask Valentino for 3 shots: wall, board
   room, feed).
2. **App Store button** — at launch, swap the beta form CTA for the
   store link (one line).
3. **Invite links** — joinbrag.app/i/... will be the app's invite URL
   surface; coordinate with the bragofficial invite-links slice.
4. **Welcome email** — needs custom SMTP (Resend + domain), optional,
   post-1.0.
