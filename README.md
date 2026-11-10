# downco-down

The Downloader in [downco](https://github.com/chchee3300/downco), a desktop app that downloads and converts media on your own computer. To use the Downloader, [install downco](https://github.com/chchee3300/downco#install). This repository holds its source and isn't an app by itself.

## What it does

- Paste a link to see the title, channel, length and thumbnail, then pick the video quality and the audio quality from the formats the site offers.
- Download several videos in a queue, each with its own settings and progress bar. Removing a video from the queue cancels only that download.
- Merge the chosen video and audio into one MP4, or save them as two separate files. Sources that only offer one combined stream, such as Twitch clips and most videos on X, are downloaded as they are.
- Clip a video to a start and end time before downloading, with a preview player when the site provides a direct video link.
- YouTube, Twitch and X get a badge in the queue. Any other site supported by [yt-dlp](https://github.com/yt-dlp/yt-dlp) also works.

[yt-dlp](https://github.com/yt-dlp/yt-dlp) does the downloading, and the ffmpeg bundled with downco merges the streams. YouTube offers yt-dlp all of its formats only when a JavaScript runtime is available. If [Deno](https://deno.com/) is installed through downco's Settings → Third-party software, the Downloader passes it to yt-dlp.

Downloading is subject to each site's terms of service. Only download videos you have the right to keep, such as your own uploads or openly licensed content.

## How it's built

The repository is a [Tauri v2](https://tauri.app/) plugin in two parts, installed together as one npm package (`sorai-toolkit-downloader`):

- `plugin/` is the Rust crate `tauri-plugin-sorai-downloader`. It runs `yt-dlp -j` and sorts the formats, builds the download command (format selection, merging, clip range), reads progress, and runs and cancels downloads.
- `src/` is the React UI. Its library build (`vite.lib.config.mjs`) writes `dist/index.js`, which exports `DownloaderApp` and the plugin's command wrappers. npm runs the build through the `prepare` script when downco installs the package. React and `@tauri-apps/api` are peer dependencies, and the stylesheet lives in downco's `resources/styles.css`.

The UI only works inside downco's Tauri runtime. To work on it, check this repository out next to downco as `sorai-toolkit-downloader` and run `npm run tauri:dev` in downco. Changes to `src/` reload in place, and changes to `plugin/` rebuild and restart the app.

## Tests

```bash
cd plugin && cargo test
```

These cover format parsing (with saved YouTube, Twitch and X responses), the download command, progress parsing and finding the bundled programs. End-to-end tests run in downco with real downloads and need a network connection: `npm run test:e2e -- downloader`.

## Versions

This repository has no releases of its own. downco pins a commit of it in `package-lock.json` and ships it as part of each downco release, so commit messages here don't need Conventional Commits prefixes.

## License

[MIT](LICENSE). yt-dlp and ffmpeg keep their own licenses: see downco's [`THIRD-PARTY-LICENSES.md`](https://github.com/chchee3300/downco/blob/master/THIRD-PARTY-LICENSES.md).
