# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

A single-page SVG converter tool that transforms Lucide (and other) SVG icons into FileMaker-compatible SVGs. The page is hosted on GitHub Pages and primarily used inside a FileMaker Pro web viewer.

## Architecture

The entire app is a single `index.html` file — no build step, no dependencies, no bundler. It contains inline `<style>`, HTML markup, and a `<script>` block.

**Conversion pipeline:** Parse input SVG → infer icon name from metadata → set explicit width/height (FileMaker requires intrinsic dimensions) → strip `<style>`, `class`, and `id` attributes → apply stroke color override → serialize back to string.

**FileMaker integration:** The page detects `typeof FileMaker !== 'undefined'` to determine if it's running inside a web viewer. When present:
- **Download** calls `FileMaker.PerformScript('Utility__ExportSVG', payload)` with JSON `{filename, svg}` — FileMaker sets a global field (`_MainMenu::zzg__Text_r20`) and uses `Export Field Contents`.
- **External links** call `FileMaker.PerformScript('Utility__OpenURL--External', payload)` with JSON `{Link: url}`.
- Both fall back to browser-native behavior (data URI download / `window.open`) outside FileMaker.

**Filename inference:** Extracts icon name from `lucide-*` CSS class, then `<title>`, then `aria-label`, then defaults to `icon`. Strips `-icon` suffix if present.

## Development

No build or test commands — edit `index.html` directly and open in a browser. Deploy by merging to `main` (GitHub Pages).

## Design System

Uses CSS custom properties prefixed `--` (e.g. `--blue`, `--bg-panel`). Component classes are prefixed `mc-` (e.g. `mc-btn`, `mc-panel`). Fonts: Space Mono (monospace body) and Chakra Petch (display/labels).
