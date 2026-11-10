# Deployment / Hosting Notes

## Hosting

The site is hosted on [MIKR.US](https://mikr.us), a Polish low-cost VPS provider — see the comment in [index.html](../index.html) and the footer's "Hosted on MIKR.US" link. `index.html` is the stock MIKR.US landing page template (unmodified aside from the content section).

## Expected server file layout

The automation scripts hardcode `/var/www/html` as the web root:

```
/var/www/html/
  *.php, *.py, *.sh        # this repo's contents
  media/                    # MP3 recordings + .txt playlists (audio.php, radio.php)
  originals/                  # raw captures awaiting cleanup (streamripper.sh, cleaner.sh)
  logs/                         # streamripper.sh capture logs
  przepisy/                       # PDF recipes (przepisy.php, przepisy_view.php)
  przepisy2/                       # Markdown recipes (checked into this repo)
```

`media/`, `originals/`, `logs/`, and `przepisy/` are runtime data directories that must exist on the server but are intentionally not part of this repository (no code depends on them being pre-populated; PHP scripts use `glob()` defensively and handle missing files).

## Requirements

- **PHP** with standard filesystem functions (`glob`, `fopen`, `readfile`), no extensions or Composer dependencies — Markdown parsing uses the vendored [przepisy_parser.php](../przepisy_parser.php) instead of a package.
- **Python 3** with `requests` and `beautifulsoup4` for [playlist.py](../playlist.py).
- **ffmpeg** and **curl** on the server for the capture/cleanup shell scripts.

## Privacy policy

[privacy_policy.md](../privacy_policy.md) is the privacy policy for a separate, unrelated mobile app that uses Firebase Cloud Messaging for push notifications. It is hosted here as a static page but documents no data collection by that app (not by this PHP site).
