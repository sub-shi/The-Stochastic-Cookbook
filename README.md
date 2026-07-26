# The Stochastic Cookbook

A MkDocs-based project containing recipes for simulating stochastic processes and Gaussian models.

## Overview

This repository presents a collection of stochastic process recipes for quantitative modeling, including:

- Geometric Brownian Motion
- Ornstein-Uhlenbeck Process
- Cox-Ingersoll-Ross Process
- Heston Stochastic Volatility Model
- Vector Brownian Motion

The content is published as a static documentation site using MkDocs Material.

### Public Site
```powershell
https://thestochasticcookbook.netlify.app/
```

## Getting Started

### Prerequisites

- Python 3.8+ (recommended)
- `pip` package manager

### Install dependencies

```powershell
python -m pip install mkdocs mkdocs-material pymdown-extensions
```

### Run locally

```powershell
mkdocs serve
```

Then open `http://127.0.0.1:8000` in your browser.

### Build the site

```powershell
mkdocs build
```

The generated site output will be placed in the `site/` directory.

## Repository Structure

- `mkdocs.yml` — MkDocs configuration and navigation
- `docs/` — Markdown source files and site assets
  - `index.md` — Home page for the documentation site
  - `resources.md` — Links to project resources and social profiles
  - `assets/` — Static assets used by the site
  - `javascript/` — custom JavaScript support files
  - `recipes/` — stochastic process recipe pages
  - `stylesheets/` — custom CSS styles
- `scripts/` — Python package support code (if any)
- `venv/` — local virtual environment (ignored by `.gitignore`)

## Notes

- The site uses MkDocs Material theme with custom CSS and JavaScript.
- The project is styled for educational recipes in stochastic processes and simulation.

## Resources

- [GitHub](https://github.com/sub-shi)
- [YouTube](https://youtube.com)
- [Medium](https://medium.com/@subratsethi9910)
- [LinkedIn](https://linkedin.com/in/subrat-sethi-0a7934208)
