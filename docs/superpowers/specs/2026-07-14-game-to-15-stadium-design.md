# Game to 15 (Direction 08): stadium night game re-skin

Date: 2026-07-14
Status: approved by Matt (chose "stadium night game" over retro arcade and chrome-only options; approved full design including frisbee HUD and serif reading columns)

## Problem

`style-guide/08-game-to-15.html` has game *mechanics* (scroll-scoring scoreboard,
momentum meter, point 15 flood) but reads visually as a light editorial magazine
piece. The client wants it to feel like an actual game: video-game energy, real
frisbee-game feel, digital scoreboard aesthetics.

## Design

### The world

- Night-game re-skin: near-black field green ground (about #0B120D), chalk-white
  field lines structuring sections, floodlight gradient pools on the hero.
- Universe Orange #FF4A00 stays the team color and becomes the LED glow color.
  Marigold #FFB61E is the amber LED accent. Indigo and Tidal survive as per-point
  accents where contrast allows on dark.
- Photographs and the halftime video render clean, no tint or filter, framed as
  broadcast stills (existing scrim rule stays within the current limit).

### The scoreboard strip becomes a real LED board

- Black board, dot-matrix digits set in the Google font "Doto" with a soft
  orange glow (text-shadow bloom).
- DISCNW / YOU as home/guest, blinking LIVE dot, score in dot-matrix.
- The momentum meter becomes a segmented LED power bar (discrete blocks that
  fill as points are scored), not a smooth gradient line.
- At 15 the board flips to a FINAL state, as today, restyled to match.

### The numerals become scoreboard digits

- Each point's colossal numeral renders in Doto with LED glow instead of Space
  Grotesk print type. The colored offset echo becomes an LED afterglow
  (blurred colored copy behind the digits).
- Spark bursts stay, reading as stadium flash pops.

### The frisbee-game layer

- Field-position HUD: a small chalk field diagram in each point's header row, a
  disc marker advancing up the field proportionally to the point number,
  reaching the end zone at point 15. Pure CSS/SVG, decorative, aria-hidden.
- Disc-pull reveal: when a point section reveals, a small disc glyph arcs
  across the numeral cell like a pull coming down. Decorative, aria-hidden,
  motion-gated.
- Hero becomes a VS screen: "DISCNW vs YOU" setup, the title in the stadium
  language, and the scroll hint becomes a blinking video-game prompt
  "PRESS ↓ TO PULL".

### What stays

- All 15 point sections, copy, stats, IDs, and the scroll-scoring JS mechanic.
- The halftime spirit-tunnel video and photo breaks.
- Point 15's orange flood (now reading as the stadium going full orange).
- Newsreader serif for reading columns (h2 headlines and paragraphs), chalk on
  dark. Space Grotesk stays for HUD labels and chrome.
- Double-gated motion (html.play + prefers-reduced-motion), no-JS settled base,
  print styles, skip link, focus styles, the 2.5s settle failsafe.

### Accessibility

- Chalk #FAFAF7 on night ground #0B120D: about 17:1.
- Orange #FF4A00 on the night ground: about 4:1, reserved for large digits,
  large text, and graphics; never small body text.
- Muted text becomes a light sage (about #A8B3A6, at least 7:1 on the ground).
- Focus outlines recolored for dark ground. ::selection updated.

## Scope

One file: `style-guide/08-game-to-15.html`, plus regenerating
`style-guide/previews/08-game-to-15-full.jpeg` (1440 wide) and
`style-guide/previews/scroll/08-game-to-15.jpeg` (620 wide downscale).
The appendix copy (thesis, palette chips, signature, choose/think-twice) must be
updated to describe the new direction. The Doto font joins the Google Fonts
request; total font families stay at three (Doto replaces nothing; Space
Grotesk and Newsreader stay).

## Success criteria

- The page reads instantly as a night game with a digital scoreboard: dark
  ground, LED digits, field lines, HUD chrome.
- The frisbee layer is legible: field diagram advances with the score; disc
  arcs on point reveals.
- Scroll scoring, flood, halftime reveal, and final flip all still work.
- Reduced-motion, no-JS, and print render a complete settled page.
- No em dashes or AI-slop tells in any rendered copy.
- Clean renders at 1440, 768, and 390 wide.
