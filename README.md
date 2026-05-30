# md-paper

A Markdown-native template for publishing academic paper pages on GitHub Pages.  
Write your paper page entirely in Markdown — no HTML, no JSX, no build knowledge required.

---

## How it works

Your paper repo contains only the files you care about:

```
my-paper/
├── md-paper/          ← engine (git submodule — never touch this)
├── public/
│   └── media/         ← your images and videos
├── publication.ts     ← your paper metadata, authors, links, theme
├── content.md         ← your paper body in Markdown
└── package.json       ← 3 scripts, nothing else
```

The engine lives in the `md-paper/` submodule. When the engine is updated, you pull the change with one command — your content is untouched.

---

## Creating a new paper

### Step 1 — Create your repo

Go to GitHub and create a new empty repository for your paper (e.g. `my-paper`). Clone it locally:

```bash
git clone https://github.com/your-org/my-paper
cd my-paper
```

### Step 2 — Add the engine as a submodule

```bash
git submodule add https://github.com/mmlab-cv/md-paper md-paper
```

This creates the `md-paper/` folder containing the engine. You never edit anything inside it.

### Step 3 — Copy the paper scaffolding

Copy these three files from the `md-paper/` folder into your repo root:

```bash
cp md-paper/publication.ts .
cp md-paper/content.md .
cp md-paper/package.json .
```

Create the media folder:

```bash
mkdir -p public/media
```

Your repo should now look like the structure shown above.

### Step 4 — Install and preview

```bash
npm install
npm run dev
```

Open `http://localhost:5173` — you should see the demo paper. Now edit `publication.ts` and `content.md` to make it yours.

### Step 5 — Deploy

Build the site:

```bash
npm run build
```

Output goes to `docs/`. Commit it and push to your deployment branch:

```bash
git add docs/
git commit -m "Deploy"
git push origin main
```

On GitHub: **Settings → Pages → Source → Deploy from branch**, select your branch and `/docs` folder.

> **Custom base path** (needed when your site lives at `https://org.github.io/repo-name/`):  
> Set `VITE_BASE=/repo-name/` before building:
> ```bash
> VITE_BASE=/repo-name/ npm run build
> ```

---

## Updating the engine

When md-paper is updated, pull the new version into your paper repo:

```bash
git submodule update --remote md-paper
git add md-paper
git commit -m "Update md-paper engine"
```

Your `publication.ts`, `content.md`, and media are never affected.

---

## The files you edit

### `publication.ts` — metadata & media list

```ts
import type { Publication, Theme } from "./md-paper/types";
import { COMING_SOON } from "./md-paper/types";

const publication: Publication = {
  title: "Your Paper Title",
  authors: [
    ["A. Author", "https://scholar.google.com/...", "1"],
    ["B. Coauthor", undefined, "1,2"],   // no URL → plain text
  ],
  affiliations: [
    ["1", "University X"],
    ["2", "Institute Y"],
  ],
  venue: "CVPR 2025",    // or undefined to hide
  year:  "2025",
  abstract: "Your abstract...",

  // "https://..." = active button   COMING_SOON = greyed out   undefined = hidden
  paper:         "https://arxiv.org/abs/XXXX.XXXXX",
  pdf:           undefined,
  code:          COMING_SOON,
  supplementary: undefined,

  siteUrl:     "https://your-portfolio.github.io/",
  teaserIndex: 1,   // 1-based index of the media item shown as teaser

  // Media files go in public/media/ — paths start with /media/
  media: [
    {
      type:    "image",
      src:     "/media/figure1.png",
      id:      "overview",   // optional alias — use as [MEDIA:overview] in content.md
      title:   "Figure title",
      caption: "Figure caption.",
    },
    {
      type:  "video",
      src:   "/media/demo.mp4",
      title: "Demo",
    },
  ],
};

export default publication;
```

---

### `content.md` — paper body

Standard Markdown with special tokens for media and layout.

#### Embedding media

| Token | Result |
|---|---|
| `[MEDIA:1]` | Single item, full width (1-based index) |
| `[MEDIA:overview]` | Single item by alias (`id` in publication.ts) |
| `[MEDIA:1:0.6]` | Single item at 60% width |
| `[MEDIA:1-4]` | Carousel — items 1 through 4 |
| `[MEDIA:1,3,5]` | Carousel — non-contiguous picks |

Add `{...}` after any token for a Markdown caption block rendered above the media:

```
[MEDIA:1]{**Figure 1.** Caption supports **bold**, *italic*, `code`, and math ($\alpha$).}
```

#### Multi-column layout

```
[MEDIA-MULTICOL:1.1]
[MEDIA:1]{**Left column caption.**}
[MEDIA:2]{**Right column caption.**}
[/MEDIA-MULTICOL]
```

The number after the colon is a scale factor — `1.1` lets the block extend 10% beyond the normal content width. Columns collapse to a single column on mobile automatically.

#### Spacing

```
[SPACING:small]    →  16 px
[SPACING:medium]   →  32 px
[SPACING:large]    →  48 px
[SPACING:xlarge]   →  64 px
```

#### Math

Full [KaTeX](https://katex.org/) — inline `$...$` and display `$$...$$`.

---

### `theme` in `publication.ts` — visual customisation

All fields are optional — remove any line to keep its default.

```ts
const theme: Theme = {
  accentColor:          "#0a4b7c",   // headings, separator dots, footer background
  pageBackground:       "#ffffff",
  blockBackground:      "#f7f7f7",   // abstract / text-block background
  baseFontSize:         16,          // root px — scales the whole page
  titleFontSize:        48,          // paper title (clamped on small screens)
  authorFontSize:       18,
  headingFontSize:      22,          // h2/h3 inside content
  abstractFontSize:     16,
  contentFontSize:      16,
  mediaTitleFontSize:   18,
  mediaCaptionFontSize: 13,
  contentMaxWidth:      1200,        // max column width in px
  bodyFont:             "Lato, sans-serif",
  headingFont:          '"Patua One", serif',
};
```

---

## License

Open source — free to use with attribution.  
If md-paper is useful for your work, a link back is appreciated.
