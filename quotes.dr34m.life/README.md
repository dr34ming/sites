# quotes

Full-bleed quote kiosk for `quotes.dr34m.life`. One short line at a time on a
1920×1080 screen, forever. No interaction, no chrome.

## Add a quote

Edit `quotes.txt`. One quote per line, `#` comments, blank lines ignored.
Deploy. The screen picks it up within ~10 minutes — nobody touches the browser.

```
Be here now.
Gold is the corpse of value. | Neal Stephenson, The Confusion
Courage = Fear + Action
```

Optional attribution follows a pipe. A line containing ` = ` is typeset as an
equation (mono, operators recessed); everything else gets the title-card
treatment (uppercase, heavy, fitted to the frame). The page reads the grammar
of the line — that split is the whole design, and it costs you nothing: write
plain lines and you get plain title cards.

The current contents are seed content from Jonathan's own 2025-01-21 list.
Replace them.

## Deploy

```
cd ~/Code/docs/sites/quotes && npx wrangler pages deploy . --project-name=quotes
```

Cloudflare Pages does not auto-deploy from git. A `git push` is not a deploy.

The project is `quotes`, but the hostname is **`quotes-a3e.pages.dev`** — the
bare `quotes.pages.dev` was already taken, so Cloudflare appended a suffix at
project creation. `quotes.dr34m.life` CNAMEs to the suffixed host.

## URL params

| param | default | what it does |
|---|---|---|
| `dwell` | `30` | seconds per quote |
| `dim` | `0.34` | night brightness, or `off` to disable dimming |
| `refresh` | `10` | minutes between `quotes.txt` re-fetches |
| `reload` | `4` | hours between page reloads, `0` to disable |
| `src` | `quotes.txt` | alternate corpus file, same origin only |

Keys, for when you're standing at the screen: space / → next, ← back, `r`
re-fetch now.

## What drives the screen

A kiosk browser pointed at `https://quotes.dr34m.life`. Nothing else. The page
is one self-contained `index.html` — inline CSS and JS, no CDN, no web fonts,
no external requests. The only network call it ever makes is to its own
`quotes.txt`. A kiosk that depends on a third party is a kiosk that goes blank
one day.

Running unattended for months, so:

- **Burn-in** — true black, and the composition traces a slow Lissajous orbit
  (397s × 271s, coprime) so no pixel holds a bright edge.
- **Night** — dims on a ramp after 22:00, back up by 07:00, local time.
- **Stall** — a watchdog forces an advance if the cycle timer ever misses by
  2.5× the dwell. Backgrounding the tab and returning re-fits and re-advances.
- **Rot** — the page reloads every ~4h (jittered), but only after proving
  `quotes.txt` is reachable. It never reloads into a network hole.
- **Silence** — the corpus is cached in `localStorage`, so a failed fetch keeps
  the screen alive on the last good copy. If it has not confirmed a fresh copy
  in 30 minutes it prints `quotes.txt unreachable · 2h 14m` in 12px in the
  corner: invisible from the couch, unmissable standing at the screen. If there
  is no corpus at all it says so. It never falls back to an invented quote.
