# Roadmap

Darnling is a web app that shows a sticker on every background, fills its see-through gaps, and exports it for every place you post. This file tracks the decisions already made, what gets built in what order, and what's out of scope.

## Architecture characteristics

What the design must protect. Everything else gets ordinary good design.

1. **Private.** No file ever leaves the browser: no uploads, no analytics, and no network calls beyond loading the page.
2. **Faithful.** No pixel changes until you approve it, every pixel outside an approved hole stays byte-identical, and the original file is never touched.
3. **Fast.** Previews update at once, even on large PNGs.

## Decisions (2026-10-24)

- **Build Darnling.** It's the owner's own job: Calla's stay-put sticker is kept out of Staypaw's README, and Sweepling waits to use Primrose's still-sipping sticker, both because of see-through gaps. Web and GitHub searches found tools that resize or preview stickers, and none that finds and fills gaps inside one. This gives up time on the other app ideas.
- **The name is Darnling:** darning mends holes, and it sounds like "darling". On 2026-10-24 it had no GitHub repos and no app was found; `darnling.app` had no DNS, and `darnling.com` is registered. Trademarks aren't checked. This gives up Mendpup, the runner-up.
- **Private, faithful and fast** are the characteristics above. This gives up offline use (a service worker) and proven accuracy as top-three goals; both get ordinary good design.
- **A hole is a see-through area that the outside can't reach.** A pixel counts as see-through under 50% opacity (alpha below 128). Darnling fills the see-through area inward from the image's border, and whatever see-through pixels the fill can't reach are holes. A prototype on 2026-10-24 found exactly the real problems: 2 holes in `stay-put.png` (352 and 129 px) and 1 in `still-sipping.png` (442 px), all inside the white border. This gives up two things:
  - **Slivers that reach the outside through a narrow neck** get a "check this" warning, never a fill. Closing gaps of 2 px added about 40 px per sticker, but at 3 px the prototype flagged 237 px between Primrose's hip and her mug, which is meant to be open.
  - **Faint pixels are ignored:** about 1,400 per sticker are a little see-through inside the art, at a median opacity of 253 of 255, which nobody can see.
- **Darnling mends holes in a copy, with the sticker's border colour.** A real die-cut sticker shows its white vinyl through such a gap, so the border colour (white on both mascots) is what the eye expects. You approve each fill against the before view, and the original file is never written. This gives up a seamless fill from the colours around the hole, which can smear an outline or skin tone into a spot that should read as backing, and gives up a smaller flag-only first version.
- **Vite and plain TypeScript, with no UI framework.** Canvas 2D draws the previews. The hole finder is a pure function over RGBA pixels, tested with Vitest, and runs in a Web Worker so a large PNG never freezes the page. It's one screen: drop a file, a preview grid, a hole list and export. Fewer dependencies also keep the privacy promise easy to check. This gives up Angular, which the owner already uses in AniMatch and the portfolio.
- **Hosted on Vercel,** the owner's call; Claude recommended GitHub Pages. It's a static build only: no Vercel Functions, no Web Analytics, no Speed Insights. This gives up hosting beside the repo with no second account, and takes on keeping Vercel's opt-in extras off.
- **Free and open source, in a public repo under Apache-2.0,** like Staypaw and Sweepling. A privacy promise is easiest to trust when anyone can read the code, and the Lev-style plan is free apps that build an audience, with character art as the paid layer. This gives up a paid tier.
- **Still PNGs only in version one.** Both mascots' stickers are still PNGs. Animated stickers (APNG, GIF, WebM) need frame-by-frame decoding, a check per frame and re-encoding, which would roughly double the work before the core job is proven. This gives up animated Telegram and LINE packs until Later.
- **A web app only, not a step in anime-pipeline,** the owner's call. The core can be shaped for the browser alone. This gives up catching a hole the moment anime-pipeline makes a sticker.
- **Export targets come from the owner's own list:** GitHub READMEs, Discord, Telegram, LINE, socials and the blog. *Claude's call.* Third-party sites disagree about the sizes (Discord emoji at 128 or 256 px, for one), so every preset's numbers are checked against the platform's own docs and dated here before it's built (M4).
- **Light halos at the edge, stray pixels, and adding a die-cut border are Later.** *Claude's call*, to keep 0.1 on holes, the job no other tool does. Photoshop's matting tools already fix halos. This gives up a fuller cleanup in one place.
- **The README shows Calla's and Primrose's real stickers,** with their holes marked, on GitHub's dark background. Both are Apache-2.0 in their own repos. The "after" column stays words until Darnling makes the mended image itself (M3). This gives up a filled "after" picture for now.
- **The brand is a placeholder:** a heart with a woven darn patch, in `docs/brand/`, with Fredoka SemiBold lockups built by `scripts/make_lockup.py`, as in Staypaw. A mascot with a darning needle, and the real logo, come later from a claude.ai design project.

## M0: Placeholder (as soon as possible)

- [x] Add `LICENSE` (Apache-2.0), `README.md`, `ROADMAP.md` and `AGENTS.md`.
- [x] Add the placeholder mark and lockups to `docs/brand/`.
- [ ] Scaffold Vite, strict TypeScript and Vitest with pnpm, and a static screen: the lockup, a drop zone, and one empty preview on GitHub's dark background.
- [ ] Design tokens: a spacing scale, a type scale, a palette with shades and a few shadow levels, with a high-contrast theme that follows the system setting.
- [ ] A Content-Security-Policy in `index.html` with `connect-src 'none'`, so the browser itself blocks any network call.
- [ ] Guardrail tests, each with a positive control: no `fetch`, `XMLHttpRequest`, `WebSocket`, `EventSource` or `sendBeacon` in `src/`, and no URL to another origin in the built `dist/`. A deliberate `fetch` fails the first.
- [ ] CI on GitHub Actions: typecheck, test, build, and secret scanning with the gitleaks release binary, checked against its published checksum and run with `--log-opts="--full-history HEAD"`.
- [ ] Deploy the static build to Vercel from `main`, with a preview for each pull request.

**Done when:** CI is green on `main`, the network guardrail fails on a deliberate `fetch` in a throwaway branch, and the Vercel URL shows the placeholder screen.

## M1: Show

- [ ] Drop or pick a PNG. It's decoded in the browser, and the original `File` is kept untouched.
- [ ] A preview grid: white, black, GitHub light (`#ffffff`) and dark (`#0d1117`), Discord dark, a checkerboard and up to three brand colours you pick, each at 50%, 100% and 200%.
- [ ] Brand colours are remembered in `localStorage`, and the page works without it.

**Done when:** both mascot stickers show on every background at every zoom, and changing a background redraws the grid within 100 ms for a 4096 × 4096 PNG, measured in Chrome.

## M2: Find

- [ ] The hole finder as a pure function over RGBA pixels, in a Web Worker.
- [ ] Holes are drawn over the previews and listed with their size and place.
- [ ] Slivers that reach the outside through a neck of 2 px or less get a "check this" warning, never a fill.
- [ ] Test fixtures: `stay-put.png` and `still-sipping.png` copied from Staypaw and Sweepling, plus a sticker with no hole.

**Done when:** the tests find exactly 2 holes in `stay-put.png` and 1 in `still-sipping.png`, none in the clean sticker, and the space between Primrose's hip and her mug comes back as a warning, not a hole.

## M3: Mend

- [ ] Detect the border colour: the commonest opaque colour touching the outside. You can pick another.
- [ ] Approve or skip each hole, with a before and after toggle on every background.
- [ ] Download the mended copy as a PNG.
- [ ] The README's "after" column shows Darnling's own mended stickers.

**Done when:** a test re-runs the finder on both mended stickers and finds no holes, every pixel outside the approved holes is byte-identical to the original, and nothing writes to the original file.

## M4: Export

- [ ] Check each target's sizes and limits against its own docs, and record them here with the date and link: GitHub READMEs, Discord emoji and stickers, Telegram stickers, LINE stickers, Bluesky, and the blog.
- [ ] One preset per target, and a zip of the whole set.

**Done when:** every preset's numbers have a dated link in this file, and a test checks each exported file against them.

## M5: 0.1

The OSS launch gates:

- [ ] The core journey works end to end: drop, see, find, mend, export.
- [ ] Opening the Vercel URL in a fresh browser works, and the supported browsers are listed in the README.
- [ ] The README's quick start is accurate, and it shows real before and after examples.
- [ ] `LICENSE`, `CONTRIBUTING.md` and `SECURITY.md` exist.
- [ ] CI is green, with no known critical bugs.
- [ ] Error messages are understandable, for a file that isn't a PNG, one that's too big, and one with no transparency.
- [ ] Issues are ready for reports.
- [ ] A code-review agent reads the code before the release.

**Done when:** every box above is ticked and Calla's stay-put sticker, mended by Darnling, is back in Staypaw's README.

## Later

- Animated stickers: APNG, GIF and WebM.
- Light and dark halos at the edge (defringe).
- Stray pixels: small opaque islands apart from the art.
- Add a die-cut border to art that has none.
- Many files at once.
- Works offline, through a service worker.
- `darnling.app`, if it's still free.

## Not planned

- **Uploads, accounts or a server,** because they break the privacy promise.
- **AI upscaling or background removal.** Other tools do these, and anime-pipeline already runs rembg.
- **A step in anime-pipeline,** the owner's call (see Decisions).
- **Native desktop or phone apps.** The browser covers both.

## How we'll know it works

A dogfooding log: every sticker the owner posts goes through Darnling first, and a hole that shows after posting is a miss. Misses must stay at zero. After 0.1, public signals: issues and pull requests from strangers, and people mending stickers without asking for help.
