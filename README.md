```
╔═══════════════════════════════════════════════╗
║                                               ║
║   ██╗   ██╗  ██████╗  ██╗ ██╗       █████╗    ║
║   ██║   ██║ ██╔═══██╗ ██║ ██║      ██╔══██╗   ║
║   ██║   ██║ ██║   ██║ ██║ ██║      ███████║   ║
║   ╚██╗ ██╔╝ ██║   ██║ ██║ ██║      ██╔══██║   ║
║    ╚████╔╝  ╚██████╔╝ ██║ ███████╗ ██║  ██║   ║
║     ╚═══╝    ╚═════╝  ╚═╝ ╚══════╝ ╚═╝  ╚═╝   ║
║                                               ║
║        F O N T   P R I O R I T I S E R        ║
║                                               ║
╚═══════════════════════════════════════════════╝
```

> A **client-side** utility that makes Microsoft Office on macOS prefer the
> fonts *you* choose. Drop in your `.otf` / `.ttf` files, drag them into
> priority order, and Voilà builds a ready-to-run Terminal command that
> writes your list into Office's `PrioritizedFonts` preference.

<p align="center">
  <a href="https://voila-black.vercel.app"><b>&#9654; Live demo</b></a>
  &nbsp;&middot;&nbsp; Vanilla JS &nbsp;&middot;&nbsp; opentype.js &nbsp;&middot;&nbsp; Vite &nbsp;&middot;&nbsp; macOS only &nbsp;&middot;&nbsp; zero uploads
</p>

---

## ✦ What it does

```
┌────────────────────────────────────────────────────────────────────────┐
│                                                                        │
│    .otf / .ttf      ┌──────────────────┐      ╔════════════════════╗   │
│    .ttc / .otc  ──► │ read PostScript  │  ──► ║  defaults write …  ║   │
│    dropped in       │ names in-browser │      ║  PrioritizedFonts  ║   │
│    the browser      └──────────────────┘      ╚════════════════════╝   │
│                                                                        │
│    Nothing ever leaves your machine — font bytes are parsed entirely   │
│    client-side and never uploaded to any server.                       │
│                                                                        │
└────────────────────────────────────────────────────────────────────────┘
```

Office for macOS reads a `PrioritizedFonts` array from its preferences and
favours those fonts when picking defaults. Voilà extracts the exact
PostScript names from your font files, lets you order them, and assembles
the `defaults write` command that sets that array — no manual typing of
fiddly font names required.

## ✦ Features

```
╭─ INPUT ────────────────────────────────────────────────────────────────╮
│ • Drag-and-drop or pick local .otf / .ttf / .ttc / .otc files          │
│ • Reads each font's PostScript name from its name table                │
│ • Up to 50 fonts; duplicates are removed automatically                 │
╰────────────────────────────────────────────────────────────────────────╯

╭─ PRIORITISE ───────────────────────────────────────────────────────────╮
│ • Drag the list to set priority order (top = highest)                  │
│ • Remove one font, Clear all, or Undo the last change                  │
│ • Order maps 1:1 to the order Office will prefer fonts                 │
╰────────────────────────────────────────────────────────────────────────╯

╭─ OUTPUT ───────────────────────────────────────────────────────────────╮
│ • Live, syntax-highlighted Terminal command as you edit                │
│ • Copy (⌘⇧C anywhere) or Download a runnable .sh script                │
│ • Save / load / export / import named presets (localStorage)           │
╰────────────────────────────────────────────────────────────────────────╯

╭─ UI ───────────────────────────────────────────────────────────────────╮
│ • Automatic light / dark mode                                          │
│ • Mobile responsive down to phone widths                               │
│ • Atkinson Hyperlegible typography · 100% client-side                  │
╰────────────────────────────────────────────────────────────────────────╯
```

## ✦ The command it builds

The generated command writes your ordered list to Office's preferences and
quits the apps so the change is picked up on next launch:

```bash
defaults write com.microsoft.office PrioritizedFonts -array \
  "MyFont-Regular" "MyFont-Bold" "MyFont-Italic" \
  && osascript -e 'tell application "Microsoft Word" to quit' \
  && osascript -e 'tell application "Microsoft PowerPoint" to quit'
```

> **macOS only** — the `defaults` / `PrioritizedFonts` mechanism is specific
> to Microsoft Office for Mac. The command has no effect on Office for Windows.

## ✦ How to apply

```
╭─ HOW TO APPLY ─────────────────────────────────────────────────────────╮
│ 1.  Quit Word and PowerPoint completely                                │
│ 2.  Run the generated command in Terminal                              │
│ 3.  Open Office → quit → reopen once to refresh                        │
╰────────────────────────────────────────────────────────────────────────╯
```

## ✦ Quick start

```bash
npm install      # install dependencies
npm run dev      # Vite dev server   ─►  http://localhost:5173
npm run build    # production build into dist/
npm run preview  # serve the production build locally
```

> **Tip** — the dev/preview servers are also defined in
> `.claude/launch.json` (pinned to ports `5173` / `4173`).

## ✦ Project structure

```
voila/
├── index.html        ◄─ the entire app: markup + CSS + JS, one file
├── public/fonts/     ◄─ Atkinson Hyperlegible Next + Mono web fonts
├── public/vendor/    ◄─ self-hosted opentype.js + SortableJS
├── vercel.json       ◄─ Vite framework + SPA rewrite config
├── package.json
└── README.md
```

## ✦ Tech stack

```
┌───────────────┐  ┌───────────────┐  ┌───────────────┐  ┌───────────────┐
│   Vanilla JS  │  │  opentype.js  │  │   SortableJS  │  │      Vite     │
│  no framework │  │name-table read│  │  drag reorder │  │ dev + bundler │
└───────────────┘  └───────────────┘  └───────────────┘  └───────────────┘
```

[opentype.js](https://github.com/opentypejs/opentype.js) parses the font
binaries; [SortableJS](https://github.com/SortableJS/Sortable) powers the
drag-to-reorder list. Both are vendored under `public/vendor/`.

## ✦ Deployment

Hosted on **Vercel**, deployed automatically on every push to `main` via the
GitHub integration.

```
git push ─► main ─► Vercel build ─► voila-black.vercel.app
```

---

<p align="center"><sub>Built client-side — your fonts never leave the browser.</sub></p>
