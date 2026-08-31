# Tashvi Collective — site guide

Single-page static site for Tashvi Collective (tashvicollective on Instagram): one
self-contained [index.html](index.html) (all CSS inline, no build step, no JS) plus an
`images/` folder. Preview via `.claude/launch.json` ("trinket-site" — `python3 -m
http.server 4173`); don't open index.html as a file, relative image paths need the server.

## Conventions

- Fonts: Karla (body/UI), Libre Baskerville (`var(--serif)`, display headings), Cinzel
  (header wordmark only).
- Colors: always use the CSS variables (`--bg`, `--card`, `--ink`, `--muted`, `--border`).
  Never hardcode colors in new markup — the variables carry the pure-black dark mode
  (`prefers-color-scheme`). The hero is the one exception (white text over the image).
- Sharp corners everywhere (no border-radius), no hover effects on cards.
- British English in product copy ("colourful", "organising").

## Images

- Product photos live in `images/`, kebab-case names (`tote-l.jpg`, `bath-robe.jpg`).
- Resize before adding: max 900px on the long edge, JPEG quality ~78
  (`sips -Z 900 -s format jpeg -s formatOptions 78 in.jpg --out images/name.jpg`).
- Cards display images square-cropped (`aspect-ratio: 1/1`, `object-fit: cover`,
  center crop) — check portrait shots still frame the product well.
- If a photo renders sideways, rotate the file itself: `sips -r 90 images/name.jpg`.
- `images/hero.png` is the hero background — it is pre-blurred artwork; to change the
  hero, replace that file (no HTML edit needed). The CSS adds parallax and a scroll-driven
  hue rotation on top.
- `images/logo.svg` is the full logo lockup (elephant + wordmark). The header uses an
  inline copy of the elephant paths with `currentColor` so it follows the nav's
  white-to-ink scroll animation — edit both if the mark changes.

## Adding a product

Add an `<article>` inside the `.grid` of the right section, following the existing
pattern exactly:

```html
    <article class="product">
      <img src="images/new-product.jpg" alt="New Product" loading="lazy">
      <div class="info">
        <h2>New Product</h2>
        <p class="desc">One or two sentences. Same tone as the others — warm,
        practical, no hard sell.</p>
      </div>
    </article>
```

Current sections and their `id`s:

| Section | id | Contents |
|---|---|---|
| Bags & Accessories | `bags` | totes, slings, sleeves, pouches, charms |
| Bath & Home | `home` | robes, towels, table linen |
| Clothing | `clothing` | dresses, shirts, tops |
| Handicrafts | `handicrafts` | artisanal décor pieces |

## Adding a section

1. Add before `</main>` (or between existing sections):

```html
    <section class="collection" id="new-id">
      <div class="section-head">
        <h2>Section Name</h2>
        <p>One-line intro in the same voice as the others.</p>
      </div>
      <div class="grid">
        <!-- product articles -->
      </div>
    </section>
```

2. Add a matching link to the header nav (`.nav-links`) **and** the footer "Explore"
   column — both use `href="#new-id"`. The `id` gets smooth scrolling and a
   `scroll-margin-top` offset for the fixed header automatically via `.collection`.

## Things that look like bugs but aren't

- The header is transparent with white contents at the top of the page and frosts to
  90%-opacity glass on scroll — that's a CSS scroll-driven animation
  (`animation-timeline: scroll()`) with a static solid fallback in browsers without
  support. The hero image's parallax + hue shift live in the same `@supports` block.
- `.hero-img` is deliberately 124% tall / offset -12% so the parallax never exposes an
  edge. It must keep explicit `width`/`height` — replaced elements don't stretch to
  insets like divs do.
- `text-box: trim-both` on hero text and `text-wrap: balance`/`pretty` are progressive
  enhancements; older browsers just ignore them.
