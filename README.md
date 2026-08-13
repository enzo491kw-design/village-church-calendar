# Village Church Kelvin Grove — auto-updating calendar

Public feed: https://enzo491kw-design.github.io/village-church-calendar/events.ics
Subscription link: webcal://enzo491kw-design.github.io/village-church-calendar/events.ics

## How it updates

`/home/simon/.hermes/scripts/village-calendar/sync.py` runs every 12 hours through a Hermes no-agent cron job. It:

1. fetches the public https://www.vc.org.au/events/ page;
2. uses BeautifulSoup and Elementor event-section structure to write `events.json`;
3. produces RFC 5545 `events.ics` with `TZID=Australia/Brisbane`;
4. commits and pushes only if calendar event content changed.

No WordPress event REST type is published by the source site, and no existing ICS link was found. The scraper therefore uses the public HTML and safely skips a listing it cannot date or time.

## Event policy

- Clearly old, undated single listings are omitted rather than treated as next year.
- A no-year date is assigned the current Brisbane year; if it is only recently passed it rolls forward one year, while entries more than 30 days old are regarded as stale page content and skipped.
- `Kelvin Grove Playgroup` is a weekly Friday RRULE, 09:30–11:00. The source says “during school term” but does not specify term dates, so no imaginary term boundaries are added.
- `The Story` is omitted because the site provides interest registration but no session date/time.
- A single supplied start time without an end is published as one hour because no duration is provided by the source.

## Manual run

```bash
python3 /home/simon/.hermes/scripts/village-calendar/sync.py
```

The job intentionally produces no standard output and exits 0 when the feed is unchanged. Errors go to stderr and return non-zero.

## Files

- `scrape.py`: source retrieval and tolerant HTML extraction.
- `generate_ics.py`: RFC 5545 feed writer, using CRLF line endings and folded content lines.
- `sync.py`: regeneration plus GitHub push.
- `events.json`: last normalized scrape result (published for transparency).
- `events.ics`: subscribed calendar feed.
