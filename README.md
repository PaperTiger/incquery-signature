# IncQuery Email Signature Builder

A single-page tool for creating on-brand IncQuery email signatures. Fill in your
details, watch the live preview update, and copy a signature that pastes cleanly
into Outlook, Gmail, and Apple Mail.

**Live:** https://papertiger.github.io/incquery-signature/

## Features

- **Live preview** as you type.
- **One-click copy** — puts a formatted (rich-HTML) signature on the clipboard so
  it pastes with all styling intact. Also copies a plain-text fallback.
- **Copy HTML source** for anyone pasting into an HTML signature field.
- **Optional fields auto-hide** — leave a field blank and its line drops out, with
  spacing recalculated automatically.
- **Brand-locked** logo panel and `IncQuery.com | ALWAYS BE SURE™` footer, so every
  signature stays consistent.

## Fields

| Field | Notes |
| --- | --- |
| Full name | Required |
| Pronouns | Optional — appended as `Name \| pronouns` |
| Job title | |
| Location | e.g. `New York \| EST` |
| Phone | Optional |
| Phone link | Optional — defaults to a `tel:` link built from the number |
| "Grab a coffee" link | Optional URL. When set, it renders at the bottom with the LinkedIn icon to its left |
| LinkedIn icon | On/off toggle (on by default); always links to the IncQuery company page |

## Why it survives paste into Outlook

Email clients rewrite `<p>` margins and `line-height` and ignore `border-radius`,
which is what makes pasted signatures look loose or square-cornered. This builder
avoids all of that:

- **Table-based layout** with spacing driven by `<td>` padding (respected on paste),
  not paragraph margins.
- **Explicit `line-height` + `mso-line-height-rule:exactly`** on every line.
- **Images are pre-rendered PNGs** hosted on the Webflow CDN — including the logo
  panel (rounded corners baked into the image, so Outlook can't strip them) and the
  LinkedIn icon (padded so scaling can't clip it).

The **copied** output references the CDN image URLs (email clients block data-URI
images). The in-page **preview** uses embedded data URIs so it renders even where the
CDN isn't reachable.

## Editing brand constants

All fixed values live as constants near the top of the `<script>` in `index.html`:

```js
var SITE_URL     = "https://incquery.com";
var LINKEDIN_URL = "https://www.linkedin.com/company/incquery/";
var PANEL_EMAIL  = "https://cdn.prod.website-files.com/.../Logo%20Panel.png";
var ICON_EMAIL   = "https://cdn.prod.website-files.com/.../linkedin-icon%402x.png";
```

If you re-export the logo panel or icon, upload the new PNG to the Webflow assets and
update `PANEL_EMAIL` / `ICON_EMAIL`. To keep the live preview in sync, also replace the
matching `PANEL_PREVIEW` / `ICON_PREVIEW` data URIs.

## Local preview

The app is a single self-contained file with no build step. Serve the folder over
HTTP (rich clipboard copy needs a secure context — `localhost` counts):

```bash
python3 -m http.server 8000
# then open http://localhost:8000/
```

Opening `index.html` directly via `file://` also works, though the modern clipboard
API may fall back to the legacy copy path.

## Deployment

Hosted with **GitHub Pages** from the `develop` branch, `/ (root)` folder
(Settings → Pages). Any push to `develop` redeploys.
