# elfaheem.com

The landing page for the apex domain — the front door to the applications
running on the cluster. Static single document served by nginx.

**الفهيم** means *the clever one*, and that is the page's thesis: everything on
the domain is a test of it.

## The two audiences

The language toggle is not chrome, it changes what the page lists:

| Language | Applications shown |
|---|---|
| English (default) | Priset, GradeLoop, Guess My Number |
| العربية | + الفهيم, بساط الريح |

الفهيم and بساط الريح are built entirely in Arabic, so they appear only on the
Arabic side — silently, with no note on the English side advertising them. The
choice is remembered in `localStorage`; English is always the first thing a new
visitor sees.

Both languages ship in the markup and are toggled with CSS, so the page is
complete without JavaScript.

**The layout never mirrors.** Only Arabic text boxes get `direction: rtl`, via
`[lang="ar"]` — and `html[lang="ar"]` explicitly forces `direction: ltr` back,
because the root carries `lang="ar"` for screen readers and would otherwise
match that same rule and mirror the entire page. The header is identical
either way, and the Arabic copy is written natively rather than translated
phrase-by-phrase. Everything is centred, and the elements that change length
between languages carry a `min-height`, so toggling settles instead of jumping.

Each card carries its product's own brand colour, taken from the shared registry
in `bsatalrih-project/client/src/games-network.ts` — the chrome stays
achromatic so five different products never look like one. Priset is not in
that registry (it lists the Arabic-facing games, for بساط الريح's promo
slides); its `#35D38A` comes straight from Priset's own design tokens, where
green is the correct-guess colour.

## The seal

The mark is an octagon held around a rhombus.

The octagon is what two squares make when one of them is turned onto its
corner — the construction the whole of Islamic geometry starts from. The
rhombus at its centre is the **nuqta**, the single pressing of a reed pen that
every letter in Arabic script is proportioned against. So the mark reads as an
aperture with a point at its heart, which is as close as geometry gets to
saying *الفهيم*.

It is drawn once as an SVG `<symbol>` and used three times — masthead, hero,
colophon — with `fill: currentColor`, so each placement sets its own weight:
brass in the header, brass at 40% in the footer. Three subpaths under one
`fill-rule="evenodd"`: the octagon, the octagon again to open it into a ring,
the nuqta.

The rhombus is also the `.medallion` the section labels have always used as a
bullet, and the lattice behind the hero is that same rhombus opened out into a
grid. The bullet is not a shrunken logo, it is the logo's own unit — which is
why the mark was drawn around it rather than to replace it.

Two details worth keeping if the mark is ever redrawn:

- **The favicon is not the same path.** Its ring is wider. The in-page ring is
  3.3 units of 32, which is 1.6px at favicon size and closes up under
  antialiasing; the favicon version opens to 3.8 on a smaller octagon.
- **Rotating the mark 45° leaves the octagon where it was** and turns the
  nuqta from a square on its corner into a square. Both seals do it on hover,
  and the masthead one also spends a whole 45° on the page's scroll (see
  *Motion*) — the frame holds still while the point inside it turns. Every
  rotation in the page is a multiple of that angle for exactly this reason.

The `apple-touch-icon` is a 180px PNG inlined as a data URI, so the page stays
one self-contained document.

## Type

Three tiers, deliberately kept apart:

| Tier | Latin | Arabic | Used for |
|---|---|---|---|
| display | Fraunces | Reem Kufi | wordmark, hero, tagline, project titles |
| mono | IBM Plex Mono | — | hosts, stack lists, labels — anything metadata |
| body | IBM Plex Sans | IBM Plex Sans Arabic | prose |

The display tier used to be Geist, a grotesque, which read as the body face in
a heavier weight — three tiers that resolved to two. A serif against
IBM Plex's sans and mono separates them at a glance. Fraunces is variable on
optical size and `font-optical-sizing: auto` is on, so the hero gets the
high-contrast display cut and the 21px project titles get the sturdier text
cut, out of one file.

Arabic has its own display face rather than a bold of the body face. Reem Kufi
is Kufic — drawn on the same grid the seal is — so الفهيم and elfaheem read as
one identity rather than a name and its translation.

It is used only where a name is being *shown*: the hero and the project
titles. Everything else Arabic stays in IBM Plex Sans Arabic, which is far
easier to read at paragraph size, and Reem Kufi is condensed enough to stop
being comfortable below about 20px. That includes the section label — its
Latin side is mono, so it is metadata, not display, and the Arabic side should
match the tier rather than the language. Swapping the whole Arabic display
tier back is one value of `--display-ar`.

Fraunces has no Arabic glyphs, so `.card h2[lang="ar"]` restates
`var(--display-ar)` — `.card h2` outranks the generic `[lang="ar"]` rule on
specificity and would otherwise drop the Arabic titles to a system fallback.
Arabic titles are set at 1.4375rem against the Latin's 1.3125rem, because
Arabic needs the extra size to match optically, and their leading is cut to
1.37 so both languages land on a line box of exactly 1.97rem — a one-line
title sits in the same place either way.

The tagline takes the display face too — it is the brand's one line, not body
copy. Fraunces is *narrower* than the sans it replaced (22.2em against 26.7em
for the same sentence), which paid for the size increase. Its Arabic stays in
IBM Plex Sans Arabic: Reem Kufi is a name-setter, and a whole sentence of it is
work to read.

**Measure Fraunces at the size it is actually set at.** It is variable on
optical size and the text cut is ~22% wider than the display cut — "hand" is
2.42em at the clamp's 16px floor and 2.37em at its 21px ceiling, and the whole
of "by hand" swings from 3.03em at 100px to 3.71em at 17px. Measuring at the
wrong size is what once clipped the reveal to "by han"; the box takes the
floor's measure, because that is the widest the word ever gets.

Two rules apply to Arabic everywhere: it is never letter-spaced, because
tracking pulls the letters out of their joins, and its prose carries more
leading (1.85) than the Latin beside it.

## The previews

Every card shows its game's actual mechanic as an animated CSS illustration:
Priset's guesses closing on a price, GradeLoop's rubric filling in, the X/O
marks resolving a guess, a Fahim question tile flipping, a carpet drifting over
a board. No screenshots — the real apps sit behind auth walls or need a GPU — and
no text inside the artwork, so the same illustration works in both languages.

Priset's is the one preview that colours outside `--brand`: its bars run the
game's own feedback ladder (red → orange → yellow → green), which *is* the
product's signature, and the green it lands on is the card's accent anyway.
Its car is sized in percentages of the tile rather than pixels, so it keeps
its proportions instead of outgrowing the frame on a 320px phone.

The animations are `animation-play-state: paused` until an IntersectionObserver
adds `.play`, and hover restarts them, so nothing runs off-screen.

## The cover

The first screen is the brand and nothing else: the seal at full size, the two
wordmarks, one line about what the domain is, and the word **Projects** sitting
on the fold with a scroll cue under it. The work is behind the door, not on it
— the only way to the cards is to scroll.

`.cover` is `calc(100svh - var(--masthead))`, where `--masthead` is the header's
measured height (83px) taken back out so the section ends exactly at the fold.
`svh` rather than `vh`, so a mobile URL bar does not push the label under.
**If the header ever changes height, `--masthead` has to change with it**, or
the label slips out of the first screen — which is the one thing this layout
exists to prevent. When the viewport is too short for the content, content
wins and the section grows; the label stays visible either way.

**The mark is one mark, and it is still.** It used to arrive from −45° on load,
shrink on scroll, and carry a much larger faded copy of itself turning and
breathing behind it. All of that is gone. The only thing it does now is answer
the cursor: hovering turns it 45°, so the octagon frame holds still while the
nuqta goes from a diamond to a square, and a brass drop-shadow lights behind
it. It is also the one hero element deliberately left out of the scroll
animation — it holds its size and simply scrolls off with the page.

A warning worth keeping from having broken the version before this one: a
stray `*/` left above the old halo's `@keyframes orbit` shipped in 1.9.0 and
silently killed it. CSS error recovery consumes the malformed rule *and the
next block*, so the keyframes vanished while the `animation` declaration
referencing them stayed valid and simply matched nothing — no error, no visible
breakage in a screenshot, and brace-balance checks still pass.
`document.getAnimations()` is what catches it: the count of animations with
that name drops to zero. The build check now counts `/*` against `*/` as well.

The name arrives a letter at a time, out of focus and slightly under the line.
Each letter is its own `inline-block`, which costs the pair kerning between
them; at 5.5rem with a 60ms stagger, worth it.

**The mark sits midway between the masthead and the wordmark, and the offset
that puts it there is measured, not written.** The hero centres its block, so
the space above the mark is whatever is left over — which depends on the seal,
the wordmark and the tagline, all clamping against the viewport's *width*, and
on the height of the window. Across ordinary screens the offset needed ranges
from 1px to 144px with no constant relating them, so `centreMark()` reads the
two gaps and sets `--nudge`.

It is applied with `translate`, so it moves the mark without moving anything
else: the wordmark and the tagline stay exactly where the centred block puts
them. `transform` is taken by the load-in entrance and `scale` by the scroll
animation; all three compose.

The measurement uses `offsetTop`, not `getBoundingClientRect()` — a layout
position, which ignores those same three transforms. Measuring the painted box
would feed the mark's own offset back into itself.

Without JavaScript `--nudge` stays `0` and the mark sits where the centred
block puts it. Verified centred within 2px from 500×473 to 1920×1400.

Behind it: two lights rather than one — a tight brass glow behind the mark and
a much wider, dimmer one under it — over a lattice at two scales in a 1:3 ratio.
Both lattice gradients are centred, so the coarse diamonds sit on the fine ones
instead of drifting against them.

**Every gradient there is transparent well before the 68rem column ends.** They
are not full-bleed, and the reason is worth keeping: breaking them out with
`width: 100vw` works visually but `100vw` counts the scrollbar, so the page
picks up exactly 8px of sideways scroll — and `overflow-x: clip` removes it on
neither the body nor the root. Escaping the shell properly means restructuring
it around every section, which is not worth a background. Left inside the
column without the fade, the texture ends dead at the edge and draws two hard
vertical seams down any wide page.

## The reveal

"…built by **hand**" is only half true, so the hand rolls over to Anthropic's
own mark — the thing that actually did the typing. A logo rather than a name,
so it needs no translation and both languages land on the same glyph. **Only
the hand turns over**; "by" stays put, so the sentence reads *built by ▲* and
the mark takes the place of the hand itself rather than of the phrase. The word
is brass with a dashed underline that breathes: the only thing on the page
saying a word is a door.

Word and mark ship stacked in a column inside a box one line tall; hovering
slides the column up by exactly one line and resizes the box at the same time —
inward here, since a mark is narrower than the word it replaces — so the
sentence closes up around it rather than leaving a hole. Widths are the words'
real measures in ems, which hold at every step of the tagline's `clamp()`.

**The tagline has to hold one line on any pointer-sized screen**, and its
`max-width` is what buys that: 36rem missed the English sentence at the top of
the font-size clamp by four pixels, which cost it a second line above 1050px,
so it is 37rem. A one-line sentence can only get shorter when the box closes,
so it can never re-wrap. At two lines it snaps back to one under the cursor and
throws the whole hero — worse, the sentence leaves the pointer, the hover
drops, the box reopens, and the line flickers between one and two for as long
as the cursor sits there. Verified stable from 500px to 1920px in
both languages, and worth re-checking if the copy grows. Below 34rem the
sentence needs two lines whatever the face, so there the box simply stops
resizing and only its contents roll: a little air after the mark, on the widths
that have no cursor anyway.

Three things it would get wrong if left to the defaults:

- The column needs `align-self: flex-start`, or the row flex stretches it to
  the one-line window and a `-50%` shift moves by half a word.
- The words need `align-items: flex-start`, or both take the width of the
  longer one and the hero's centred text pushes the shorter word out of frame.
- Cross-start follows the script, which is why the Arabic pair hugs the right
  edge without a second rule.

The mark is `aria-hidden`, so the sentence a screen reader reads is still a
sentence. `tabindex="0"` and `:focus` make it work without a mouse. Anthropic's
artwork is used as fetched rather than redrawn, and keeps its own colour — it
is a credit, not decoration.

## Motion

Two kinds, kept apart.

**On a clock.** The crest turns into place on load, the name arrives a letter at
a time, and the previews play their mechanic when they scroll into view.
One-shot: they say hello and stop. One exception loops forever — the nuqta
falling down the scroll cue — and it is transform-only.

**On the scrollbar.** Everything inside `@supports (animation-timeline: scroll())`
is bound to scroll position instead of a duration, so it runs backwards as
readily as forwards — the reader is holding the handle the whole time.

- **The masthead seal is the document's progress dial** — one slow 45° turn
  from top to bottom. An octagon maps onto itself every 45°, so it lands back
  exactly where it started: the frame arrives unmoved and only the nuqta has
  changed, from a square on its corner into a square. In between it is
  genuinely turning, which is the point — it is the only thing on the page
  telling you how far down you are.
- **The hero leaves in four pieces at four rates, and recedes as well as
  rises** — the name climbs while scaling down, the Arabic name drags behind
  it, the tagline is gone before either, and the lattice sinks and grows the
  other way. Depth, rather than everything sliding at once. The mark is not in
  this list: it holds still and scrolls off with the page. Scroll back up and
  the rest reassembles exactly.
- **The cards are on a `view()` timeline** rather than rising once and going
  quiet, so they keep answering the scrollbar. They arrive tilted away from the
  reader and settle flat — the grid carries the `perspective`, which is the one
  place on the page with any depth.

The card animation sits on the `li`, not on the card: an animation holding
`transform` outranks `.card:hover`'s lift, and the two have to stay out of each
other's way. That is why `--brand` and `--i` live on the list item now. The
stagger is spent on where each card's range *starts* — a scroll timeline has no
clock to delay against.

Two failure modes are handled rather than hoped about:

| | |
|---|---|
| No support for scroll timelines | The whole block is behind `@supports`; the one-shot animations are what runs. |
| Nothing to scroll | A window taller than the document makes the timelines inactive, and an inactive timeline drops its fill — so every card sits at its normal style instead of at `opacity: 0`. Worth checking after any change: the alternative is an invisible page. |

The cue fades out over the first 18vh — it has done its job the moment anyone
takes it up.

Reduced motion turns these off by name. The blanket rule cuts
`animation-duration`, and a scroll timeline has no duration to cut. The one
infinite animation needs naming there too: clipping it to a single 0.01ms pass
parks the falling nuqta mid-fall, so it is stopped outright and put back where
it belongs.

## Layout

```
index.html          the whole page — inline CSS and JS, no build step
nginx.conf          gzip, cache and security headers (all at server level)
Dockerfile          nginx:1.27-alpine + the two files above
k8s/                namespace, deployment, service
```

## Deploy

```bash
docker build -t ghcr.io/wkn00/elfaheem-landing:1.10.1 .
docker push ghcr.io/wkn00/elfaheem-landing:1.10.1

kubectl apply -f k8s/namespace.yaml
kubectl apply -f k8s/deployment.yaml
```

Bump the tag in `k8s/deployment.yaml` for each change — `imagePullPolicy` is
`IfNotPresent`, so reusing a tag will not pull.

The namespace needs the registry secret once:

```bash
kubectl create secret docker-registry ghcr-creds -n elfaheem \
  --docker-server=ghcr.io --docker-username=wkn00 --docker-password=<PAT>
```

### Scheduling

Two replicas, kept off the control plane (`wk`) by a weighted node affinity for
`wk1`/`wk2`, and spread one-per-node by pod anti-affinity. Both are
*preferred*, not required: the page stays schedulable if a worker is gone.
Make the node affinity `requiredDuringScheduling…` if `wk` should be excluded
outright, at the cost of that fallback.

### Routing

The tunnel is token-managed, so its routes live in the Cloudflare dashboard,
not in this repo. `elfaheem.com` needs one public hostname:

```
elfaheem.com  →  http://elfaheem-frontend.elfaheem.svc.cluster.local:80
```
