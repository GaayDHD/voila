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
│ • Copy (⌘⇧C anywhere), or download a script that does the whole job    │
│ • The script finds open Office apps, quits, writes, verifies, reopens  │
│ • Backs up the previous list every run — restore it with --undo        │
│ • Save / load / export / import named presets (localStorage)           │
╰────────────────────────────────────────────────────────────────────────╯

╭─ UI ───────────────────────────────────────────────────────────────────╮
│ • Automatic light / dark mode                                          │
│ • Mobile responsive down to phone widths                               │
│ • Atkinson Hyperlegible typography · 100% client-side                  │
╰────────────────────────────────────────────────────────────────────────╯
```

## ✦ The command it builds

Office keeps its preferences in memory and flushes them to disk when it quits,
so a `defaults write` made while an app is running can be overwritten by that
flush. The generated command therefore **quits first, then writes**, and reads
the value back so you can see it landed:

```bash
osascript -e 'repeat with a in {"Microsoft Word","Microsoft PowerPoint"}' \
          -e 'set n to a as text' \
          -e 'if application n is running then tell application n to quit' \
          -e 'end repeat' \
  && defaults write com.microsoft.office PrioritizedFonts -array \
       "MyFont-Regular" "MyFont-Bold" "MyFont-Italic" \
  && defaults read com.microsoft.office PrioritizedFonts
```

The quit is guarded by `is running` because a bare `tell application X to quit`
would *launch* an app that was closed.

Note that this command **does not perform the open → quit → reopen cycle**
described below — it only sets the preference. Use the downloadable script if
you want the fonts to take effect without doing that by hand.

> **macOS only** — the `defaults` / `PrioritizedFonts` mechanism is specific
> to Microsoft Office for Mac. The command has no effect on Office for Windows.

## ✦ How to apply

Copy the command straight into Terminal, or download the script and run it.

```
╭─ HOW TO APPLY ─────────────────────────────────────────────────────────╮
│ 1.  Download the script                                                │
│ 2.  Run the command shown under the download button                    │
│ 3.  Answer the prompt — it handles the whole restart cycle for you     │
╰────────────────────────────────────────────────────────────────────────╯
```

### Why not just double-click it?

Because it will not work, and no change to this tool can make it work. A file
downloaded by a browser is always written `-rw-r--r--`; there is no web API
that can set the execute bit. Double-clicking therefore reports:

> The file "prioritise-fonts.sh" could not be executed because you do not have
> appropriate access privileges.

Passing the file to `zsh` reads it as *data* rather than executing it, so it
needs neither `chmod` nor any change to security settings — and it is
unaffected by the quarantine flag Safari and Firefox attach to downloads.

```bash
zsh "$HOME/Downloads/prioritise-fonts-20260823-041836.sh"
```

Each download gets a unique timestamped name. That matters: with a fixed name,
a second download lands as `prioritise-fonts(2).sh`, and in zsh the parentheses
are glob syntax — an unquoted path then fails with `no matches found`. The app
shows the exact command for the file you just downloaded, already quoted.

### What the script does

Unlike the one-liner, the script can look at the machine it is running on:

```
╭─ THE SCRIPT ───────────────────────────────────────────────────────────╮
│ 1.  Shows the current list and the list you asked for                  │
│ 2.  Warns about names that match no installed font                     │
│ 3.  Detects which Office apps are actually open                        │
│ 4.  Asks before quitting anything                                      │
│ 5.  Quits, waits for a real exit (save dialogs included), then writes  │
│ 6.  Reads the value back and verifies it matches                       │
│ 7.  Runs the refresh cycle: open → quit → open                         │
│ 8.  Confirms the value survived, and rewrites it if Office changed it  │
╰────────────────────────────────────────────────────────────────────────╯
```

### Why the open → quit → reopen cycle

Writing the preference is not enough on its own. Office reads `PrioritizedFonts`
at launch and rebuilds its font cache, but the rebuilt cache only takes effect
on the *next* launch — so the app has to be opened, quit, and opened again
before the new order shows up. That is the manual ritual this tool exists to
remove, and the script now performs it for you, with progress printed as it
goes. Expect it to take about a minute per app.

The machine is left as it was found: apps that were running are reopened, and
if nothing was running, one app is cycled to prime the cache and then closed
again. Documents are not restored — reopen them from **File ▸ Open Recent**.

Because Office rewrites its preferences as it quits, the script re-reads the
value after the cycle and rewrites it if Office changed it.

| Flag | Effect |
| --- | --- |
| `--check` | Report current state and exit; changes nothing |
| `--yes` | Skip the confirmation prompt |
| `--undo` | Restore the previous list from the newest backup |
| `--no-cycle` | Write the preference but skip the restart cycle |
| `--settle N` | Seconds to let Office settle while open (default 8) |

Every run backs up the previous list to `~/.voila-font-backups/` first, so
`--undo` always has something to restore.

**On app detection.** The script matches an explicit allowlist of Office bundle
ids, never a `com.microsoft.*` prefix — VS Code (`com.microsoft.VSCode`),
OneDrive, Defender, AutoUpdate and Windows App all share that prefix. Note that
PowerPoint's bundle id is lowercase-p `com.microsoft.Powerpoint`.

**On automation permission.** Quitting an app via AppleScript requires your
terminal to hold Automation permission for it. The first run shows a "Terminal
wants to control Microsoft Word" prompt; if it was ever denied the quit fails
silently, so the script checks and tells you where to re-enable it.

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
