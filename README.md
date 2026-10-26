<h1 align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="docs/brand/lockup-dark.svg">
    <img alt="Mendmoth" src="docs/brand/lockup.svg" height="48">
  </picture>
</h1>

**Mend your sticker before dark mode finds the holes.** Mendmoth shows your sticker on every background, fills its see-through gaps, and exports it for every place you post.

A sticker can look finished on a white page and still have see-through gaps inside its outline: a sliver between an arm and the body, a gap under the hair. On GitHub's dark mode or in Discord those gaps show as dark slivers, and you only find out after you've posted. Mendmoth finds them, fills them in a copy, and leaves your original alone.

> **Status:** planning. Nothing works yet. See [ROADMAP.md](ROADMAP.md) for what's next.

## Before and after

These are real stickers from two of my other projects, on GitHub's dark background. Mendmoth's hole finder, prototyped on 2026-10-24, marks the holes in magenta.

![Calla, a cat-eared homebody with warm white hair, stands with her arms down. On the left, two dark slivers show between her arms and her body. On the right, the same slivers are filled magenta and ringed.](docs/readme/calla-stay-put-before.png)

![Primrose, a blonde café owner, waves with both hands. On the left, a dark gap shows under her hair beside her raised hand. On the right, it's filled magenta and ringed.](docs/readme/primrose-still-sipping-before.png)

| You're… | Without Mendmoth | With Mendmoth |
|---|---|---|
| adding a sticker to a README | it looks fine in light mode, and dark slivers show for everyone using dark mode | you see it on GitHub's light and dark before you commit, and the holes are filled |
| uploading an emoji to Discord | a gap you never saw shows against Discord's dark theme | you checked it on Discord's dark first |
| making a Telegram or LINE pack | each sticker needs its own size and limits, which you look up every time | one export makes the set |
| fixing a hole you did spot | you open an art tool, find the hole by eye and paint it | Mendmoth finds it and fills it with the sticker's border colour, once you approve |
| sharing the art | you're not sure the fix changed only the hole | every pixel outside the holes you approved stays exactly as it was |

Calla is from [Staypaw](https://github.com/hazeliscoding/staypaw), where her stay-put sticker stays out of the README until these gaps are filled. Primrose is from [Sweepling](https://github.com/hazeliscoding/sweepling).

## What it does

Planned for 0.1:

- **Shows** your sticker on white, black, GitHub's light and dark, Discord's dark, a checkerboard and your own brand colours, side by side, at 50%, 100% and 200%.
- **Finds holes:** see-through areas inside the art that the outside can't reach. A narrow gap that does reach the outside gets a "check this" warning, since it may be meant.
- **Mends them in a copy,** with the sticker's border colour, like the vinyl of a real die-cut sticker. You approve each fill.
- **Exports** sets for READMEs, Discord, Telegram, LINE, socials and blogs, sized from each platform's own docs.

## The privacy promise

- **Nothing leaves your browser.** Mendmoth is a static page. There are no uploads, no accounts, no analytics and no network calls beyond loading the page.
- **Your original is never touched.** Mends happen in a copy, and only the pixels you approved change.
- **Open source,** so you can check all of this instead of trusting it.

## Alternatives

Checked against each tool's own page on 2026-10-24.

| | Mendmoth (planned) | [imageonline.io Check Transparency](https://imageonline.io/preview-transparency/) | [Photoshop matting](https://helpx.adobe.com/photoshop/desktop/make-selections/refine-modify-selections/fringe-pixels-around-a-selection.html) | [Emotify](https://github.com/Dartsgame974/Emotify) |
|---|---|---|---|---|
| Previews on dark backgrounds | GitHub, Discord, black, your colours | a checkerboard | any layer you put behind it | no |
| Finds holes inside the art | yes, apart from the outside | highlights every see-through pixel, the outside too | no | no |
| Fixes them | fills holes with the border colour | no | edge fringe and mattes (Defringe, Color Decontaminate) | no |
| Exports sizes for each platform | READMEs, Discord, Telegram, LINE, socials | no | by hand | Twitch emotes and Discord at 256 px |
| Runs where | your browser, nothing uploaded | your browser, nothing uploaded | the Photoshop app | a Windows exe or Python, on your PC |

**Use Photoshop** when your sticker's problem is a light or dark halo around its edge: its matting tools fix that today, and Mendmoth doesn't yet. **Use imageonline.io** to see at a glance how much of a file is see-through. **Use Emotify** to batch-resize Twitch emotes.

## Contributing

Issues are welcome. Planning lives in [ROADMAP.md](ROADMAP.md).

## License

[Apache-2.0](LICENSE). Calla and Primrose, the characters in this README, were drawn with an AI image model, and they're Apache-2.0 like the code, as in their own repos.
