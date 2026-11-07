# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

```bash
npm ci              # Install dependencies (use Node 24 for release parity)
npm run dev         # Watch mode build (inline sourcemaps, no minification)
npm run build       # Production build: typecheck + minified bundle
npm run lint        # ESLint with --max-warnings 0 (warnings are errors)
npm run typecheck   # tsc --noEmit --skipLibCheck
npm test            # Run all tests with vitest
npm run check       # Full gate: lint + typecheck + test + build + verify-release
npm run demo        # Build + copy plugin into demo vault (must commit demo changes)
```

Run a single test file:
```bash
npx vitest run tests/parser.test.ts
```

## Architecture

This is an **Obsidian plugin** (`Plugin` subclass in `src/main.ts`) that renders tabbed content from Markdown source. Three source syntaxes are supported:

- **Fenced**: ` ```tabsdown ... ``` ` code blocks
- **Markers**: `%% tabs %%` / `%% tab: Label %%` / `%% endtabs %%` Obsidian comment blocks
- **Htabs**: `%% htabs %%` / `%% endhtabs %%` — ATX headings define tab structure; no `%% tab: %%` lines needed. The first heading's level is the root level; same-level headings are sibling tabs, deeper headings become nested tabs (serialised as markers blocks internally). No config options apply; the context menu shows Add / Rename / Delete only.

### Data flow

```
Markdown source
  └─ src/syntax.ts        — commentMarkers(): finds %% markers outside literal contexts
  └─ src/parser.ts        — parseTabs(): produces ParsedTab[] + TabsdownConfig from source text;
                             dispatches to parseHeadingTabs() for htabs syntax, which converts
                             the heading hierarchy to ParsedTab[] (nested groups → markers blocks)
  └─ src/source.ts        — sourceBlocks(), locateBlock(), rewriteBlock(), rewriteTab()
                             Owns all source-level block location and editing logic.
                             BlockLocator (lineStart + nestedOffsets) identifies a block
                             precisely within a note's text.
  └─ src/render.ts        — TabBlockRenderChild: MarkdownRenderChild that owns the DOM
                             tab list/panel lifecycle, keyboard nav, label editing, delete
  └─ src/tabs.ts          — mountTabs(): standalone DOM helper (no Obsidian dependency)
                             also used by quartz-tabsdown publishing plugin
  └─ src/main.ts          — TabsdownPlugin: registers code block processor + marker reading/editing,
                             resolves LocatorRef → BlockLocator, manages vault writes
```

### Key design constraints

- **Markdown stays the source of truth**. All tab mutations go through `applySourceEdit` → `editor.replaceRange` or `vault.process`; never a direct DOM-to-source path.
- **No private Obsidian APIs, no runtime dependencies**. Only Obsidian's public API and bundled CM6 packages (already external in esbuild).
- **Production DOM uses `createEl`**. The exceptions are `src/label.ts` and `src/tabs.ts`, which must work outside Obsidian (for Quartz); tests use jsdom.
- `LocatorRef` is an indirect reference resolved against live editor text, supporting nested blocks that don't have their own `getSectionInfo`.
- **Htabs headings are the source of truth for tab identity**. `rewriteHtabTab()` edits heading lines directly; `htabMarkers()` locates root-level headings by byte offset in the inner view. Config-field submenus are suppressed for htabs and all their descendant blocks via `hideConfigFields` propagated through `renderFragment`.

### Source modules

| File | Role |
|---|---|
| `src/syntax.ts` | Marker parsing + CommonMark-aware context detection; `TabsSyntax` includes `"htabs"`; `CommentMarker.kind` includes `"htabs"` / `"endhtabs"` |
| `src/parser.ts` | `parseTabs()` — text → typed result; dispatches to `parseHeadingTabs()` for htabs; `parseInlineLabel()` for label tokens |
| `src/config.ts` | Config key/value types and parsing |
| `src/source.ts` | Block location, snapshot, rewrite; `htabMarkers()` locates root headings by offset; `rewriteHtabTab()` handles htabs Add/Rename/Delete |
| `src/render.ts` | `TabBlockRenderChild` — DOM construction, panel lifecycle, editing UI; accepts `syntax` to suppress config menus for htabs |
| `src/tabs.ts` | `mountTabs()` — DOM-only tabs without Obsidian (used by Quartz plugin) |
| `src/label.ts` | `renderLabel()` — renders inline label tokens to DOM; Quartz-compatible |
| `src/marker-reading.ts` | Reading View coordinator for marker-syntax and htabs blocks; relaxed coverage check for virtual scrolling |
| `src/marker-editor.ts` | Live Preview CM6 extension for inline marker editing |
| `src/block-settings.ts` | Context menu for block-level configuration; `hideConfigFields` suppresses style submenus for htabs |
| `src/panel-height.ts` | Tracks and animates panel height transitions |
| `src/separator.ts` | Overflow separator visibility tracking |

### Tests

Tests live in `tests/`, run with vitest + jsdom. `tests/obsidian.mock.ts` stubs the Obsidian module and patches `Node.prototype.createEl`. Tests **do not** mock the vault or editor — they exercise the real parsing and source-edit logic.

### Demo vault

`demo/` is a tracked Obsidian vault. After any source change, run `npm run demo` and commit everything under `demo/.obsidian/plugins/tabsdown/`. CI fails if the committed demo plugin doesn't match the current build.

### Releases

`npm run version -- <x.y.z>` bumps all four version files together. Releases are cut via the GitHub **Release** workflow (dry-run first, then live), never by hand-tagging.
