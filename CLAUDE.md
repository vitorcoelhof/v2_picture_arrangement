# CLAUDE.md

Guidance for Claude Code (claude.ai/code) when working in this repository. Updated 2026-11-14 (v2, in development).

## Project Overview

Single-file web app for civil engineers to build the photo annex (`.docx`) of a technical report (laudo). The user adds photos, orders them, writes captions, previews A4/Letter pages that match the `.docx`, and exports. It runs entirely in the browser: no server, no build step, nothing is uploaded.

This is **v2**, a separate project from `picture_arrangement` (no shared history). Do not change the old project from here.

## Running the App

Open an `.html` file directly in Chrome, Edge or Firefox. No install, no server. Works offline except for `docx.js` (CDN, cached after first load).

## Architecture

**Single-file constraint:** each `.html` holds HTML, CSS and JS. Do not split into files unless the user asks.

**Three variants:** `index.html` and `com senha.html` (identical, with license gate) and `sem_senha.html` (no gate). The app IIFE is the same in all three. **Apply every change to all three** and confirm `index.html` and `com senha.html` still have the same md5.

**Dependencies:** `docx.js` 7.8.2 (CDN). `libheif-js` embedded inline (wasm base64) for HEIC, which is why each file is ~1.9 MB.

**Data flow:**
1. Logo uploaded once, stored in `localStorage`.
2. Photos loaded in bulk. HEIC is detected by MIME and extension, then decoded to JPEG.
3. Captions edited in place.
4. `computePages()` splits the photos into pages. The page view and the `.docx` export both use its result, so sizes match.
5. "Gerar Documento" builds the `.docx`: one section per page.
6. Save/resume: `.anexo` file plus IndexedDB backup.

## Pagination rules (do not change without Vitor's approval)

- **Photos per page:** user-chosen, 1 to 8. The sidebar sets the default (4). Each page has its own "Fotos nesta página" selector (`pageSizes`). The count is tied to the **page position**, not to the photos. There is **no Automático mode**.
- **"Uma por página" flag (`solo`):** a flagged photo is alone on its page and uses the full usable area (no cap). Flagged photos do not count in the per-page number. A flag closes the previous group early and the group is not refilled from later photos.
- **Page cut by a flag:** has no setting of its own (selector disabled, "cortada por foto em destaque"). The page right after the flag keeps its own setting in `afterSoloSizes`, keyed by the flagged photo (`soloKey`). A setting equal to the default is deleted only if the hidden fallback gives the same number.
- **Rows:** photos in one row share one height, whatever the orientation. No crop, no gray bars, no visible border.
- **Captions:** never cut and no "legenda longa" warning. Only the page with a long caption shrinks. Caption min width of 45 mm applies only to the height estimate.
- **Last incomplete page:** uses the mode ceiling, not the shrunken size of another page.
- **Warnings:** amber chip below the minimum size (`minSize`, Pequeno/Médio/Grande, capped at 90% of the row height ceiling for the chosen count); red chip below 4 cm: "Foto abaixo de 4 cm. Use menos fotos nesta página ou encurte a legenda."
- **Page geometry (A4):** top 25 mm, bottom 18 mm, sides 20 mm, logo max 12 mm, fixed 5 mm slack, final paragraph of 1 pt. Table widths must not exceed the usable width (9638 twips on A4).
- **Novo projeto:** clears photos, `pageSizes` and `afterSoloSizes`, and resets the sidebar default to 4.
- **Aproveitamento da página (`fillFactor`):** the selector exists (75% to 95%) and is saved, but pagination does **not** read it yet. Planned: make it work again as an optional setting, default 100% (no change), reducing the row height ceiling, and make the minimum-size warning respect it. Update this section when done.

## Project persistence

`.anexo` is a binary container: `"ANEXOF01"` + uint32 LE manifest length + UTF-8 JSON manifest (settings, captions, order, flags, `pageSizes`, `afterSoloSizes`, image refs) + raw image bytes. `PROJECT_VERSION` is currently 1. Bump it if the manifest changes incompatibly. Invalid values (0, 9, non-numeric) are ignored on load. Old `.anexo` files (no flags, Automático) must keep opening, with 4 per page.

In Chrome/Edge the File System Access API lets "Salvar" overwrite the same file (Shift+click or Ctrl+Shift+S = save as); other browsers download. The IndexedDB backup (`anexoFotografico`: `photos` store by id, `meta` store key `session`) writes photos once and the small meta debounced.

## Testing

- Never edit a file while a regression is running.
- Test the three HTMLs. Check: no photo lost or reordered, no page over the usable area, same-row heights, flagged photos never share a page, image sizes in the `.docx` equal the page view, one section per page, PAGE/NUMPAGES footer.
- Known open items: not opened in real Word; JPEG stored as `.png` in `word/media` (docx.js 7.8.2); very narrow photos slightly distorted.
- No secrets, tokens or personal data in code, examples or docs.

## Feature Inventory

| ID | Feature | Status |
|----|---------|--------|
| F1 | Logo upload (PNG/JPG) | — |
| F2 | Watermark from same logo | — |
| F3 | `localStorage` persistence for logo | — |
| F4 | Bulk photo upload / folder drag-drop | — |
| F5 | Manual reordering via drag-drop | — |
| F6 | Auto sequential numbering | — |
| F7 | Editable caption per photo | — |
| F8 | Remove individual photos | — |
| F9 | Page layout, 1 to 8 photos per page (default 4) | ✅ v2 |
| F10 | Valid `.docx` output (Word 2016+) | ✅ structure checked, not opened in Word |
| F11 | Configurable output filename | — |
| F12 | Auto-download on generate | — |
| F13 | Caption prefix (Foto/Figura/Imagem) | — |
| F14 | Paper size (A4 default, Letter) | — |
| F15 | Configurable start number | ✅ |
| F16 | Any image format incl. `.heic/.heif` | ✅ |
| F17 | Save/resume: `.anexo` + IndexedDB backup | ✅ |
| F18 | "Uma por página" flag | ✅ v2 |
| F19 | Photos-per-page selector per page | ✅ v2 |
| F20 | Aproveitamento da página (optional) | in progress |

## Out of Scope

- PDF output, digital signatures, cloud integration
- Editing report body text (Word handles that)
- Desktop or mobile app
