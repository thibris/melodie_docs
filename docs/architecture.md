# Architecture

This repository contains the PHP source for a personal website hosted on [MIKR.US](https://mikr.us). It has no build step, framework, or database — pages are plain PHP scripts that read the filesystem directly (MP3s, text/markdown files, PDFs) and render HTML.

## Top-level layout

```
index.html            Static landing page (MIKR.US default template)
header.php             Shared navigation bar, included by every page
footer.php              Shared disk-usage widget, included by every page
audio.php               Simple downloadable list of MMN recordings
radio.php                Interactive player for MMN recordings (year/month/song tree)
playlist.py                Scrapes playlists for recordings from a blog
cleaner.sh                  Trims/transcodes the largest raw capture and moves it to media/
streamripper.sh               Records the live radio stream to a timestamped file
przepisy.php                   Recipe browser v1 (lists PDFs, opens them via przepisy_view.php)
przepisy_view.php                Streams a single PDF safely (path validated against traversal)
przepisy2.php                     Recipe browser v2 (Markdown-based, two-pane UI)
przepisy_parser.php                 Vendored Parsedown library used to render Markdown to HTML
przepisy2/<recipe name>/lista.md      Ingredient list for a v2 recipe
przepisy2/<recipe name>/przepis.md     Preparation steps for a v2 recipe
przepisy2/<recipe name>/*.jpg|png|...    Optional photos shown in a gallery
privacy_policy.md                         Privacy policy for an associated mobile app (FCM push notifications)
README.md                                  One-line reference links used during development
```

## Request flow

All dynamic pages (`audio.php`, `radio.php`, `przepisy.php`, `przepisy2.php`) follow the same pattern:

1. `include 'header.php'` — renders the top navigation (`<ul>` of links between the player and recipe versions).
2. Page-specific logic — scans a directory with `glob()`, builds the page body.
3. `include 'footer.php'` — renders a disk-usage bar for the server's `/` filesystem.

There is no shared PHP framework, session handling, or database; state is entirely derived from files on disk under the web root (expected to be `/var/www/html` based on the automation scripts — see [deployment.md](deployment.md)).

## Data directories (not checked into the repo)

The PHP scripts expect these runtime directories to exist alongside the code on the server:

- `media/` — MP3 recordings (`melodie*.mp3`, or `*.YYYY_MM_DD_HHHMMM.mp3`) plus a sibling `.txt` file per recording holding its scraped playlist.
- `przepisy/` — PDF recipe files served by [przepisy.php](../przepisy.php) / [przepisy_view.php](../przepisy_view.php).
- `originals/` and `logs/` — used by the recording scripts ([streamripper.sh](../streamripper.sh), [cleaner.sh](../cleaner.sh)).

`przepisy2/` (Markdown recipes) **is** checked into this repo, one folder per recipe.
