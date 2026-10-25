# Swathi-Bhaskar-Portfolio
# Swathi Bhaskar — Developer Portfolio

A single-page portfolio site. No framework, no build step, no third-party
requests at runtime: one HTML file plus local assets.

## Files

```
index.html                              the whole site (markup, styles, script)
assets/
  swathi-bhaskar.jpg                    portrait, 880×1100, 4:5
  swathi-bhaskar.webp                   same image, WebP (served first)
  Swathi_Bhaskar_Resume.pdf             the file the download buttons serve
  favicon.svg                           SB monogram
  certificates/
    hackerrank-sql-basic.png            full-size originals. Never shown
    tap-academy-full-stack.jpg          inline — each opens in a new tab
    gateway-python-django.jpg           from its "View certificate" link
    code-galatta-programming.jpg
  fonts/
    manrope-variable.woff2              Manrope, variable 200–800 (25 KB)
    jetbrains-mono-500.woff2            JetBrains Mono 500 (22 KB)
README.md
```

Initial page load is roughly 180 KB; the four full-size certificate images
(another 430 KB) are only fetched when someone clicks through to one.

## Running it locally

It needs to be served over HTTP, not opened with `file://` — browsers refuse
to load fonts cross-origin from the filesystem, so the type would fall back.

```bash
cd swathi-portfolio
python3 -m http.server 8000
# then open http://localhost:8000
```

Any static server works equally well:

```bash
npx serve .
npx http-server -p 8000
```

## Building it

There is no build step. The file you edit is the file you deploy.

## Deploying

Upload the folder as-is to any static host. All asset paths are relative, so
it works from a domain root or a subdirectory without changes.

- **GitHub Pages** — push to a repo, then Settings → Pages → deploy from the
  branch root. For a project page the site lands at
  `https://<user>.github.io/<repo>/`; relative paths handle that correctly.
- **Netlify / Vercel / Cloudflare Pages** — drag the folder in, or connect the
  repo. Leave the build command empty and set the publish directory to the
  folder root.

### After deploying

Update the canonical and Open Graph URLs in `index.html` to the real address:

```html
<link rel="canonical" href="https://your-domain/">
<meta property="og:image" content="https://your-domain/assets/swathi-bhaskar.jpg">
```

`og:image` in particular needs an absolute URL — LinkedIn and X will not
resolve a relative one when generating a link preview.

## Editing common things

- **Replace the resume** — overwrite `assets/Swathi_Bhaskar_Resume.pdf`,
  keeping the filename. Four buttons point at it and none need changing.
- **Add a certificate** — drop the original into `assets/certificates/`, then
  copy an existing `<article class="cert-card">` block and change the title,
  issuer, facts row and link. No thumbnail needed: the cards are text only.
- **Replace the photo** — export at 4:5 and overwrite both the `.jpg` and the
  `.webp`. If you change the aspect ratio, update `aspect-ratio:4/5` in the
  `.portrait img` rule.
- **Colours** — every colour is a custom property in the `:root` block at the
  top of the `<style>`. `--accent` is the deep pine used for buttons, links
  and the architecture panels.
- **Add a project** — copy an `<article class="project">` block. The right-hand
  `.arch` panel is a stack of `.layer` divs with `.arch-arrow` between them;
  `.layer.hl` marks the entry point.

## Spacing

Vertical rhythm comes from two custom properties rather than per-section
values, so the whole page can be tightened or loosened from one place:

```css
--sec-pad:   clamp(38px, 4.4vw, 58px);  /* padding above and below a section */
--block-gap: 22px;                      /* heading block -> content below it */
```

Anchor scrolling is tied to the same variable:

```css
main section[id]{ scroll-margin-top: calc(var(--nav-h) + 14px - var(--sec-pad)); }
```

Subtracting the section's own top padding makes the *heading* land 14 px below
the nav rather than a screenful of empty padding. Because the offset cancels
the padding, the landing position stays identical at every viewport width —
measured at 82 px on 390 px, 768 px and 1440 px alike. If you change
`--sec-pad`, the anchor offset follows automatically.

## Notes on a few decisions

**External links and file links all open in a new tab.** Every off-site
anchor carries `target="_blank" rel="noopener noreferrer"`. The resume links
deliberately no longer use the `download` attribute: with `download` the file
saves straight to disk and never opens a tab, which is the opposite of the
intended behaviour. Without it, browsers open the PDF in their built-in viewer
in a new tab, and the viewer has its own download button. One caveat that
cannot be engineered away: if the host serves the PDF with
`Content-Disposition: attachment`, or the visitor has set "always download
PDFs", the browser downloads it instead of previewing. That is the host's and
the visitor's decision, not something the page can override.

**No contact form.** A form needs a backend or a third-party endpoint to
actually deliver mail, and a form that silently fails is worse than no form.
The contact section uses direct `mailto:`, `tel:` and profile links instead.
To add a working form later, [Web3Forms](https://web3forms.com) or
[Formspree](https://formspree.io) will do it with an access key and no server.

**Certificates are listed, not displayed.** Rendering four certificate images
inline cost about 1,600 px of page height on desktop and 2,300 px on mobile,
for images too small to read at card size anyway. The cards now carry the
title, issuer, date and credential number as text, and the original image is
one click away in a new tab at full resolution. The originals are untouched.

**No project screenshots.** The old portfolio could not be reached from the
build environment and no screenshots were supplied, so rather than mock up
fake interface images, each project card carries a small architecture panel
describing how that application is actually layered. Real screenshots would be
a straight improvement — drop them in and replace the `.arch` panel.

**Self-hosted fonts.** Manrope and JetBrains Mono ship with the site rather
than loading from Google Fonts. It is faster (no extra DNS, connection and
round-trip before text renders), it works offline, and it sends nothing to a
third party. Both are open licensed: Manrope under the SIL Open Font License,
JetBrains Mono under the SIL Open Font License.

**Motion.** One entrance sequence in the hero, and transitions on hover and
focus. There is deliberately no scroll-triggered animation on the lower
sections. `prefers-reduced-motion` is respected throughout.

## Browser support

Current Chrome, Edge, Firefox and Safari. Uses CSS custom properties, grid,
`clamp()`, `aspect-ratio` and `IntersectionObserver` — all long-standing
baseline features. Degrades cleanly without JavaScript: every section is plain
HTML, so only the mobile menu toggle and the active-link highlight need it.

## Accessibility

- Skip-to-content link as the first tab stop
- One `<h1>`, headings in order, landmark elements throughout
- Visible focus rings on every interactive element
- Mobile menu wired with `aria-expanded` / `aria-controls`, closes on Escape
- All text meets WCAG AA contrast (lowest measured pair is 4.67:1)
- Verified with no horizontal overflow from 320 px to 1920 px
