# MMN Player

"MMN" (Melodie Mgieł Nocnych) is a late-night radio show on Radio Kampus. This part of the site records, archives, and plays back episodes.

## [audio.php](../audio.php)

A minimal static list:

- Scans `./media` for files matching `melodie*.mp3`.
- For each file, looks for a sibling `.txt` playlist file (same base name) and uses its contents as the link's `title` tooltip.
- Prints a `<a href download>` link per recording.

Note: the displayed link text uses the literal string `basename($filename)` instead of calling the function — this looks like a pre-existing bug (the file name won't render, only the `download` prompt/tooltip works).

## [radio.php](../radio.php)

An interactive player (`MMN - Player`):

- Scans `./media/*.mp3` and parses the capture timestamp from the filename using the pattern `melodie.YYYY_MM_DD_HHHMMM.mp3` (e.g. `melodie.2025_08_25_23H55M.mp3`).
- Groups recordings into a `year → month → songs` tree (`$songsByDate`), rendered as nested, collapsible `<div>`s.
- Reads each recording's matching `.txt` file (if present) as its playlist description.
- Client-side JS (inline `<script>`) toggles month sections and, on clicking a song, sets `<audio>`'s `src` and displays the playlist text in a side panel.
- Files without a parseable timestamp fall back to being grouped under year `"Inne"` ("Other"), month `"00"`.

## [playlist.py](../playlist.py)

A Python scraper that fills in the `.txt` playlist files used by `audio.php`/`radio.php`:

- Runs from `/var/www/html/media` (hardcoded `os.chdir`).
- Downloads `https://melodienocy.blogspot.com/p/muzyka-z-melodii-mgie-nocnych.html` with `requests` + `BeautifulSoup`.
- Extracts the blog's `div.post-body.entry-content` section, splits it into per-date blocks (blocks start with a `DD.MM.YY` date header).
- For every `.mp3` in the current directory without a matching `.txt`, parses the date from the filename and writes the matching block's text into `<name>.txt`.
- Intended to run periodically (e.g. via cron) after new recordings are captured, see [scripts.md](scripts.md).

## Supporting scripts

- [streamripper.sh](../streamripper.sh) — records the live stream into `originals/`.
- [cleaner.sh](../cleaner.sh) — trims the recording and moves the final MP3 into `media/`.

Full details in [scripts.md](scripts.md).
