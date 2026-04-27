# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

`java-labs` is an educational documentation site about Java, published to GitHub Pages at `https://caramelotech.github.io/java-labs`. It is built with [Astro](https://astro.build/) + [Starlight](https://starlight.astro.build/) and contains no application code - only Markdown/MDX content and runnable Java examples.

## Commands

```bash
npm run dev      # Start local dev server (localhost:4321)
npm run build    # Build static site to dist/
npm run preview  # Preview production build locally
```

Deploy to GitHub Pages happens automatically on push to `main` via `.github/workflows/deploy.yml`. No manual deploy step exists.

## Structure

- `src/content/docs/` - All documentation pages (Markdown/MDX). Each file maps to a URL under `/java-labs/`.
- `src/content/config.ts` - Astro content collection schema. Docs support optional `tags` and `date` frontmatter fields beyond Starlight defaults.
- `src/styles/custom.css` - Global CSS overrides for Starlight.
- `astro.config.mjs` - Site config: base path `/java-labs`, locale `pt-BR`, GitHub social link.
- `examples/` - Standalone Java source files (`.java`) with their own `README.md`. These are not built by Astro - they are meant to be compiled and run directly with `javac`/`java`.

## Content conventions

- All content is in **Brazilian Portuguese**.
- Doc filenames follow the pattern `NN-slug.md` (e.g. `01-introducao-java.md`). The `sidebar.order` frontmatter controls ordering when set explicitly.
- The `roadmap.md` file uses `sidebar.order: 99` to keep it at the bottom.
- Internal links between docs use the Starlight-relative path `/java-labs/<slug>/`.
