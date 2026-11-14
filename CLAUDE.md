# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a single-file web application for civil engineers to generate formatted photo annexes (`.docx`) for technical inspection reports (laudos técnicos). It runs entirely in the browser with no server or build step required.

## Running the App

Open the main `.html` file directly in Chrome, Edge, or Firefox — no build step, no installation, no server needed. The app works fully offline.

## Architecture

**Single-file constraint:** The entire application is one `.html` file. JavaScript, CSS, and HTML all live together. Do not split into separate files unless the user explicitly requests it.

**Key dependencies (loaded via CDN, no npm):**
- `docx.js` — client-side `.docx` generation (loaded via CDN)
- `libheif-js` — `.heic/.heif` decoding, **embedded inline** in each `.html` (single-file build with the wasm as base64) so HEIC conversion works fully offline. This is why each `.html` is ~1.8 MB.

**Data flow:**
1. Logo uploaded once → stored in `localStorage` (persists across sessions)
2. Photos loaded in bulk → any `image/*` plus `.heic/.heif` accepted (HEIC detected by MIME **and** extension, since iPhone files often have an empty `file.type`); HEIC/HEIF are decoded to JPEG via `libheif` before being held in memory
3. User edits captions on each photo in-place
4. On "Gerar Documento" → `docx.js` builds a `.docx` and triggers browser download
5. Work can be resumed later (F17): **Salvar** writes a `.anexo` project file, **Abrir** loads it back, and the current session is also auto-backed-up to IndexedDB (restored via a "Continuar de onde parei" banner on next open)

**Document layout (F9):** 2-column × 2-row photo grid per page, with captions below each photo, logo in the page header, and the logo reused as a low-opacity watermark background.

Layout is computed once per document by `computeLayout()` (same max photo box on every page, reserving space for the longest caption) and shared by the preview and the export. Each photo row is its own centered, fixed-layout table `[photo | gap | photo]` whose columns equal the image widths, so the horizontal gap (`state.gapCm`) is identical everywhere. The gallery cards show each photo uncropped inside a frame with the docx slot's aspect ratio, plus its size in cm.

**Project persistence (F17):** `.anexo` is a binary container — `"ANEXOF01"` + uint32 LE manifest length + UTF-8 JSON manifest (settings, captions, order, image refs) + concatenated raw image bytes (no base64). Bump `PROJECT_VERSION` if the manifest changes incompatibly. In Chrome/Edge the File System Access API is used, so "Salvar" overwrites the same file after the first save (Shift+click / Ctrl+Shift+S = save as); other browsers fall back to a download. The IndexedDB backup (`anexoFotografico` DB: `photos` store keyed by id, `meta` store with key `session`) is written incrementally — photos once on add, the small meta debounced on every change — so typing captions never rewrites image data.

**Three HTML variants:** `index.html` and `com senha.html` (identical, with license gate) and `sem_senha.html` (no gate). The app IIFE is the same in all three — apply every change to all of them.

## Feature Inventory (from PRD)

| ID | Feature | Status |
|----|---------|--------|
| F1 | Logo upload (PNG/JPG) | — |
| F2 | Auto watermark from same logo | — |
| F3 | `localStorage` persistence for logo | — |
| F4 | Bulk photo upload / folder drag-drop | — |
| F5 | Manual reordering via drag-drop | — |
| F6 | Auto sequential numbering | — |
| F7 | Editable caption per photo | — |
| F8 | Remove individual photos | — |
| F9 | 2×2 grid layout in generated doc | — |
| F10 | Valid `.docx` output (Word 2016+) | — |
| F11 | Configurable output filename | — |
| F12 | Auto-download on generate | — |
| F13 | Caption prefix config (Foto/Figura/Imagem) | — |
| F14 | Paper size (A4 default, Letter) | — |
| F15 | Configurable start number for the sequence | ✅ |
| F16 | Any image format incl. `.heic/.heif` (iPhone HDR) via embedded `libheif` | ✅ |
| F17 | Save/resume work: `.anexo` project file + automatic IndexedDB backup | ✅ |

MVP priority order (from PRD §10): F4 → F6 → F7 → F9 → F10, then F1/F2/F3, then F5/F13/F14.

## Out of Scope

- PDF output, digital signatures, cloud integration
- Editing report body text (Word handles that)
- Desktop or mobile app — browser only
