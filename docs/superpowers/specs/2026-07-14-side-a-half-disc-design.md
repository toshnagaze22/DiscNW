# Side A (Direction 23): half-vinyl, half-disc hero redesign

Date: 2026-07-14
Status: approved by Matt (chose "half-vinyl, half-disc morph" over white-pressing and fix-in-place options; approved vertical split, disc left / vinyl right)

## Problem

The current hero turntable in `style-guide/23-side-a.html` has visible defects and
reads as pure music with no frisbee in the visual:

1. The tonearm renders broken: the pivot base is clipped off the top edge of the
   deck canvas, the arm tube points off-canvas, and the headshell renders as a
   white donut sitting on the record edge.
2. The bottom arc of the curved label text ("DISCNW RECORDINGS · SEATTLE")
   renders upside down.
3. The record is black-on-black against the dark room and goes muddy.
4. The disc/frisbee pun exists only in the copy, never in the visual.

## Design

### The hero object

One circle, split by a crisp vertical seam down the middle, marked with a thin
brass hairline so it reads as deliberate design rather than a glitch.

- **Left half: bone-white ultimate disc.** Smooth white dome with subtle
  shading, concentric flight rings pressed near the center (UltraStar-style),
  and a slightly raised rim catching light at the edge. Faces the title side.
- **Right half: black vinyl.** The existing groove texture, brightened so it
  stops going muddy, with the rotating reflection sweep carrying the spin.
- **Shared center label.** A record label and a disc's printed center graphic
  are nearly the same object, so the oxblood label with the DiscNW mark spans
  both halves untouched. It is the hinge of the pun.
- **Spin without strobe.** The halves stay fixed; a half-black half-white
  circle spinning would flicker. Each half is a static clip window
  (`clip-path: inset`) with the full-circle texture spinning inside it, so
  grooves and sheen rotate while the split holds still.

### Tonearm rebuild

Correct geometry: pivot base fully inside the canvas at top right, counterweight
behind it, arm tube angling down-left to land a small rectangular headshell and
stylus on the outer grooves of the vinyl half only.

### Supporting fixes and frisbee cues

- Reverse the bottom SVG text path so the label's lower arc reads upright like
  a seal.
- Spec chips gain the disc's stat next to the record's: 175 g alongside
  33 1/3 RPM.
- The mini-disc in the homepage mock section gets the same half-and-half
  treatment so the signature carries through.
- Copy touch-ups where the visual now makes the argument: hero lede, appendix
  "Signature" item, the deck aria-label, and the meta description all describe
  the morph.
- Copy follows the house voice: no em dashes, plainspoken, concrete.

### Guarantees preserved

- Reduced motion / no JS / print show the settled still object with both
  halves and the tonearm down.
- Photographs stay clean; analog texture lives only on chrome.
- Side A / Side B toggle keeps working (label color flip must still work with
  the shared center label).

## Scope

One file: `style-guide/23-side-a.html` (self-contained HTML/CSS/JS mock).
The hub tile preview image under `style-guide/previews/`, if one exists for
direction 23, should be regenerated after the change.

## Success criteria

- The hero object reads as both a record and an ultimate disc at first glance.
- No clipped or floating tonearm parts at common viewport widths (360, 768,
  1440).
- Curved label text reads upright top and bottom.
- Reduced-motion and no-JS renders are complete and settled.
