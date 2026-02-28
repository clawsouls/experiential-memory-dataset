# Hugo Static Site Setup

## Installation & Init

```bash
brew install hugo          # macOS
hugo new site my-blog
cd my-blog
git init
```

## Directory Structure

```
my-blog/
├── archetypes/        # Content templates
├── content/           # Markdown posts
│   └── posts/
├── data/              # Data files (JSON, YAML, TOML)
├── layouts/           # Custom templates (override theme)
├── static/            # Static assets (images, CSS, JS)
├── themes/            # Installed themes
├── hugo.toml          # Site configuration
└── public/            # Generated output (gitignored)
```

## Theme Installation

```bash
git submodule add https://github.com/author/theme-name themes/theme-name
```

In `hugo.toml`:
```toml
theme = "theme-name"
```

Popular themes: PaperMod, Blowfish, Congo, Stack.

## Configuration (hugo.toml)

```toml
baseURL = "https://example.com/"
languageCode = "en-us"
title = "My Blog"
theme = "PaperMod"

[params]
  description = "A blog about things"
  author = "Author Name"
  ShowReadingTime = true
  ShowShareButtons = true

[markup.goldmark.renderer]
  unsafe = true  # allows raw HTML in markdown

[outputs]
  home = ["HTML", "RSS", "JSON"]  # JSON for search
```

## Content Creation

```bash
hugo new posts/my-first-post.md
```

Front matter (YAML):
```yaml
---
title: "My First Post"
date: 2024-01-15
draft: false
tags: ["tutorial", "hugo"]
categories: ["web"]
description: "A brief intro to Hugo"
cover:
  image: "images/cover.jpg"
  alt: "Cover image"
---
```

## Shortcodes

Hugo provides built-in shortcodes and supports custom ones:
```markdown
{{</* youtube dQw4w9WgXcQ */>}}
{{</* figure src="/images/photo.jpg" title="Caption" */>}}
{{</* tweet user="username" id="123456789" */>}}
```

Custom shortcodes go in `layouts/shortcodes/`.

## Building & Serving

```bash
hugo server -D        # local dev with drafts
hugo server --bind 0.0.0.0  # expose to network
hugo                  # build to public/
hugo --minify         # production build with minification
```

## Deployment

### Netlify / Vercel
Drop-in support. Set build command to `hugo --minify` and publish directory to `public`.

### GitHub Pages
Use GitHub Actions:
```yaml
- uses: peaceiris/actions-hugo@v2
  with:
    hugo-version: 'latest'
- run: hugo --minify
- uses: peaceiris/actions-gh-pages@v3
  with:
    github_token: ${{ secrets.GITHUB_TOKEN }}
    publish_dir: ./public
```

## Taxonomies

Hugo supports taxonomies (tags, categories) out of the box. Define custom ones:
```toml
[taxonomies]
  tag = "tags"
  category = "categories"
  series = "series"
```

## Multilingual

```toml
defaultContentLanguage = "en"
[languages.en]
  weight = 1
  title = "My Blog"
[languages.ko]
  weight = 2
  title = "내 블로그"
```

Content files: `post.md` (default) and `post.ko.md` (Korean).
