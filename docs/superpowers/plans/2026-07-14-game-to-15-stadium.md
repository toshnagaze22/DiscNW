# Game to 15 Stadium Night Game Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Re-skin `style-guide/08-game-to-15.html` from light editorial into a dark stadium night game with an LED dot-matrix scoreboard, scoreboard-digit numerals, and a frisbee field HUD, keeping all 15 points, copy, and the scroll-scoring mechanics.

**Architecture:** Single self-contained HTML mock (inline CSS + inline JS). The re-skin is a palette/typography/chrome transformation plus two decorative additions (field-position SVGs, pull-disc arcs) inserted mechanically with a Python script. The existing scroll-scoring JS is untouched; the momentum meter's new segmented look reads the same `--mo` custom property via `clip-path` instead of `transform`.

**Tech Stack:** Plain HTML/CSS/JS. Google Fonts adds "Doto" (dot-matrix, verified serving). Verification via Playwright screenshots against `python3 -m http.server 8931` in `style-guide/`.

## Global Constraints

- Spec: `docs/superpowers/specs/2026-07-14-game-to-15-stadium-design.md`.
- Only `style-guide/08-game-to-15.html` and the two previews (`previews/08-game-to-15-full.jpeg` 1440w, `previews/scroll/08-game-to-15.jpeg` 620w) change.
- Display numbering stays "07" everywhere it appears (hub card shows idx 07 for this file; file numbering intentionally differs).
- The scroll-scoring JS block is NOT modified.
- Photos/video render clean: no tint or filter beyond the existing bottom scrim (max .6) and the existing break-img contrast/saturate filter.
- Double-gated motion (html.play AND prefers-reduced-motion: no-preference); base styles are the settled page for no-JS/print/reduced-motion. The 1.2s/2.5s settle failsafe stays.
- Copy voice: no em dashes in rendered copy, plainspoken, concrete.
- Contrast on the night ground: chalk #F4F2E6 for text (~16:1), sage #A9B5A4 for muted (~8:1), orange #FF4A00 only for large digits/graphics/large text.
- Dev server: `cd style-guide && python3 -m http.server 8931`.

---

### Task 1: Night world and the LED scoreboard

**Files:**
- Modify: `style-guide/08-game-to-15.html` (fonts link; `:root`; base body/link/selection/focus rules; the whole `.strip` block; print block)

**Interfaces:**
- Produces: palette tokens `--night`, `--night-2`, `--chalk`, `--sage`, redefined `--indigo:#8FA1EC`, `--tidal:#3FC1D4`; the class hook `.s-dot`; Doto loaded as `font-family:"Doto"`. Tasks 2-3 rely on these tokens and on Doto.
- Consumes: nothing.

- [ ] **Step 1: Add Doto to the fonts link**

Replace the Google Fonts stylesheet href with:

```html
<link href="https://fonts.googleapis.com/css2?family=Doto:wght@500..900&family=Newsreader:ital,opsz,wght@0,6..72,400..700;1,6..72,400..600&family=Space+Grotesk:wght@300..700&display=swap" rel="stylesheet">
```

- [ ] **Step 2: Rework the palette tokens**

In `:root`, replace the first block of custom properties with:

```css
--night:#0B120D;                          /* the field at night — page ground */
--night-2:#070C08;                        /* the board + deep chrome */
--chalk:#F4F2E6;                          /* field lines + primary text */
--sage:#A9B5A4;                           /* muted text on the night ground */
--paper:#FAFAF7;                          /* button text + print ground */
--ink:#101010;
--accent:#FF4A00;                         /* Universe Orange — the LED + the flood */
--indigo:#8FA1EC;                         /* indigo LED — lightened for the dark ground */
--tidal:#3FC1D4;                          /* tidal LED — lightened for the dark ground */
--marigold:#FFB61E;                       /* amber LED — decorative bursts, never text */
--pt:var(--accent);
--muted:var(--sage);
--hair:rgba(244,242,230,.16);
--hair-strong:rgba(244,242,230,.34);
--led:"Doto",var(--grot);
```

Keep every other token (`--strip-h`, `--pad`, `--grot`, `--serif`, easings) as is, but set `--strip-h:56px`.

- [ ] **Step 3: Re-ground the base styles**

- `body{background:var(--night);color:var(--chalk);...}` (replace paper/ink).
- `::selection{background:var(--accent);color:var(--night)}`.
- `a{color:var(--chalk);...}` and `a:hover{text-decoration-color:var(--chalk)}`.
- `.skip{background:var(--chalk);color:var(--night);...}`.

- [ ] **Step 4: The strip becomes an LED board**

Replace the strip CSS declarations:

```css
.strip{... background:var(--night-2); border-bottom:1px solid var(--hair-strong);}
.strip-in{... color:var(--chalk);}  /* add color */
.s-box{
  position:relative;display:inline-flex;gap:.45em;align-items:baseline;
  font-family:var(--led);font-weight:900;font-size:17px;letter-spacing:.08em;
  color:var(--accent);text-shadow:0 0 10px rgba(255,74,0,.6),0 0 26px rgba(255,74,0,.28);
  font-variant-numeric:tabular-nums;
}
.s-dash{color:var(--sage);text-shadow:none}
.s-sep{color:var(--sage)}
.s-meta{color:var(--sage);font-weight:500}
.s-final{font-family:var(--led);font-weight:900;font-size:17px;letter-spacing:.1em;color:var(--ink)}
.s-plus{... color:var(--marigold); (keep everything else)}
```

Swap the strip crest default (white mark on the dark board; dark mark returns on the orange final board):

```css
.strip-crest .crest-dark{display:none}
.strip-crest .crest-white{display:block}
html.play .strip.final .crest-white{display:none}
html.play .strip.final .crest-dark{display:block}
```

(Delete the two old `html.play .strip.final .crest-*` lines that did the reverse.)

Replace the strip's `.s-bug` HTML content: `<span class="s-bug"><span class="s-dot" aria-hidden="true"></span>Live</span>` (the `.eq` bars stay only in the video's `.hl-bug`). Add CSS:

```css
.s-bug{display:inline-flex;align-items:center;gap:7px;font-weight:600;letter-spacing:.16em;color:var(--chalk)}
.s-dot{width:8px;height:8px;border-radius:50%;background:var(--accent);box-shadow:0 0 8px var(--accent)}
@media (prefers-reduced-motion: no-preference){
  html.play .s-dot{animation:blinkdot 1.2s steps(2,jump-none) infinite}
  @keyframes blinkdot{50%{opacity:.25}}
}
```

Momentum meter goes segmented (JS still sets `--mo` on the strip; do not touch JS):

```css
.s-momentum{
  position:absolute;left:0;bottom:0;height:5px;width:100%;display:none;
  background:repeating-linear-gradient(90deg,var(--accent) 0 calc(100%/15 - 3px),transparent calc(100%/15 - 3px) calc(100%/15));
  clip-path:inset(0 calc((1 - var(--mo,0)) * 100%) 0 0);
}
```

and in the motion block replace `html.play .s-momentum{transition:transform .55s var(--ease-lux)}` with `html.play .s-momentum{transition:clip-path .55s var(--ease-lux)}`.

- [ ] **Step 5: Print goes white**

In `@media print`, add `body{background:#fff;color:var(--ink)}`, change the strip rule to `background:#fff!important;border-bottom-color:var(--ink)`, add `.s-dot` to the existing `.s-momentum,.burst,.s-bug,.s-plus{display:none!important}` list (s-bug already covers the dot; adding is belt and braces), and add `a{color:var(--ink)}`.

- [ ] **Step 6: Verify**

Serve and screenshot the top of the page at 1440x900 after ~2.6s. Expected: dark page, black board strip with white crest, chalk labels, glowing orange 0 dot-matrix score, blinking dot next to LIVE. Scroll to point 3; expected score 0-3 in dot-matrix and a segmented orange bar under the strip filled 3 of 15 blocks. Note: the rest of the page will look half-converted (light-styled sections on dark ground) until Tasks 2-4; only judge the strip and ground.

- [ ] **Step 7: Commit**

```bash
cd /Users/toshihironagase/Desktop/DiscNW
git add style-guide/08-game-to-15.html
git commit -m "Game to 15: night ground and LED dot-matrix scoreboard"
```

---

### Task 2: Hero VS screen, floodlights, and the point sections

**Files:**
- Modify: `style-guide/08-game-to-15.html` (hero CSS + HTML; `.num`/`.numcell` CSS; tagrow/kicker/col/quote/hotline/prog/about/footer colors; sideline hairlines)

**Interfaces:**
- Consumes: Task 1 tokens (`--night`, `--chalk`, `--sage`, `--led`).
- Produces: hero classes `.vs-row`, `.blink` used by Task 4's copy checks; chalk-on-dark section chrome Task 3 draws onto.

- [ ] **Step 1: Floodlights and the VS row**

`.hero` gains floodlight pools:

```css
.hero{ (keep layout lines) background:
  radial-gradient(46% 40% at 16% -6%, rgba(255,243,209,.14), transparent 62%),
  radial-gradient(46% 40% at 84% -6%, rgba(255,243,209,.14), transparent 62%);}
```

After the `</h1>` line insert:

```html
<p class="vs-row" aria-label="DiscNW versus you"><span>DiscNW</span><b class="vs">VS</b><span>You</span></p>
```

```css
.vs-row{display:flex;align-items:baseline;gap:18px;margin:clamp(18px,3vh,28px) 0 0;
  font-family:var(--led);font-weight:900;font-size:clamp(18px,2.2vw,26px);
  letter-spacing:.14em;text-transform:uppercase;color:var(--chalk)}
.vs-row .vs{color:var(--accent);font-size:.78em;text-shadow:0 0 12px rgba(255,74,0,.55)}
```

Add `.vs-row` to the hero entrance rules (same pattern as `.standfirst`, delay .5s) in both the `html.play` pre-state list and the `html.go` settled list, to the reduced-motion force-settle list, and to the print force-settle list.

The scroll hint becomes a game prompt. Replace the `.pull-hint` line with:

```html
<p class="pull-hint"><span class="blink">Press ↓ to pull</span></p>
```

```css
@media (prefers-reduced-motion: no-preference){
  html.play .blink{animation:blinkhint 1.3s steps(2,jump-none) infinite}
  @keyframes blinkhint{50%{opacity:.3}}
}
```

- [ ] **Step 2: The numerals become LED digits**

Replace `.num` and `.num::before`:

```css
.num{
  font-family:var(--led);font-weight:900;
  font-size:clamp(170px,17vw + 100px,290px);
  line-height:.8;letter-spacing:0;color:var(--pt);
  text-shadow:0 0 18px color-mix(in srgb, var(--pt) 50%, transparent),
              0 0 64px color-mix(in srgb, var(--pt) 26%, transparent);
  font-variant-numeric:tabular-nums;margin-top:0;user-select:none;position:relative;z-index:0;
}
.num::before{
  content:attr(data-n);position:absolute;left:0;top:0;z-index:-1;
  color:var(--pt);opacity:.35;filter:blur(14px);transform:none;
}
```

Point 15 on the flood keeps ink digits with a white afterglow:

```css
.pt-15 .num{color:var(--ink);text-shadow:0 0 18px rgba(255,255,255,.45),0 0 64px rgba(255,255,255,.22)}
.pt-15 .num::before{color:#fff;opacity:.5}
```

(Delete the old `.pt-15 .num::before` rule it replaces.)

- [ ] **Step 3: Chalk chrome on the sections**

- `.kicker{color:var(--chalk)}`; `.tagrow` keeps `border-top:1px solid var(--pt)`; its label spans inherit chalk.
- `.run{color:var(--sage)}` (keep tabular settings).
- `.col h2` add `color:var(--chalk)`; `.col p` inherits chalk; `.pt-quote figcaption{color:var(--sage)}` (it already uses --muted, which now maps to sage; verify).
- Sidelines: `.pt:not(.pt-15) > .wrap{box-shadow:inset 1px 0 0 var(--hair),inset -1px 0 0 var(--hair)}`.
- `.prog{background:#fff;border-color:var(--hair-strong)}` stays a white broadcast card.
- `.break figcaption{color:var(--sage)}` (via --muted; verify).
- `.about{border-top-color:var(--chalk)}`, `.spec > div{border-top-color:var(--hair)}`, swatches `border:1px solid var(--hair-strong)`.
- `.pagefoot{border-top-color:var(--chalk)}`.
- `.pt-15` flood section: `color:var(--ink)` already; `.btn{background:var(--ink);color:var(--paper)}` unchanged; check `.fine` and tagrow read ink on orange (existing rules).
- Hotline: `h2.hotline a{color:var(--accent)}` stays (80px text, large-scale exemption).

- [ ] **Step 4: Verify**

Screenshots: hero (expect floodlight pools, chalk GAME TO 15, dot-matrix DISCNW VS YOU, blinking Press ↓ to pull), point 3 (expect giant glowing tidal dot-matrix 03, chalk serif column, sidelines), point 15 (expect orange flood, ink dot-matrix 15, GAME., button legible).

- [ ] **Step 5: Commit**

```bash
git add style-guide/08-game-to-15.html
git commit -m "Game to 15: VS-screen hero, floodlights, LED numerals, chalk chrome"
```

---

### Task 3: The frisbee layer

**Files:**
- Modify: `style-guide/08-game-to-15.html` (15 run spans, 15 numcells via script; new CSS)

**Interfaces:**
- Consumes: `--pt`, `--hair`, `--hair-strong`, `--chalk` tokens; `.pt.in` reveal class from existing JS.
- Produces: `.fieldpos`, `.fp-*`, `.pull-disc` (all decorative, aria-hidden).

- [ ] **Step 1: Insert the field-position SVGs and pull discs mechanically**

Save and run this script (from the repo root):

```python
import re
p = 'style-guide/08-game-to-15.html'
s = open(p).read()

SVG = ('<svg class="fieldpos" viewBox="0 0 120 28" aria-hidden="true" focusable="false">'
       '<rect class="fp-f" x="1" y="1" width="118" height="26"/>'
       '<line class="fp-l" x1="19" y1="1" x2="19" y2="27"/>'
       '<line class="fp-l" x1="101" y1="1" x2="101" y2="27"/>'
       '<line class="fp-m" x1="60" y1="1" x2="60" y2="27"/>'
       '<circle class="fp-d" cx="{x}" cy="14" r="4"/></svg>')

def field_x(n):
    x = 19 + n * (101 - 19) / 15.0
    if n == 15: x += 6            # game point lands inside the end zone
    return round(x, 1)

def run_repl(m):
    n = int(m.group(2))
    return m.group(1) + SVG.format(x=field_x(n)) + m.group(3) + m.group(4)

s, c1 = re.subn(r'(<span class="run" aria-hidden="true">)(?:0 – (\d+))((?: · Game)?)(</span>)',
                lambda m: m.group(1) + SVG.format(x=field_x(int(m.group(2)))) + '0 – ' + m.group(2) + (m.group(3) or '') + m.group(4),
                s)
assert c1 == 15, f'expected 15 run spans, got {c1}'

s, c2 = re.subn(r'<span class="burst"></span>',
                '<span class="burst"></span><span class="pull-disc" aria-hidden="true"></span>', s)
assert c2 == 15, f'expected 15 numcells, got {c2}'

open(p, 'w').write(s)
print('ok', c1, c2)
```

Expected output: `ok 15 15`.

- [ ] **Step 2: Style the HUD and the pull disc**

```css
/* field-position HUD — a chalk field, the disc advancing to the end zone */
.fieldpos{width:78px;height:auto;display:inline-block;vertical-align:-5px;margin-right:10px}
.fp-f{fill:none;stroke:var(--hair-strong)}
.fp-l{stroke:var(--hair-strong)}
.fp-m{stroke:var(--hair)}
.fp-d{fill:var(--pt)}
.pt-15 .fp-d{fill:var(--ink)}
.pt-15 .fp-f,.pt-15 .fp-l{stroke:rgba(16,16,16,.45)}
.pt-15 .fp-m{stroke:rgba(16,16,16,.25)}

/* the pull — a chalk disc arcs across the numeral when the point reveals */
.pull-disc{display:none}
@media (prefers-reduced-motion: no-preference){
  html.play .pull-disc{display:block;position:absolute;left:-6%;top:16%;width:26px;height:26px;z-index:3;pointer-events:none;opacity:0}
  html.play .pull-disc::before{content:"";display:block;width:100%;height:100%;border-radius:50%;
    background:radial-gradient(circle at 38% 32%, #fff, var(--chalk) 55%, #C9C6B4 100%);
    box-shadow:inset 0 0 0 3px rgba(16,16,16,.18), 0 2px 6px rgba(0,0,0,.5)}
  html.play .pt.in .pull-disc{animation:pullx 1.05s var(--ease-lux) .12s both}
  html.play .pt.in .pull-disc::before{animation:pully 1.05s var(--ease-lux) .12s both}
  @keyframes pullx{0%{opacity:0;transform:translateX(0)}10%{opacity:1}86%{opacity:1}100%{opacity:0;transform:translateX(min(30vw,420px))}}
  @keyframes pully{0%{transform:translateY(0) rotate(0deg)}50%{transform:translateY(-74px) rotate(180deg)}100%{transform:translateY(10px) rotate(360deg)}}
  html.play #pt-01 .pull-disc{display:none}
}
```

Add `.pull-disc` to the print kill list alongside `.burst`.

- [ ] **Step 3: Verify**

Reload, screenshot point 5's tagrow zoomed (expect the mini chalk field with the disc dot 5/15 up the field, then 0 – 5) and catch a reveal mid-animation if possible. Confirm `document.querySelectorAll('.fieldpos').length === 15` and pt-15's disc sits inside the end zone box.

- [ ] **Step 4: Commit**

```bash
git add style-guide/08-game-to-15.html
git commit -m "Game to 15: field-position HUD and pull-disc reveals"
```

---

### Task 4: Copy and appendix

**Files:**
- Modify: `style-guide/08-game-to-15.html` (meta description, appendix thesis/spec/dd items, palette chips)

**Interfaces:**
- Consumes: final visual language from Tasks 1-3.
- Produces: nothing downstream.

- [ ] **Step 1: Meta description**

```html
<meta name="description" content="Experimental direction 07 for the DiscNW redesign: the site is a night game of ultimate played to 15. An LED scoreboard scores your reading, fifteen glowing scoreboard numerals land point by point, a chalk field HUD tracks the disc to the end zone, and the stadium floods orange at game point.">
```

- [ ] **Step 2: Appendix thesis**

```html
<p class="thesis">The site is a night game played to 15, under the lights. A real LED scoreboard scores your reading as fifteen glowing dot-matrix numerals carry you from the first pull to a full-screen game point. A chalk field HUD tracks the disc up the field with every point, a pull arcs across each numeral as it lands, and a halftime highlight loop breaks in. Stadium scoreboard on the outside, storytelling on the inside.</p>
```

- [ ] **Step 3: Spec items**

Palette chips become:

```html
<span class="chip"><span class="swatch" style="background:#0B120D"></span>Night Field <code>#0B120D</code></span>
<span class="chip"><span class="swatch" style="background:#F4F2E6"></span>Chalk <code>#F4F2E6</code></span>
<span class="chip"><span class="swatch" style="background:#FF4A00"></span>Universe Orange <code>#FF4A00</code></span>
<span class="chip"><span class="swatch" style="background:#FFB61E"></span>Amber LED <code>#FFB61E</code></span>
<span class="chip"><span class="swatch" style="background:#3FC1D4"></span>Tidal LED <code>#3FC1D4</code></span>
```

Type dd: `Doto, a dot-matrix face, for every scoreboard digit and HUD readout. Space Grotesk for the chrome. Newsreader for the reading columns, chalk on the night ground.`

Signature dd: `The LED board: DISCNW 0 – 0 YOU in glowing dot-matrix with a blinking live dot and a fifteen-block momentum bar that fills as you read. Every numeral is a scoreboard digit in its own color, a chalk field diagram walks the disc to the end zone, and at game point the whole stadium floods orange.`

Choose-this-if dd: `You want the page people retell in one sentence: the site is a night game to 15. The scoreboard language is drawn straight from how the sport keeps score, and the video-game energy meets kids and players where they are.`

Think-twice dd: keep the current text unchanged.

- [ ] **Step 4: Verify and scan**

Reload, screenshot the appendix. Grep rendered copy for em dashes (allowed only inside `<script>`/CSS comments). Check the title/kicker still read direction 07.

- [ ] **Step 5: Commit**

```bash
git add style-guide/08-game-to-15.html
git commit -m "Game to 15: stadium copy for meta and appendix"
```

---

### Task 5: Verification sweep, previews, push

**Files:**
- Modify: `style-guide/previews/08-game-to-15-full.jpeg`, `style-guide/previews/scroll/08-game-to-15.jpeg`

- [ ] **Step 1: Three widths** 1440x900, 768x1000, 390x844: hero, a mid point, point 15. Expect: no clipped HUD, digits scale down cleanly, strip fits at 390 (s-meta/s-sep/s-bug already hide under 480px).

- [ ] **Step 2: Mechanics** Normal motion at 1440: scroll to bottom, expect strip flips FINAL 15 · Game on orange with dark crest; scroll back to top, expect score returns to 0 and board un-flips. Reduced motion: expect static calm strip ("Game to 15"), settled sections, no animations, video paused on poster. Console: no errors.

- [ ] **Step 3: Previews** With reduced motion emulated at 1440 wide, full-page JPEG to `style-guide/previews/08-game-to-15-full.jpeg`, then `cp` + `sips --resampleWidth 620` to `style-guide/previews/scroll/08-game-to-15.jpeg`.

- [ ] **Step 4: Commit and push for production**

```bash
git add style-guide/previews/08-game-to-15-full.jpeg style-guide/previews/scroll/08-game-to-15.jpeg
git commit -m "Game to 15: regenerate preview tiles for the stadium re-skin"
git push origin main
```

(Also push the earlier Side A commits sitting on main; production deploys from main.)
