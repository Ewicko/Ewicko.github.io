# Blog-First Site Redesign Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Convert ethanwicko.com from a static HTML portfolio into a Hugo-based blog-first site with ultra-minimal serif design.

**Architecture:** Hugo static site generator with custom templates, deployed via GitHub Actions to GitHub Pages. Blog as homepage, projects as secondary section. Georgia serif typography, light #fafafa background, 640px content width.

**Tech Stack:** Hugo (extended), GitHub Actions, KaTeX (conditional), Giscus (comments), HTML/CSS

**Spec:** `docs/superpowers/specs/2026-03-18-blog-redesign-design.md`

**Note:** This is a static site — no unit test framework. Verification is via `hugo build` (must succeed with zero errors) and `hugo server` for visual inspection. Each task ends with a build check.

---

## File Structure

```
ethanwicko.github.io/
├── hugo.toml                           # Hugo configuration
├── archetypes/
│   └── default.md                      # Default front matter for new posts
├── content/
│   ├── blog/
│   │   ├── _index.md                   # Blog section config (homepage)
│   │   └── lorem-ipsum-sample.md       # Sample blog post with all features
│   ├── projects/
│   │   ├── _index.md                   # Projects section config
│   │   ├── compliant-mechanism-generative.md
│   │   ├── flexurediff.md
│   │   ├── force-profile-flexures.md
│   │   ├── manufix.md
│   │   ├── siam-poster.md
│   │   ├── spacex.md
│   │   └── older-projects.md           # External link card (no standalone page)
│   └── about.md                        # About page
├── layouts/
│   ├── _default/
│   │   ├── baseof.html                 # Base template (html, head, nav, footer)
│   │   ├── list.html                   # Default list template
│   │   └── single.html                 # Default single page template
│   ├── blog/
│   │   ├── list.html                   # Blog index (homepage)
│   │   └── single.html                 # Individual blog post
│   ├── projects/
│   │   ├── list.html                   # Projects grid
│   │   └── single.html                 # Individual project page
│   ├── page/
│   │   └── single.html                 # About page layout
│   ├── 404.html                        # Custom 404 page
│   ├── index.html                      # Homepage override (renders blog list)
│   ├── shortcodes/
│   │   └── video.html                  # Video embedding shortcode
│   └── partials/
│       ├── head.html                   # <head> with meta, OG, CSS, KaTeX
│       ├── nav.html                    # Site navigation
│       ├── footer.html                 # Footer with RSS link
│       ├── share.html                  # Share links (X, LinkedIn, copy)
│       └── comments.html              # Giscus comments
├── static/
│   ├── css/
│   │   └── style.css                   # Single stylesheet
│   ├── images/                         # All existing image directories
│   ├── favicon.ico
│   ├── favicon.png                     # Optimized version (<50KB)
│   ├── CNAME
│   └── robots.txt                      # Updated sitemap URL
└── .github/
    └── workflows/
        └── deploy.yml                  # Hugo build + GitHub Pages deploy
```

---

### Task 1: Hugo Scaffolding & Configuration

**Files:**
- Create: `hugo.toml`
- Create: `archetypes/default.md`
- Move: `CNAME` → `static/CNAME`
- Move: `robots.txt` → `static/robots.txt` (update sitemap URL)
- Move: `favicon.ico` → `static/favicon.ico`
- Move: `favicon.png` → `static/favicon.png`
- Move: `images/` → `static/images/`

- [ ] **Step 1: Install Hugo (if not present) and init the project structure**

Hugo needs to be installed. Check with `hugo version`. If not installed:

```bash
brew install hugo
```

Create the Hugo directory structure (do NOT run `hugo new site` — we're retrofitting into the existing repo):

```bash
cd /Users/ethanwicko/Ewicko.github.io
mkdir -p archetypes content/blog content/projects layouts/_default layouts/blog layouts/projects layouts/page layouts/partials layouts/shortcodes static/css .github/workflows
```

- [ ] **Step 2: Create hugo.toml**

```toml
baseURL = "https://ethanwicko.com/"
languageCode = "en-us"
title = "Ethan Wicko"
enableRobotsTXT = false

[params]
  description = "Projects, research, and experiments by Ethan Wicko."
  author = "Ethan Wicko"
  mainSections = ["blog"]

[params.giscus]
  repo = "Ewicko/Ewicko.github.io"
  repoID = ""
  category = "Blog Comments"
  categoryID = ""
  mapping = "pathname"
  theme = "light"

[permalinks]
  blog = "/blog/:slug/"
  projects = "/projects/:slug/"

[outputs]
  home = ["HTML", "RSS"]
  section = ["HTML"]

[outputFormats.RSS]
  mediatype = "application/rss+xml"
  baseName = "index"

[markup]
  [markup.highlight]
    style = "monokailight"
    noClasses = true
    lineNos = false
  [markup.goldmark]
    [markup.goldmark.renderer]
      unsafe = true

```

Note: The giscus `repoID` and `categoryID` are left blank — the user must configure these after enabling GitHub Discussions on the repo. Instructions will be in a comment in `hugo.toml`.

- [ ] **Step 3: Create archetypes/default.md**

```markdown
---
title: "{{ replace .File.ContentBaseName "-" " " | title }}"
date: {{ .Date }}
description: ""
draft: true
math: false
---
```

- [ ] **Step 4: Move static assets into static/**

```bash
cd /Users/ethanwicko/Ewicko.github.io
cp CNAME static/CNAME
cp favicon.ico static/favicon.ico
cp favicon.png static/favicon.png
cp -r images static/images
```

- [ ] **Step 5: Update robots.txt and copy to static/**

Update the sitemap URL from `ewicko.github.io` to `ethanwicko.com`, then copy:

```bash
sed 's|https://ewicko.github.io/sitemap.xml|https://ethanwicko.com/sitemap.xml|' robots.txt > static/robots.txt
```

- [ ] **Step 6: Optimize favicon.png**

Resize the 1.3MB favicon.png to a reasonable size:

```bash
sips -Z 180 static/favicon.png
```

This resizes to max 180px dimension, which is sufficient for a favicon and should bring it well under 50KB.

- [ ] **Step 7: Update .gitignore for Hugo**

Add Hugo build artifacts to `.gitignore`:

Append the following lines to the existing `.gitignore`:
```
public/
resources/
.hugo_build.lock
```

- [ ] **Step 8: Verify Hugo recognizes the project**

```bash
cd /Users/ethanwicko/Ewicko.github.io
hugo version
hugo config
```

Expected: Hugo outputs the parsed configuration with no errors.

- [ ] **Step 9: Commit**

```bash
git add hugo.toml archetypes/ static/CNAME static/robots.txt static/favicon.ico static/favicon.png static/images/ .gitignore
git commit -m "feat: scaffold Hugo project with config and static assets"
```

---

### Task 2: CSS Stylesheet

**Files:**
- Create: `static/css/style.css`

- [ ] **Step 1: Create the stylesheet**

Write `static/css/style.css` with the full design language from the spec:

```css
/* Reset */
*, *::before, *::after { box-sizing: border-box; }
body { margin: 0; }

/* Typography */
body {
  font-family: Georgia, 'Times New Roman', serif;
  font-size: 17px;
  line-height: 1.7;
  color: #333;
  background: #fafafa;
  -webkit-font-smoothing: antialiased;
}

h1, h2, h3, h4 {
  font-family: Georgia, serif;
  color: #1a1a1a;
  line-height: 1.3;
}

h1 { font-size: 32px; font-weight: normal; margin: 0 0 8px; }
h2 { font-size: 24px; margin: 40px 0 16px; }
h3 { font-size: 20px; margin: 32px 0 12px; }

a { color: #0066cc; text-decoration: none; }
a:hover { text-decoration: underline; }

/* Layout */
.container { width: 100%; max-width: 640px; margin: 0 auto; padding: 0 20px; }
.container-wide { width: 100%; max-width: 1100px; margin: 0 auto; padding: 0 20px; }

/* Navigation */
.site-nav {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
  font-size: 13px;
  padding: 16px 0;
  border-bottom: 1px solid #e0e0e0;
  margin-bottom: 40px;
}

.site-nav .nav-inner {
  max-width: 640px;
  margin: 0 auto;
  padding: 0 20px;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.site-nav.nav-wide .nav-inner {
  max-width: 1100px;
}

.site-name { color: #1a1a1a; font-weight: normal; }
.site-name:hover { text-decoration: none; color: #0066cc; }

.nav-links { display: flex; gap: 20px; }
.nav-links a { color: #666; }
.nav-links a:hover { color: #0066cc; }
.nav-links a.active { color: #1a1a1a; font-weight: 700; }

/* Blog index */
.post-list { list-style: none; padding: 0; margin: 0; }

.post-item {
  padding: 20px 0;
  border-bottom: 1px solid #e0e0e0;
}

.post-item:last-child { border-bottom: none; }

.post-date {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
  font-size: 13px;
  color: #999;
  display: block;
  margin-bottom: 4px;
}

.post-title {
  font-size: 18px;
  font-weight: normal;
  margin: 0;
}

.post-title a { color: #0066cc; }
.post-title a:hover { text-decoration: underline; }

.post-summary {
  font-size: 15px;
  color: #555;
  margin: 6px 0 0;
}

/* Blog post */
.post-header { margin-bottom: 32px; }
.post-header h1 { font-size: 32px; margin-bottom: 8px; }
.post-header .post-date { margin-bottom: 0; }

.post-content img {
  max-width: 100%;
  height: auto;
  display: block;
  margin: 24px 0;
}

.post-content blockquote {
  border-left: 3px solid #e0e0e0;
  margin: 24px 0;
  padding: 0 0 0 20px;
  color: #555;
}

/* Code blocks */
.post-content code {
  font-family: Menlo, Monaco, 'Courier New', monospace;
  font-size: 14px;
  background: #f5f5f5;
  padding: 2px 5px;
  border-radius: 3px;
}

.post-content pre {
  background: #f5f5f5;
  padding: 16px;
  border-radius: 4px;
  overflow-x: auto;
  line-height: 1.5;
}

.post-content pre code {
  background: none;
  padding: 0;
  font-size: 14px;
}

/* Projects grid */
.projects-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(260px, 1fr));
  gap: 24px;
  padding: 24px 0 60px;
}

.project-card {
  background: #fff;
  border: 1px solid #e0e0e0;
  border-radius: 8px;
  overflow: hidden;
  transition: box-shadow 200ms ease, border-color 200ms ease;
  text-decoration: none;
  color: inherit;
  display: block;
}

.project-card:hover {
  box-shadow: 0 4px 12px rgba(0,0,0,0.08);
  border-color: #ccc;
  text-decoration: none;
}

.project-card .thumb {
  aspect-ratio: 16/9;
  width: 100%;
  display: block;
  object-fit: cover;
}

.project-card .card-body { padding: 16px; }

.project-card .card-title {
  font-size: 17px;
  font-weight: 700;
  color: #1a1a1a;
  margin: 0 0 6px;
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
}

.project-card .card-desc {
  font-size: 14px;
  color: #666;
  margin: 0 0 10px;
  line-height: 1.5;
}

.project-tags { display: flex; flex-wrap: wrap; gap: 6px; }

.project-tag {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
  font-size: 11px;
  padding: 3px 8px;
  border-radius: 999px;
  background: #f0f0f0;
  color: #555;
  border: 1px solid #e0e0e0;
}

/* Individual project page */
.project-content { max-width: 1100px; }

.breadcrumb {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
  font-size: 13px;
  color: #999;
  margin-bottom: 24px;
}

.breadcrumb a { color: #0066cc; }

.project-meta {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
  font-size: 13px;
  color: #999;
  display: flex;
  gap: 24px;
  flex-wrap: wrap;
  margin: 12px 0 32px;
}

.project-meta span { display: inline; }

.two-col {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 32px;
  align-items: start;
  margin: 24px 0;
}

.tech-stack { display: flex; flex-wrap: wrap; gap: 8px; margin: 16px 0; }

.tech-badge {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
  font-size: 12px;
  padding: 4px 10px;
  border-radius: 999px;
  background: #f0f0f0;
  color: #555;
  border: 1px solid #e0e0e0;
}

.project-nav {
  display: flex;
  justify-content: space-between;
  padding: 32px 0;
  border-top: 1px solid #e0e0e0;
  margin-top: 48px;
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
  font-size: 14px;
}

/* Share links */
.share-links {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
  font-size: 14px;
  padding: 32px 0;
  border-top: 1px solid #e0e0e0;
  margin-top: 48px;
  color: #999;
}

.share-links a { margin-right: 16px; }

/* Comments */
.comments-section {
  margin-top: 32px;
  padding-top: 32px;
  border-top: 1px solid #e0e0e0;
}

/* Footer */
.site-footer {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
  font-size: 13px;
  color: #999;
  border-top: 1px solid #e0e0e0;
  padding: 28px 0 40px;
  margin-top: 60px;
}

.site-footer a { color: #0066cc; }

/* Video */
.video-embed {
  max-width: 100%;
  margin: 24px 0;
}

.video-embed video {
  width: 100%;
  height: auto;
  border-radius: 4px;
}

/* 404 */
.not-found {
  text-align: center;
  padding: 80px 0;
}

/* Responsive */
@media (max-width: 768px) {
  .two-col { grid-template-columns: 1fr; }
  .project-meta { flex-direction: column; gap: 8px; }
  .site-nav .nav-inner { flex-direction: column; gap: 8px; }
}

@media (prefers-reduced-motion: reduce) {
  .project-card { transition: none; }
}
```

- [ ] **Step 2: Verify the file exists**

```bash
ls -la static/css/style.css
```

- [ ] **Step 3: Commit**

```bash
git add static/css/style.css
git commit -m "feat: add ultra-minimal stylesheet"
```

---

### Task 3: Base Templates (head, nav, footer, baseof)

**Files:**
- Create: `layouts/partials/head.html`
- Create: `layouts/partials/nav.html`
- Create: `layouts/partials/footer.html`
- Create: `layouts/_default/baseof.html`

- [ ] **Step 1: Create partials/head.html**

```html
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<meta name="robots" content="noai, noimageai">
<meta name="theme-color" content="#fafafa">

<title>{{ if .IsHome }}{{ .Site.Title }}{{ else }}{{ .Title }} · {{ .Site.Title }}{{ end }}</title>

<meta name="description" content="{{ with .Description }}{{ . }}{{ else }}{{ .Site.Params.description }}{{ end }}">

<!-- Open Graph -->
<meta property="og:title" content="{{ .Title }}">
<meta property="og:description" content="{{ with .Description }}{{ . }}{{ else }}{{ .Site.Params.description }}{{ end }}">
<meta property="og:url" content="{{ .Permalink }}">
<meta property="og:type" content="{{ if eq .Section "blog" }}article{{ else }}website{{ end }}">
{{ with .Params.thumbnail }}<meta property="og:image" content="{{ . | absURL }}">{{ end }}

<!-- Twitter -->
<meta name="twitter:card" content="{{ if .Params.thumbnail }}summary_large_image{{ else }}summary{{ end }}">

<link rel="canonical" href="{{ .Permalink }}">
<link rel="alternate" type="application/rss+xml" title="{{ .Site.Title }}" href="{{ "index.xml" | absURL }}">
<link rel="icon" type="image/png" href="/favicon.png">
<link rel="icon" type="image/x-icon" href="/favicon.ico">
<link rel="stylesheet" href="/css/style.css">

{{ if .Params.math }}
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/katex@0.16.11/dist/katex.min.css">
<script defer src="https://cdn.jsdelivr.net/npm/katex@0.16.11/dist/katex.min.js"></script>
<script defer src="https://cdn.jsdelivr.net/npm/katex@0.16.11/dist/contrib/auto-render.min.js"
  onload="renderMathInElement(document.body, {delimiters: [{left: '$$', right: '$$', display: true},{left: '$', right: '$', display: false}]});">
</script>
{{ end }}
```

- [ ] **Step 2: Create partials/nav.html**

```html
<nav class="site-nav{{ if eq .Section "projects" }} nav-wide{{ end }}">
  <div class="nav-inner">
    <a href="/" class="site-name">ethanwicko.com</a>
    <div class="nav-links">
      <a href="/" {{ if .IsHome }}class="active"{{ end }}>Blog</a>
      <a href="/projects/" {{ if eq .Section "projects" }}class="active"{{ end }}>Projects</a>
      <a href="/about/" {{ if eq .Title "About" }}class="active"{{ end }}>About</a>
    </div>
  </div>
</nav>
```

- [ ] **Step 3: Create partials/footer.html**

```html
<footer class="site-footer">
  <div class="container">
    &copy; {{ now.Year }} Ethan Wicko · <a href="/index.xml">Subscribe via RSS</a>
  </div>
</footer>
```

- [ ] **Step 4: Create layouts/_default/baseof.html**

```html
<!DOCTYPE html>
<html lang="en">
<head>
  {{ partial "head.html" . }}
</head>
<body>
  {{ partial "nav.html" . }}
  <main>
    {{ block "main" . }}{{ end }}
  </main>
  {{ partial "footer.html" . }}
</body>
</html>
```

- [ ] **Step 5: Create layouts/_default/list.html and single.html (fallbacks)**

`layouts/_default/list.html`:
```html
{{ define "main" }}
<div class="container">
  <h1>{{ .Title }}</h1>
  {{ .Content }}
</div>
{{ end }}
```

`layouts/_default/single.html`:
```html
{{ define "main" }}
<div class="container">
  <h1>{{ .Title }}</h1>
  {{ .Content }}
</div>
{{ end }}
```

- [ ] **Step 6: Build check**

```bash
cd /Users/ethanwicko/Ewicko.github.io && hugo --printPathWarnings 2>&1
```

Expected: Build succeeds (may warn about no content, which is fine).

- [ ] **Step 7: Commit**

```bash
git add layouts/
git commit -m "feat: add base templates (head, nav, footer, baseof)"
```

---

### Task 4: Blog Templates (Homepage + Post)

**Files:**
- Create: `layouts/index.html`
- Create: `layouts/blog/list.html`
- Create: `layouts/blog/single.html`
- Create: `layouts/partials/share.html`
- Create: `layouts/partials/comments.html`
- Create: `content/blog/_index.md`

- [ ] **Step 1: Create content/blog/_index.md**

```markdown
---
title: "Blog"
---
```

- [ ] **Step 2: Create layouts/index.html (homepage = blog index)**

```html
{{ define "main" }}
<div class="container">
  {{ $posts := where .Site.RegularPages "Section" "blog" }}
  {{ if $posts }}
  <ul class="post-list">
    {{ range $posts }}
    <li class="post-item">
      <span class="post-date">{{ .Date.Format "January 2006" }}</span>
      <h2 class="post-title"><a href="{{ .RelPermalink }}">{{ .Title }}</a></h2>
      {{ with .Description }}<p class="post-summary">{{ . }}</p>{{ end }}
    </li>
    {{ end }}
  </ul>
  {{ else }}
  <p>No posts yet.</p>
  {{ end }}
</div>
{{ end }}
```

- [ ] **Step 3: Create layouts/blog/list.html**

Same content as `layouts/index.html` — this handles `/blog/` URL directly:

```html
{{ define "main" }}
<div class="container">
  {{ $posts := .Pages }}
  {{ if $posts }}
  <ul class="post-list">
    {{ range $posts }}
    <li class="post-item">
      <span class="post-date">{{ .Date.Format "January 2006" }}</span>
      <h2 class="post-title"><a href="{{ .RelPermalink }}">{{ .Title }}</a></h2>
      {{ with .Description }}<p class="post-summary">{{ . }}</p>{{ end }}
    </li>
    {{ end }}
  </ul>
  {{ else }}
  <p>No posts yet.</p>
  {{ end }}
</div>
{{ end }}
```

- [ ] **Step 4: Create partials/share.html**

```html
<div class="share-links">
  Share:
  <a href="https://twitter.com/intent/tweet?text={{ .Title | urlquery }}&url={{ .Permalink | urlquery }}" target="_blank" rel="noopener">X</a>
  <a href="https://www.linkedin.com/sharing/share-offsite/?url={{ .Permalink | urlquery }}" target="_blank" rel="noopener">LinkedIn</a>
  <a href="#" onclick="navigator.clipboard.writeText('{{ .Permalink }}');this.textContent='Copied!';return false;">Copy link</a>
</div>
```

- [ ] **Step 5: Create partials/comments.html**

```html
{{ with .Site.Params.giscus }}
{{ if .repo }}
<div class="comments-section">
  <script src="https://giscus.app/client.js"
    data-repo="{{ .repo }}"
    data-repo-id="{{ .repoID }}"
    data-category="{{ .category }}"
    data-category-id="{{ .categoryID }}"
    data-mapping="{{ .mapping }}"
    data-strict="0"
    data-reactions-enabled="1"
    data-emit-metadata="0"
    data-input-position="top"
    data-theme="{{ .theme }}"
    data-lang="en"
    data-loading="lazy"
    crossorigin="anonymous"
    async>
  </script>
</div>
{{ end }}
{{ end }}
```

- [ ] **Step 6: Create layouts/blog/single.html**

```html
{{ define "main" }}
<div class="container">
  <article>
    <header class="post-header">
      <h1>{{ .Title }}</h1>
      <span class="post-date">{{ .Date.Format "January 2, 2006" }}</span>
    </header>
    <div class="post-content">
      {{ .Content }}
    </div>
    {{ partial "share.html" . }}
    {{ partial "comments.html" . }}
  </article>
</div>
{{ end }}
```

- [ ] **Step 7: Build check**

```bash
cd /Users/ethanwicko/Ewicko.github.io && hugo --printPathWarnings 2>&1
```

Expected: Build succeeds.

- [ ] **Step 8: Commit**

```bash
git add layouts/index.html layouts/blog/ layouts/partials/share.html layouts/partials/comments.html content/blog/
git commit -m "feat: add blog templates (homepage index, post, share, comments)"
```

---

### Task 5: Sample Blog Post (Lorem Ipsum)

**Files:**
- Create: `content/blog/lorem-ipsum-sample.md`

- [ ] **Step 1: Create the sample blog post**

This post demonstrates all supported features — headings, code blocks, math, images, blockquotes, and links.

```markdown
---
title: "Lorem Ipsum: A Sample Post"
date: 2026-03-18
description: "A sample blog post demonstrating all the features of this site — code blocks, math, images, and more."
draft: false
math: true
---

This is a sample blog post to demonstrate the formatting and features available on this blog. It covers everything from basic prose to code blocks, mathematical notation, and embedded media.

## Writing and Prose

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.

> "The purpose of computation is insight, not numbers." — Richard Hamming

Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.

## Code Blocks

Here's an example of a Python function with syntax highlighting:

```python
import numpy as np

def topology_optimize(domain, volume_fraction=0.5, penalty=3.0):
    """Run SIMP-based topology optimization on a 2D domain."""
    density = np.ones(domain.shape) * volume_fraction
    for iteration in range(200):
        ke = compute_element_stiffness(density, penalty)
        u = solve_fem(ke, domain.loads, domain.bcs)
        sensitivity = compute_sensitivity(u, ke, density, penalty)
        density = optimality_criteria_update(density, sensitivity, volume_fraction)
    return density
```

And some inline code: the `density` array stores per-element material distribution values between 0 and 1.

## Mathematics

The SIMP (Solid Isotropic Material with Penalization) method relates element stiffness to density:

$$E_e = E_0 \cdot \rho_e^p$$

where $E_e$ is the element Young's modulus, $E_0$ is the base material modulus, $\rho_e$ is the element density, and $p$ is the penalization factor (typically $p = 3$).

The optimization problem is formulated as:

$$\min_{\rho} \quad c(\rho) = \mathbf{U}^T \mathbf{K} \mathbf{U} = \sum_{e=1}^{N} (\rho_e)^p \mathbf{u}_e^T \mathbf{k}_0 \mathbf{u}_e$$

subject to:

$$\frac{V(\rho)}{V_0} = f, \quad 0 < \rho_{\min} \leq \rho_e \leq 1$$

## Images

Here's an example of an inline image (using an existing project image):

![Compliant mechanism design](/images/compliant-mechanism-topop/mechanism-business-card.png)

## Lists

Things I find interesting:

- Topology optimization and generative design
- Reinforcement learning for engineering applications
- Nonlinear finite element analysis
- Diffusion models for structural design

## Links

Some useful references:

1. [Bendsøe & Sigmund (2003)](https://link.springer.com/book/10.1007/978-3-662-05086-6) — the foundational text on topology optimization
2. [TopOpt group at DTU](https://www.topopt.mek.dtu.dk/) — excellent educational resources
3. [FEniCSx documentation](https://docs.fenicsproject.org/) — the FEA framework used in my solvers

---

*This is a sample post. Delete it when you publish your first real article.*
```

- [ ] **Step 2: Build and serve to verify**

```bash
cd /Users/ethanwicko/Ewicko.github.io && hugo server -D
```

Expected: Site builds and serves. Navigate to `http://localhost:1313/` to see the blog index with the sample post. Click through to verify the post renders correctly with code highlighting, math (KaTeX), and the image.

Press Ctrl+C to stop the server after verifying.

- [ ] **Step 3: Commit**

```bash
git add content/blog/lorem-ipsum-sample.md
git commit -m "feat: add sample blog post demonstrating all features"
```

---

### Task 6: Project Templates

**Files:**
- Create: `layouts/projects/list.html`
- Create: `layouts/projects/single.html`
- Create: `layouts/shortcodes/video.html`
- Create: `content/projects/_index.md`

- [ ] **Step 1: Create content/projects/_index.md**

```markdown
---
title: "Projects"
---
```

- [ ] **Step 2: Create layouts/shortcodes/video.html**

```html
<div class="video-embed">
  <video controls autoplay muted loop playsinline>
    <source src="{{ .Get "src" }}" type="video/mp4">
  </video>
</div>
```

- [ ] **Step 3: Create layouts/projects/list.html**

```html
{{ define "main" }}
<div class="container-wide">
  <h1>{{ .Title }}</h1>
  <div class="projects-grid">
    {{ range .Pages.ByWeight }}
    <a class="project-card" href="{{ if .Params.external_url }}{{ .Params.external_url }}{{ else }}{{ .RelPermalink }}{{ end }}"
       {{ if .Params.external_url }}target="_blank" rel="noopener"{{ end }}>
      {{ with .Params.thumbnail }}
      <img class="thumb" src="{{ . }}" alt="{{ .Title }}" loading="lazy" decoding="async">
      {{ end }}
      <div class="card-body">
        <div class="card-title">{{ .Title }}</div>
        <p class="card-desc">{{ .Description }}</p>
        <div class="project-tags">
          {{ range .Params.tags }}
          <span class="project-tag">{{ . }}</span>
          {{ end }}
        </div>
      </div>
    </a>
    {{ end }}
  </div>
</div>
{{ end }}
```

- [ ] **Step 4: Create layouts/projects/single.html**

```html
{{ define "main" }}
<div class="container-wide">
  <nav class="breadcrumb">
    <a href="/">Home</a> → <a href="/projects/">Projects</a> → {{ .Title }}
  </nav>

  <h1>{{ .Title }}</h1>

  <div class="project-meta">
    {{ with .Params.project_type }}<span>Type: {{ . }}</span>{{ end }}
    {{ with .Params.duration }}<span>Duration: {{ . }}</span>{{ end }}
    {{ with .Params.status }}<span>Status: {{ . }}</span>{{ end }}
  </div>

  <div class="post-content">
    {{ .Content }}
  </div>

  {{ with .Params.tags }}
  <div class="tech-stack">
    {{ range . }}<span class="tech-badge">{{ . }}</span>{{ end }}
  </div>
  {{ end }}

  <nav class="project-nav">
    {{ with .PrevInSection }}<a href="{{ .RelPermalink }}">← {{ .Title }}</a>{{ else }}<span></span>{{ end }}
    {{ with .NextInSection }}<a href="{{ .RelPermalink }}">{{ .Title }} →</a>{{ else }}<span></span>{{ end }}
  </nav>
</div>
{{ end }}
```

- [ ] **Step 5: Build check**

```bash
cd /Users/ethanwicko/Ewicko.github.io && hugo --printPathWarnings 2>&1
```

Expected: Build succeeds.

- [ ] **Step 6: Commit**

```bash
git add layouts/projects/ layouts/shortcodes/ content/projects/_index.md
git commit -m "feat: add project templates (grid, single, video shortcode)"
```

---

### Task 7: About Page & 404 Page

**Files:**
- Create: `content/about.md` (in `content/`, replacing nothing — the old root `about.md` is unrelated)
- Create: `layouts/page/single.html`
- Create: `layouts/404.html`

- [ ] **Step 1: Create layouts/page/single.html**

```html
{{ define "main" }}
<div class="container">
  <h1>{{ .Title }}</h1>
  <div class="post-content">
    {{ .Content }}
  </div>
</div>
{{ end }}
```

- [ ] **Step 2: Create content/about.md**

Source content from the current `index.html` about and contact sections:

```markdown
---
title: "About"
layout: "page"
type: "page"
---

I'm obsessed with translational research and accelerating engineering.

Currently, I'm working at Transcend Mechanics (the startup I founded) focused on compliant mechanisms, generative design, and high-performance multiphysics solvers. My generative design tools are accessible at [beta.deflex.ai](https://beta.deflex.ai), bringing state-of-the-art computational design to researchers and engineers everywhere.

My long term goal is to enable non-experts to generatively design novel multiphysics assemblies from a prompt.

## Contact

Email: [ethan.j.wicko@gmail.com](mailto:ethan.j.wicko@gmail.com) · LinkedIn: [ethanwicko](https://www.linkedin.com/in/ethanwicko/) · GitHub: [ewicko](https://github.com/ewicko)
```

- [ ] **Step 3: Create layouts/404.html**

```html
{{ define "main" }}
<div class="container not-found">
  <h1>Page not found</h1>
  <p>The page you're looking for doesn't exist. <a href="/">Go home</a>.</p>
</div>
{{ end }}
```

- [ ] **Step 4: Build check**

```bash
cd /Users/ethanwicko/Ewicko.github.io && hugo --printPathWarnings 2>&1
```

- [ ] **Step 5: Commit**

```bash
git add content/about.md layouts/page/ layouts/404.html
git commit -m "feat: add about page and custom 404"
```

---

### Task 8: Migrate Project Content — Compliant Mechanisms & FlexureDiff

**Files:**
- Create: `content/projects/compliant-mechanism-generative.md`
- Create: `content/projects/flexurediff.md`

- [ ] **Step 1: Create content/projects/compliant-mechanism-generative.md**

Convert the HTML content from `compliant-mechanism-generative.html` to Markdown. Include all sections: overview, solver inputs/outputs, technical implementation, obstacle avoidance, example designs, platform access, future development. Use the `{{</* video */>}}` shortcode for the mechanism video. Front matter:

```yaml
---
title: "Compliant Mechanism Generative Design"
description: "Topology-optimized compliant mechanisms with input and output ports."
type: "projects"
project_type: "Research Tool"
thumbnail: "/images/compliant-mechanism-topop/mechanism-business-card.png"
tags: ["Python", "FEniCSx", "MMA", "NumPy", "Matplotlib", "Paraview"]
duration: "Ongoing"
status: "Active"
weight: 1
math: false
---
```

Migrate all body content from the HTML page to Markdown, preserving section headings, descriptions, images, and the video. Reference images with their existing paths (e.g., `![alt](/images/compliant-mechanism-topop/filename.png)`). Use `{{</* video src="/images/compliant-mechanism-topop/mechanism_video.mp4" */>}}` for videos.

- [ ] **Step 2: Create content/projects/flexurediff.md**

Convert `flexurediff.html` to Markdown. This project has mathematical formulas — set `math: true` in front matter. Front matter:

```yaml
---
title: "FlexureDiff"
description: "Diffusion model for flexure generation based on MIT TopoDiff."
type: "projects"
project_type: "AI Research"
thumbnail: "/images/FlexureDiff/diffusion_main.png"
tags: ["PyTorch", "UNet", "TopoDiff", "Diffusion Models", "Spatial Encoding"]
duration: "Ongoing"
status: "In Development"
weight: 2
math: true
---
```

Convert all math formulas from the HTML subscript/superscript format to proper LaTeX notation using `$...$` and `$$...$$`. Migrate all sections: overview, technical implementation, spatial encoding, ablation study, mathematical formulation.

- [ ] **Step 3: Build check**

```bash
cd /Users/ethanwicko/Ewicko.github.io && hugo --printPathWarnings 2>&1
```

- [ ] **Step 4: Commit**

```bash
git add content/projects/compliant-mechanism-generative.md content/projects/flexurediff.md
git commit -m "feat: migrate compliant mechanisms and FlexureDiff project pages"
```

---

### Task 9: Migrate Project Content — Force Profile, ManuFix, SIAM, SpaceX

**Files:**
- Create: `content/projects/force-profile-flexures.md`
- Create: `content/projects/manufix.md`
- Create: `content/projects/siam-poster.md`
- Create: `content/projects/spacex.md`
- Create: `content/projects/older-projects.md`

- [ ] **Step 1: Create content/projects/force-profile-flexures.md**

Front matter:
```yaml
---
title: "Force Profile Flexures Generative Design"
description: "Generative design for flexures with custom force-displacement profiles."
type: "projects"
project_type: "Numerical Methods Research"
thumbnail: "/images/force-profile-flexures-generative-design/exponential-force-flexure.png"
tags: ["Python", "FEniCSx", "PETSc", "UFL", "MMA Optimizer", "Arc-Length FEA", "Nonlinear FEA"]
duration: "Ongoing"
status: "In Progress"
weight: 3
math: true
---
```

Migrate all content: overview, technical approach (profile matching, MMA sensitivities, multi-objective, nonlinear analysis, SIMP), custom arc-length solver, early results.

- [ ] **Step 2: Create content/projects/manufix.md**

Front matter:
```yaml
---
title: "ManuFix RL Voxel Editor"
description: "RL agent converts STL to 2.5-axis machinable geometry via PPO-trained 3D-CNN policy."
type: "projects"
project_type: "RL Research"
thumbnail: "/images/Manufix/manufix.png"
tags: ["PyTorch", "PPO", "3D-CNN", "CUDA"]
duration: "2 months"
status: "Proof of Concept"
weight: 4
math: false
---
```

Migrate all content. Use `{{</* video */>}}` shortcode for the two `.mp4` training videos.

- [ ] **Step 3: Create content/projects/siam-poster.md**

Front matter:
```yaml
---
title: "SIAM Conference Poster"
description: "Fast structural optimization using RL, presented at SIAM International Conference."
type: "projects"
project_type: "Conference Presentation"
thumbnail: "/images/compliant-generative-with-RL/Tile_SIAM.png"
tags: ["Presentation", "Pufferlib", "RL"]
duration: "2025"
status: "Presented"
weight: 5
math: false
---
```

- [ ] **Step 4: Create content/projects/spacex.md**

Front matter:
```yaml
---
title: "SpaceX"
description: "Projects on the Starship Program."
type: "projects"
project_type: "Industry"
thumbnail: "/images/spacex/spacex.png"
tags: ["Aerospace", "Starship"]
duration: "Completed"
status: "Completed"
weight: 6
math: false
---
```

- [ ] **Step 5: Create content/projects/older-projects.md**

This is the external link card — no standalone page. Front matter only:

```yaml
---
title: "Older Engineering Projects"
description: "Collection of previous engineering projects including transmissions, mechanical design, and more."
type: "projects"
thumbnail: "/images/older-projects/compliant-mechanism.png"
tags: ["Archive", "Portfolio"]
weight: 7
external_url: "https://glass-perigee-76c.notion.site/Ethan-Wicko-s-Engineering-Portfolio-894b94091ed54e4d88816af1fc0e7d37"
_build:
  render: false
  list: true
---
```

The `_build.render: false` prevents Hugo from generating a standalone page, while `list: true` ensures it shows up in the projects grid.

Note: the `_build` directive means this page won't have its own URL — the projects list template links to `external_url` instead.

- [ ] **Step 6: Build check**

```bash
cd /Users/ethanwicko/Ewicko.github.io && hugo --printPathWarnings 2>&1
```

- [ ] **Step 7: Commit**

```bash
git add content/projects/
git commit -m "feat: migrate remaining project pages (force profile, manufix, SIAM, SpaceX, older)"
```

---

### Task 10: GitHub Actions Deployment

**Files:**
- Create: `.github/workflows/deploy.yml`

- [ ] **Step 1: Create .github/workflows/deploy.yml**

```yaml
name: Deploy Hugo site to GitHub Pages

on:
  push:
    branches: ["main"]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: false

defaults:
  run:
    shell: bash

jobs:
  build:
    runs-on: ubuntu-latest
    env:
      HUGO_VERSION: 0.145.0
    steps:
      - name: Install Hugo CLI
        run: |
          wget -O ${{ runner.temp }}/hugo.deb https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_extended_${HUGO_VERSION}_linux-amd64.deb \
          && sudo dpkg -i ${{ runner.temp }}/hugo.deb
      - name: Checkout
        uses: actions/checkout@v4
        with:
          submodules: recursive
      - name: Setup Pages
        id: pages
        uses: actions/configure-pages@v5
      - name: Build with Hugo
        env:
          HUGO_CACHEDIR: ${{ runner.temp }}/hugo_cache
          HUGO_ENVIRONMENT: production
          TZ: America/New_York
        run: |
          hugo \
            --minify \
            --baseURL "${{ steps.pages.outputs.base_url }}/"
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./public

  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

- [ ] **Step 2: Build check**

```bash
cd /Users/ethanwicko/Ewicko.github.io && hugo --printPathWarnings 2>&1
```

- [ ] **Step 3: Commit**

```bash
git add .github/workflows/deploy.yml
git commit -m "feat: add GitHub Actions workflow for Hugo deployment"
```

---

### Task 11: Clean Up Old Files & Final Verification

**Files:**
- Remove: `index.html` (old homepage — Hugo generates its own)
- Remove: `compliant-mechanism-generative.html`
- Remove: `flexurediff.html`
- Remove: `force-profile-flexures.html`
- Remove: `manufix.html`
- Remove: `siam-poster.html`
- Remove: `spacex.html`
- Remove: `.nojekyll`
- Remove: root `about.md` (quantum mechanics content)
- Remove: root `CNAME` (moved to `static/CNAME`)
- Remove: root `robots.txt` (moved to `static/robots.txt`)
- Remove: root `favicon.ico` (moved to `static/`)
- Remove: root `favicon.png` (moved to `static/`)
- Remove: `README_old.md` (historical, no longer relevant)
- Keep: `README.md` (repo description)
- Keep: root `images/` directory (still needed as `static/images/` is a copy — OR remove root `images/` if `static/images/` has the content)

- [ ] **Step 1: Remove old HTML files**

```bash
cd /Users/ethanwicko/Ewicko.github.io
git rm index.html compliant-mechanism-generative.html flexurediff.html force-profile-flexures.html manufix.html siam-poster.html spacex.html .nojekyll about.md README_old.md
```

- [ ] **Step 2: Remove root-level files that were moved to static/**

```bash
cd /Users/ethanwicko/Ewicko.github.io
git rm CNAME robots.txt favicon.ico favicon.png
```

- [ ] **Step 3: Remove root images/ directory (now in static/images/)**

```bash
cd /Users/ethanwicko/Ewicko.github.io
git rm -r images/
```

- [ ] **Step 4: Final build and serve**

```bash
cd /Users/ethanwicko/Ewicko.github.io && hugo server -D
```

Walk through and verify:
- Homepage shows blog index with the lorem ipsum post
- Blog post renders with code highlighting, KaTeX math, and images
- `/projects/` shows the project grid with all 7 cards
- Each project page loads with content, images, and videos
- `/about/` shows the about page
- Navigation highlights the active section
- Footer shows RSS link
- Share links appear on blog posts
- Site is responsive at mobile widths

- [ ] **Step 5: Commit**

```bash
git add -A
git commit -m "chore: remove old static HTML files (replaced by Hugo)"
```

---

### Task 12: Configure GitHub Pages for Hugo

**Note:** This task involves repository settings changes that must be done manually or confirmed by the user.

- [ ] **Step 1: Verify GitHub Pages source**

The GitHub Pages deployment source must be changed from "Deploy from a branch" to "GitHub Actions." This is done in the repo settings:

1. Go to https://github.com/Ewicko/Ewicko.github.io/settings/pages
2. Under "Build and deployment" → Source, select **GitHub Actions**

Ask the user to confirm this is set correctly before pushing.

- [ ] **Step 2: Push to main**

```bash
git push origin main
```

- [ ] **Step 3: Monitor deployment**

```bash
gh run list --limit 3
```

Wait for the workflow to complete, then verify the live site at https://ethanwicko.com.

- [ ] **Step 4: Verify live site**

Check that:
- https://ethanwicko.com loads the blog index
- https://ethanwicko.com/blog/lorem-ipsum-sample/ shows the sample post
- https://ethanwicko.com/projects/ shows the project grid
- https://ethanwicko.com/about/ shows the about page
- https://ethanwicko.com/index.xml serves the RSS feed
