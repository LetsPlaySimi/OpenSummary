# OpenSummary

OpenSummary turns your photos into two things you can post: a clean summary image showing the EXIF data (aperture, shutter speed, ISO, focal length, date, camera, lens), and a set of equal slices of the same photo for carousel posts.

It runs entirely in your browser. There is no server, no account and no upload: your photos are read and processed on your own device.

Live at **[opensummary.app](https://opensummary.app)**.

## What it does

- **Summary image.** Draws the photo blurred in the background with the EXIF details on top. You can adjust the blur and text size and add a copyright line.
- **Split.** Cuts a photo from left to right into equal slices (2 or more) so a wide shot continues across carousel slides.
- **Batch.** Select several photos at once. "Download all" saves every result.
- **Output.** JPEG files named `<original>_summary.jpg` and `<original>_<n>of<total>.jpg`.

## How it is structured

There is no build step, no framework and no package manager. The whole app is static files.

```
index.html        The app: markup, CSS and JavaScript in one file
onboarding.html   One-time intro page, shown on first visit
exif.js           EXIF reader (fork of exif-js, see Credits)
icons/            SVG icons used on the summary image
huey.jpg          Example photos used on the onboarding page
b747.jpg
```

Inside `index.html`, the script is organised roughly in this order:

1. **Setup.** Loads the icons by name and defines small helpers for turning a canvas into a Blob and an image URL into an `Image`.
2. **EXIF formatting.** `getExif` reads the file with `exif.js`, a fork of exif-js. `buildInfo` formats the values and splits them into a top group (exposure settings and date) and a bottom group (camera, lens, copyright). Values that are missing or invalid are skipped rather than printed as `NaN`.
3. **Drawing.** `drawSummary` paints the blurred photo onto a canvas, then the icons and text. `drawPiece` crops slice *i* of *n* out of the original. All sizes are relative to the image's shorter side, so a preview and a full-resolution render look the same.
4. **UI and rendering.** `render` runs when the files or options change. It produces a small preview for the screen and defers the full-quality render until the user downloads, using the same parameters. Output is capped at 16 megapixels to stay within mobile canvas limits. Rapid slider changes are debounced, and a run counter cancels stale renders.
5. **Downloads and input.** "Download all" saves files one at a time. Drag and drop is supported on the upload box.

Two small pieces of state are kept in `localStorage`: whether the onboarding page has been seen, and the theme choice. Nothing else is stored. Crawlers skip the onboarding redirect.

The only external request the app makes is for [context-filter-polyfill](https://github.com/davidenke/context-filter-polyfill), loaded from jsDelivr. It makes the canvas `filter` property (used for the blur) work on iOS, where it is not supported natively. Fonts and styles for the app itself are local.

## Running it locally

Any static file server works. For example:

```sh
git clone https://github.com/LetsPlaySimi/OpenSummary.git
cd OpenSummary
python3 -m http.server 8000
```

Then open `http://localhost:8000`. Opening `index.html` directly from disk mostly works too, but a local server is more reliable.

## Deployment

The site is deployed at [opensummary.app](https://opensummary.app) as plain static files. To host your own copy, upload the contents of the repository to any static host. No configuration is needed.

## Contributing

Issues and pull requests are welcome. Keep changes small and focused where you can, and test with a few real photos, including ones with missing EXIF data.

**Vibecoding is 100% allowed.** Code written with an AI assistant is welcome here, and you do not need to disclose it. The only standard is that the change works and you have run it yourself.

By contributing, you agree that your contribution is released under the same license as the project (see below).

## Credits

OpenSummary builds on two open source libraries:

- [exif-js](https://github.com/exif-js/exif-js) reads the EXIF data from photos in the browser. OpenSummary uses [a fork with fixes](https://github.com/LetsPlaySimi/exif-js-fix), which is included in this repository as `exif.js`.
- [context-filter-polyfill](https://github.com/davidenke/context-filter-polyfill) by David Enke makes `ctx.filter` (the canvas blur) work on iOS.

Thanks to their authors and contributors.

## License

OpenSummary is licensed under [Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International](https://creativecommons.org/licenses/by-nc-sa/4.0/) (CC BY-NC-SA 4.0). In plain terms:

- You can copy, modify and share it.
- You must credit OpenSummary and link to this repository.
- Anything you build from it must be released under the same license.
- You may not use it for commercial purposes or make money from it in any way.

The full text is in [LICENSE](LICENSE). The libraries listed under Credits are not covered by this license and keep their own.
