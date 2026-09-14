# Cloud Service Atlas

Static HTML/CSS/JavaScript rebuild of CloudComparer for comparing equivalent
services across major cloud service providers.

A single-page, dependency-free rebuild of [ilyas-it83/CloudComparer](https://github.com/ilyas-it83/CloudComparer),
which was originally a Jekyll (Ruby) site. This version is a static
HTML + CSS + JavaScript app — no build step, no server, no framework.

The comparison currently covers AWS, Azure, Google Cloud, IBM Cloud, Oracle
Cloud, Alibaba Cloud and Huawei Cloud.

## Run locally

Open `index.html` directly in a browser (double-click it, or drag it into a
browser window). Everything — markup, styling and behaviour — lives in that
one file; it only reaches outside itself for:

- the `assets/img/` folder next to it (provider logos + ~950 service icons)
- Google Fonts (IBM Plex Mono / IBM Plex Sans), loaded from a CDN `<link>`

For a local HTTP preview, run a static server from the project directory:

```sh
python3 -m http.server 8000
```

Then open <http://localhost:8000>.

The page also works fully offline after removing the Google Fonts `<link>`
tags in `<head>`; it falls back to system fonts automatically.

To host it, upload the whole folder (`index.html` + `assets/` + `favicon.ico`)
to any static host (GitHub Pages, Netlify, S3, etc.) — no build tooling
required.

## Interface guide

### Search

The search field is a live, free-text filter. It searches across:

- service groups, such as `Compute` or `Storage`
- groups/subcategories, such as `Virtual Server` or `Object Storage`
- provider service names, such as `Amazon EC2`, `Lambda` or `Kubernetes`

Search results update as you type. Search can be combined with provider and
Service Group filters.

### Provider filters

The provider chips toggle CSP columns on and off. Multiple providers can be
enabled at the same time, making it possible to compare a smaller set such as
AWS versus Azure. Use `reset filters` to restore all providers, clear the
search field and remove selected Service Groups.

### Service Group filter

The Service Group control is a multi-select dropdown on the same row as the
provider chips. Its options are sorted alphabetically. Select one or more
groups to show only those categories in the matrix.

- `All service groups` means no Service Group restriction is active.
- `Clear selection` removes all selected groups without changing the search.
- Search and Service Group selection can be combined.
- The matrix remains visible while the dropdown is open; only the category
  filter changes the rendered results.

### Matrix and responsive layout

Categories and service groups are rendered alphabetically. Each service links
to its provider documentation when a reference URL is available. Missing
provider equivalents are shown as empty cells.

Below approximately 880px, the matrix reflows from a grid table into stacked,
labeled cards per service group. Provider logos and service icons are loaded
from the local `assets/` directory.

### Day and night themes

Use the `Night`/`Day` button in the header to switch the visual theme. The
selection is stored in the browser and restored on the next visit. The day
theme is the default when no preference has been saved.

## What changed from the original

- **Jekyll/Liquid → static JS.** The original used a Ruby/Jekyll build with
  Liquid templating over a `_data/cloudservices.yml` file. That YAML data
  (24 categories, 114 service groups, 953 individual services across AWS,
  Azure, GCP, IBM Cloud, Oracle Cloud, Alibaba Cloud and Huawei Cloud) has
  been converted to JSON and is embedded directly in `index.html`; the page
  renders the whole matrix client-side with vanilla JavaScript (no
  frameworks, no build step).
- **Search.** A live search box filters the matrix by category, service
  group, or service name.
- **Provider filters.** Toggle any of the seven providers on/off to narrow
  the comparison (e.g. "just AWS vs Azure").
- **Service Group navigation.** Choose one or more alphabetized Service Groups
  from the compact dropdown in the provider filter row. The body no longer
  contains a separate Service Group navigation panel.
- **Combined filtering.** Search text, provider visibility and Service Group
  selection work together and can be reset from the filter controls.
- **Day/night theme.** Switch between light and dark palettes from the header;
  the choice persists locally in the browser.
- **Responsive layout.** Below ~880px the matrix reflows from a grid table
  into stacked, labeled cards per service group (the original project's
  [issue #196](https://github.com/ilyas-it83/CloudComparer/issues/196),
  "make the comparison table responsive," is addressed directly).
- **New visual design.** A "technical atlas" look (grid backdrop, catalog
  numbering, IBM Plex Mono/Sans) replaces the original Jekyll `minima`
  theme — this is a fresh visual take, not a pixel copy of the original.
- A handful of small data-quality fixes: 4 icon filenames in the original
  YAML didn't match any file on disk (stray `%20` in the filename, one
  wrong Google Cloud icon name) — corrected during conversion.

## Updating the data

The service matrix lives inside `index.html` as a single JavaScript array
(`var DATA = [...]`), near the bottom of the file, just above
`var PROVIDERS`. Each entry looks like:

```js
{
  "category": "Compute",
  "subcategory": "Virtual Server",
  "aws":     [{ "name": "Amazon EC2", "ref": "https://...", "icon": "Arch_Amazon-EC2_64.png" }],
  "azure":   [{ "name": "Azure Virtual Machine", "ref": "https://...", "icon": "Azure Virtual Machine.png" }],
  "google":  [...], "ibm": [...], "oracle": [...], "alibaba": [...], "huawei": [...]
}
```

`icon` filenames are resolved at runtime against
`assets/img/cloudproviders/<provider>/<icon>`. To add or edit a service,
edit that array directly (it's plain JSON-compatible JS) and make sure any
new icon file is placed in the matching provider folder.

After changing data or behavior, verify both desktop and narrow layouts. A
minimal validation pass is:

1. Open the page directly or through `python3 -m http.server 8000`.
2. Search for a category, subcategory and provider service name.
3. Select multiple Service Groups and use `Clear selection`.
4. Toggle provider chips and use `reset filters`.
5. Confirm provider icons, service icons and external links still work.

## Project structure

```text
index.html    Application markup, styles, embedded data and behavior
README.md     Project documentation
LICENSE.md    MIT license
favicon.ico   Browser tab icon
assets/       Provider logos and service icons
```

There is intentionally no package manager, build directory or runtime
dependency. Keep changes focused in `index.html` unless the data assets or
project documentation also need to change.

## Deployment

This repository contains only the public static application. Production
hosting configuration, infrastructure addresses, credentials, certificates,
and deployment commands are maintained separately from this repository.

## Credits & license

Service data and the original concept are from
[ilyas-it83/CloudComparer](https://github.com/ilyas-it83/CloudComparer) by
Ilyas F, released under the MIT License (see `LICENSE.md`). This rebuild
carries the same license. Provider names, logos and service icons belong to
their respective owners (AWS, Microsoft, Google, IBM, Oracle, Alibaba,
Huawei) and are used here only for identification, as in the original
project.
