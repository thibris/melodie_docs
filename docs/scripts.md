# Automation Scripts

Two shell scripts and one Python script automate capturing and preparing MMN recordings. They are designed to run on the server (`/var/www/html`), typically via cron, not from this repo checkout.

## [streamripper.sh](../streamripper.sh)

```
streamripper.sh <prefix> <duration_seconds> <suffix>
```

- Captures the live stream at `https://stream.radiokampus.fm/kampus` using `curl` for `<duration_seconds>` seconds.
- Builds a timestamped filename: `<prefix>.<YYYY_MM_DD_HHHMMM>.mp3`, saved under `/var/www/html/originals/` as `<suffix>_<prefix>.<timestamp>.mp3`.
- Logs curl's output to `/var/www/html/logs/<prefix>.<timestamp>.log`.
- Commented-out lines show an earlier approach that trimmed with `ffmpeg` directly after capture; this is now done separately by `cleaner.sh`.

## [cleaner.sh](../cleaner.sh)

- Looks at `/var/www/html/originals/`, picks the **largest** file (`ls -S | head -n 1`) — i.e. the most recently completed full-length capture.
- Trims it with `ffmpeg` (copies the audio codec, starting at `00:08:15` for a `01:00:00` duration) to strip leading dead air/ads, writing the result to `/var/www/html/media/<name without first 2 chars>`.
- Deletes everything in `originals/` afterward.

Expected usage: run `streamripper.sh` to capture, then `cleaner.sh` once capture finishes to produce the final file consumed by `audio.php`/`radio.php`.

## [playlist.py](../playlist.py)

- Python 3, depends on `requests` and `beautifulsoup4`.
- Run after `cleaner.sh` deposits new files in `media/`; it fills in missing `.txt` playlist files by scraping the MMN blog.
- See [mmn-player.md](mmn-player.md#playlistpy) for scraping details.

## Suggested cron ordering

1. `streamripper.sh <show> <duration> <tag>` — scheduled to start at the show's broadcast time.
2. `cleaner.sh` — scheduled shortly after the capture duration ends.
3. `python3 playlist.py` — scheduled after `cleaner.sh`, so the new file in `media/` already exists when the playlist is scraped.

No scheduler configuration (crontab, systemd timer) is included in this repo; set it up on the host separately.
