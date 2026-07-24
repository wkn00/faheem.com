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

## Type

Three tiers, deliberately kept apart:

| Tier | Face | Used for |
|---|---|---|
| display | Geist | header logo, hero wordmark, project titles |
| mono | IBM Plex Mono | hosts, stack lists, section labels — anything metadata |
| body | IBM Plex Sans / IBM Plex Sans Arabic | prose |

Geist has no Arabic glyphs, so `.card h2[lang="ar"]` restates
`var(--arabic)` — `.card h2` outranks the generic `[lang="ar"]` rule on
specificity and would otherwise drop the Arabic titles to a system fallback.

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

## Layout

```
index.html          the whole page — inline CSS and JS, no build step
nginx.conf          gzip, cache and security headers (all at server level)
Dockerfile          nginx:1.27-alpine + the two files above
k8s/                namespace, deployment, service
```

## Deploy

```bash
docker build -t ghcr.io/wkn00/elfaheem-landing:1.4.1 .
docker push ghcr.io/wkn00/elfaheem-landing:1.4.1

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
