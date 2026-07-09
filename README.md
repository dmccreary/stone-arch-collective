# Stone Arch Collective

<p align="center">
  <img src="docs/img/logo.svg" alt="Stone Arch Collective" width="220">
</p>

[![MkDocs](https://img.shields.io/badge/Made%20with-MkDocs-526CFE?logo=materialformkdocs)](https://www.mkdocs.org/)
[![Material for MkDocs](https://img.shields.io/badge/Material%20for%20MkDocs-526CFE?logo=materialformkdocs)](https://squidfunk.github.io/mkdocs-material/)
[![GitHub Pages](https://img.shields.io/badge/View%20on-GitHub%20Pages-blue?logo=github)](https://dmccreary.github.io/stone-arch-collective/)
[![Claude Code](https://img.shields.io/badge/Built%20with-Claude%20Code-DA7857?logo=anthropic)](https://claude.ai/code)

## Live Site

[https://dmccreary.github.io/stone-arch-collective/](https://dmccreary.github.io/stone-arch-collective/)

## Overview

This repository holds the public website and brand identity for **Stone Arch
Collective**, built with MkDocs and the Material theme and deployed via
GitHub Pages. The site currently consists of a home page and a page telling
the story behind the Stone Arch Collective mark — what it depicts, why an
arch, and how the logo itself was rebuilt from a rough raster sketch into a
precise vector through an AI-assisted design process.

## What's Here

This is an early-stage brand site, not yet a full content site.

| Item | Detail |
|------|--------|
| Pages | 2 — Home, About the Logo |
| Logo assets | 2 SVGs (current mark + earlier revision), 2 raster PNGs, 1 multi-resolution favicon |
| Custom tooling | `plugins/social_override.py` — MkDocs hook that sets `og:image` / `twitter:image` from a page's frontmatter |

The About page documents how the mark was designed: reconstructed from a
low-resolution sketch into a scalable vector through an iterative,
conversational AI workflow.

## Getting Started

### Clone the Repository

```bash
git clone https://github.com/dmccreary/stone-arch-collective.git
cd stone-arch-collective
```

### Install Dependencies

This project uses MkDocs with the Material theme:

```bash
pip install mkdocs mkdocs-material
```

### Build and Serve Locally

```bash
mkdocs build
mkdocs serve
```

Then open `http://127.0.0.1:8000/stone-arch-collective/` in your browser.

### Deploy to GitHub Pages

```bash
mkdocs gh-deploy --force
```

This builds the site and pushes it to the `gh-pages` branch, which GitHub
Pages serves from.

## Repository Structure

```
stone-arch-collective/
├── docs/                       # MkDocs source
│   ├── index.md                # Home page (logo splash)
│   ├── about.md                # About the Logo — brand story
│   ├── css/
│   │   └── extra.css           # Site styling (logo sizing, etc.)
│   └── img/
│       ├── logo.svg            # Primary logo (header + favicon source art)
│       ├── logo-v1.svg         # Earlier logo revision
│       ├── low-res-logo.png    # Raster logo (social preview image)
│       ├── arch-only.png       # Arch mark only, no wordmark (favicon source)
│       └── favicon.ico         # Multi-resolution browser icon
├── plugins/
│   └── social_override.py      # MkDocs hook: sets og:image/twitter:image from page frontmatter
├── mkdocs.yml                  # Site configuration
└── README.md                   # This file
```

## Reporting Issues

Found a typo, broken link, or rendering issue?
[Open an issue](https://github.com/dmccreary/stone-arch-collective/issues).

## License

All rights reserved. The Stone Arch Collective name, logo, and site content
are not published under an open-source or Creative Commons license. Contact
the maintainer before reusing any assets from this repository.

## Acknowledgements

- **[MkDocs](https://www.mkdocs.org/)** — static site generator
- **[Material for MkDocs](https://squidfunk.github.io/mkdocs-material/)** — theme
- **[Claude Code](https://claude.ai/code)** by Anthropic — used to design and rebuild the logo as a scalable vector, and to build this site
- **[GitHub Pages](https://pages.github.com/)** — hosting

## Contact

**Dan McCreary**

- LinkedIn: [linkedin.com/in/danmccreary](https://www.linkedin.com/in/danmccreary/)
- GitHub: [@dmccreary](https://github.com/dmccreary)
