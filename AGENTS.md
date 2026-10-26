# AGENTS.md

These are the working rules for agents in this repo. Mendmoth is a web app (Vite, plain TypeScript, Canvas 2D, Apache-2.0) that shows a sticker on every background, fills its see-through gaps, and exports it for every place you post. It runs entirely in the browser.

## Sources of truth

- `README.md`: the pitch and the privacy promise.
- `ROADMAP.md`: decisions already made, the milestones, and what is out of scope. Check it before proposing features. Respect those decisions unless the owner reopens them.
- Work from the next unchecked item in `ROADMAP.md`. Don't build past the current milestone without asking.

## The privacy promise (hard rules)

People drop unreleased art into Mendmoth. They'll only do that if nothing can leave the page, so never break these, not even in dev tooling that ships.

- **No network calls from the app.** No `fetch`, `XMLHttpRequest`, `WebSocket`, `EventSource` or `navigator.sendBeacon`, and no package that makes them. A guardrail test fails on any of these in `src/`, and the Content-Security-Policy in `index.html` keeps `connect-src 'none'` as a second lock. Loading the page is the only traffic.
- **No third-party origins.** No CDN scripts, web fonts, images or iframes from another origin: bundle or self-host them. A test fails on another origin's URL in the built `dist/`. Every outside request tells someone that the page was opened.
- **No analytics or telemetry,** Vercel's included. Don't add `@vercel/analytics` or `@vercel/speed-insights`, and keep them off in the Vercel project. The deploy is a static build: no Vercel Functions, no middleware.
- **Files stay in memory.** A dropped file is read in the browser and never written to `localStorage` or IndexedDB. `localStorage` holds only settings, such as brand colours, and the page works when it's unavailable.

## Faithful pixels (hard rules)

- **The original is never changed.** Keep the dropped `File` as it came. Every mend works on a copy of its pixels.
- **Only approved holes change.** Every pixel outside an approved hole stays byte-identical. Don't premultiply, resample or colour-convert the copy you export at 100%. A test compares them pixel by pixel. A tool that quietly shifts colours can't be trusted with someone's art.
- **A sliver that reaches the outside is a warning, never a fill,** because it may be space the artist meant (Primrose's hip and her mug, in `ROADMAP.md`).
- **The fixtures keep their holes.** `stay-put.png` and `still-sipping.png` are the tests' real cases. Never mend, recolour or replace them, and copy any new version from Staypaw or Sweepling instead of editing it here.

## Code

- TypeScript with `strict` on. pnpm manages packages.
- The hole finder and the mender are pure functions over `{ width, height, data }` RGBA pixels, with no DOM, so Vitest runs them in Node. The UI calls them through a Web Worker, so a large PNG never blocks the page.
- Canvas 2D draws the previews. Read pixels with `willReadFrequently` only where pixels are read back.

## Brand

- The logo is the placeholder heart with a woven darn patch in `docs/brand/`. `scripts/make_lockup.py` rebuilds the lockups from `mark.svg` and `mark-dark.svg`, so rerun it when the mark changes: `uv run --no-project --with fonttools --with uharfbuzz python -I scripts/make_lockup.py <Fredoka-SemiBold.ttf> docs/brand`. Its header says where to get the font.
- Calla (from Staypaw) and Primrose (from Sweepling) appear in the README. Never edit, recolour or redraw them here: each set comes from one written spec and one reference image, and a sticker changed by hand drifts from the rest.

## Dependencies

- **A special-purpose library** (a PNG encoder, a zip writer): add it yourself, with its reason in the commit message.
- **A general-purpose helper:** only after checking that nothing in the project or the platform already does the job.
- **A framework:** never without asking the owner. The UI is plain TypeScript by decision.
- **Anything that makes network calls or loads from another origin:** never. See the privacy promise.

## Glossary

One meaning per term. Use these words in code, docs and commits.

- **See-through:** a pixel under 50% opacity (alpha below 128).
- **Outside:** the see-through area connected to the image's border.
- **Hole:** see-through pixels the outside can't reach. Mendmoth fills these, once approved.
- **Sliver:** a see-through area that reaches the outside through a neck of 2 px or less. It gets a warning, never a fill.
- **Border colour:** the commonest opaque colour touching the outside, usually a sticker's white die-cut edge. Holes are filled with it.
- **Mend:** filling approved holes in the copy.
- **Original:** the file you dropped, which never changes.
- **Preset:** the sizes and limits for one place you post, from that platform's own docs.
- **Background:** one colour or pattern the previews draw the sticker on.

## Commands

Added in M0 with the scaffold.

## Working style

- **Commits:** [Conventional Commits](https://www.conventionalcommits.org/) (`feat:`, `fix:`, `docs:`, `chore:`, `test:`, `ci:`, `build:`, `refactor:`). Keep each commit atomic, and use a scope when it adds clarity (`feat(finder): …`).
- **No AI attribution** in commits or PRs. That means no `Co-Authored-By` trailers, no "Generated with" lines and no session links.
- **Checks:** automate acceptance checks instead of handing manual steps to the owner. Give every check that tests for an absence a positive control. For example, the network guardrail must fail on a deliberate `fetch`.
- **Structural rules get tests.** When a decision is a rule about the code's shape, add a test that fails when it's broken.
- **Validation:** evidence comes from dogfooding and public signals (issues, pull requests). Don't plan interviews, recruiting or outreach.
- **Docs:** short and concise. Prefer editing `ROADMAP.md` over creating new planning documents.
- **Code comments:** explain why, not what. Comment only what the code can't say for itself, such as a browser quirk or a line that keeps a hard rule.
- Local Playwright output goes to `.playwright-mcp/`, which git ignores.
