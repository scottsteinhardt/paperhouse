# Paperhouse

A simple, easy-to-deploy photo blog.

Named after [the Can song](https://open.spotify.com/track/3vTZCQSFPCkiNN6tFFqFMi).

**[See a live demo →](https://scottfromny.com/photos)**

[![Demo screenshot](docs/demo.png)](https://scottfromny.com/photos)

---

## Why Paperhouse

One Python script. No database, no dashboard, no monthly fees. You drop photos and YAML files into folders, run one command, and get a static site ready to upload to Cloudflare Pages — for free.

Each photo lives in its own folder alongside a `meta.yaml` that holds the caption, date, and location. Date and location in the YAML always win over whatever the image metadata says.

Light and dark mode are built in — the site respects `prefers-color-scheme` automatically, and readers can toggle manually with a button that remembers their preference.

---

## Requirements

- Python 3.11+ (or Python 3.9–3.10 with `pip install tomli`)
- `pip install pyyaml`

---

## Installation

```bash
curl -o paperhouse https://raw.githubusercontent.com/scottsteinhardt/paperhouse/main/paperhouse
chmod +x paperhouse
mv paperhouse /usr/local/bin/
```

Install the Python dependency:

```bash
pip install pyyaml
```

Verify:

```bash
paperhouse --help
```

---

## Quick start

```bash
mkdir my-photos && cd my-photos
paperhouse init
```

This creates:

```
my-photos/
├── paperhouse.toml          ← your configuration
├── photos/
│   └── example-photo/
│       └── meta.yaml        ← starter metadata
└── site/
    └── assets/
        └── style.css        ← starter stylesheet
```

Open `paperhouse.toml` and fill in your name, site URL, and nav links. Then:

```bash
paperhouse --dry    # preview what would be built
paperhouse          # build your site
```

---

## Adding photos

Each photo lives in its own folder inside `photos/`:

```
photos/
  brooklyn-bridge/
    brooklyn-bridge.jpg    ← your photo (any common image format)
    meta.yaml              ← caption, date, location
  coney-island-winter/
    coney-island.jpg
    meta.yaml
```

The folder name becomes the URL slug (e.g., `/brooklyn-bridge/`).

**meta.yaml:**

```yaml
caption: "Looking up at the Brooklyn Bridge at golden hour."
date: 2026-03-15
location: "Brooklyn, NY"
# alt: "Optional alt text for accessibility — falls back to caption"
# draft: true
# slug: custom-url-slug
```

| Field      | Required | Notes |
|------------|----------|-------|
| `caption`  | yes      | The photo caption. Also used as the page title and og:title. |
| `date`     | no       | `YYYY-MM-DD`. Uses file modification time if omitted. Overrides image EXIF. |
| `location` | no       | Plain text. Overrides image EXIF location. |
| `alt`      | no       | Alt text for accessibility. Falls back to `caption`. |
| `draft`    | no       | `true` hides the photo from all builds. |
| `slug`     | no       | URL slug. Auto-generated from folder name if omitted. |

**Supported image formats:** `.jpg`, `.jpeg`, `.png`, `.webp`, `.avif`, `.gif`

If a folder contains multiple images, Paperhouse picks the first one it finds (alphabetically, jpg/jpeg first).

---

## Commands

```
paperhouse init           Set up a new project in the current directory
paperhouse                Build your site
paperhouse --dry          Preview what would be built without writing anything
paperhouse --config PATH  Use a specific config file instead of paperhouse.toml
```

---

## Configuration

`paperhouse.toml` controls everything. See `paperhouse.example.toml` for a commented template.

### `[paths]`

| Key      | Description |
|----------|-------------|
| `photos` | Path to your photo folders directory |
| `site`   | Path to the output directory for your built site |

### `[site]`

| Key                | Description |
|--------------------|-------------|
| `base_url`         | Your site's root URL, no trailing slash |
| `title`            | Displayed in the header on every page |
| `subtitle`         | Short tagline used in SEO meta only |
| `description`      | Index page meta description |
| `author`           | Your name, used in bylines and copyright |
| `email`            | Your email, used in structured data (optional) |
| `css_url`          | URL path to your stylesheet |
| `default_og_image` | Fallback social sharing image, relative to `base_url` |

### `[author]`

Used for Schema.org structured data. All optional.

| Key               | Description |
|-------------------|-------------|
| `location_city`   | Your city |
| `location_region` | Your state or region |
| `location_country`| Defaults to `US` |
| `instagram`       | Full URL |
| `bluesky`         | Full URL |
| `twitter`         | Full URL |
| `mastodon`        | Full URL |

### `[[nav]]` *(repeatable)*

| Key        | Description |
|------------|-------------|
| `label`    | Link text |
| `href`     | URL |
| `external` | `true` → adds `target="_blank" rel="noopener"` |

---

## Design

The starter stylesheet (`site/assets/style.css`) is a clean, minimal design with:

- **Light and dark mode** — auto-detects `prefers-color-scheme`, toggleable with a button that stores the preference in `localStorage`
- **Responsive photo grid** — 3 columns on desktop, 2 on tablet, 1 on mobile
- **3:2 aspect ratio thumbnails** on the grid with `object-fit: cover`
- **Full natural-size photo** on individual pages
- **Prev/next navigation** between photos (sorted newest-first)
- **Inter** loaded from Google Fonts for clean readability

All styles are CSS custom properties — switching colors is a one-line edit at the top of the file.

---

## Deploying to Cloudflare Pages

Every time you run `paperhouse`, it generates a zip file named something like `UPLOAD THIS AS OF 2026-03-15 2:18 PM.zip` in the parent directory of your site. That's your deploy artifact.

**First time:**

1. Go to [Cloudflare Pages](https://pages.cloudflare.com/) → **Create a project** → **Direct Upload**
2. Name your project (becomes `yourproject.pages.dev`)
3. Upload the zip file Paperhouse generated
4. Click **Deploy**

**Subsequent deploys:**

1. Run `paperhouse` — builds and generates a new zip
2. Go to your Cloudflare Pages project → **Deployments** → **Upload**
3. Drop in the new zip

**Custom domain:** Add it under **Custom domains** in your project settings.

---

## What gets built

```
site/
├── index.html              ← photo grid
├── feed.xml                ← RSS feed
├── sitemap.xml
├── llms.txt
├── assets/
│   └── style.css
├── images/
│   └── 2026/03/
│       └── brooklyn-bridge/
│           └── brooklyn-bridge.jpg
└── brooklyn-bridge/
    └── index.html          ← individual photo page
```

---

## License

MIT
