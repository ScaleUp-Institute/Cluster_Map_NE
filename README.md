# North East Scaleup Map

A regional edition of the ScaleUp Institute's UK Scaleup Map, prepared for the
British Business Bank. The map opens on the whole United Kingdom, as the full
tool does, with the North East outlined and its scaleups plotted on it. Every
filter, dashboard and layer of the full tool is present and works.

## What is in it

**521 companies**, every one of them in the North East — 97.3% of the national
file is not here. The restriction is in the data, not the interface: this build
is generated from a payload that holds the region and nothing else, so there is
no national dataset behind the page to read out of the browser.

| | North East | national file |
|---|---:|---:|
| Companies | 521 | 19,138 |
| Company–sector rows | 782 | 28,517 |
| Investors | 138 | 5,234 |
| Procurement suppliers | 24 | 950 |
| Primes (offices in region) | 15 (17) | 191 |
| Westminster constituencies | 27 | 650 |

Everything derived from companies follows the same cut. Each investor's company
list is pruned to the region, so the record cannot be used to recover national
reach. Prime office counts are reset to the offices actually shipped. Seat
statistics are recomputed from the retained companies rather than inherited.

The twelve ITL1 region outlines are kept so the UK coastline still draws. They
are public geography and carry no business data.

## Access

The two data files are encrypted with AES-256-GCM over gzip, the key derived by
PBKDF2-SHA-256 at 600,000 rounds. `js/gate.js` asks for the passphrase and
decrypts in the browser; there is no plaintext on the server to reach.

This edition has **its own passphrase**, unrelated to the national build's.
Neither opens the other.

## How it is built

```
py tools/make_region_build.py --region "North East (England)" --out build/ne
py tools/make_deploy.py secure --out site-ne --data-dir build/ne \
     --key-out PASSPHRASE-NE.txt
```

The first command cuts the national data down to one region. The second
assembles the site and encrypts that cut copy instead of the national files.
Both live in the main Cluster Map repository; this repository holds only the
built output.

`js/app.js` is shared with the national build and behaves identically unless the
data declares `meta.scope`, which a region build sets. That is what hides the
regions comparison, drops the single-choice region picker, tints the region on
the map and rewrites captions that would otherwise claim a national population.
