# Office Font Prioritiser

## Overview

**Office Font Prioritiser** is a web‑based utility designed to help macOS users take control of how Microsoft Office (Word and PowerPoint) choose fonts. The app extracts the exact PostScript names from your font files, deduplicates them and builds a ready‑to‑run Terminal command that instructs Office to favour your chosen fonts over its defaults. Everything runs client‑side, so none of your font data ever leaves your browser.

## Features

- **Drag‑and‑drop font import.** Drop up to 50 `.otf`/`.ttf` files into the page to extract their PostScript names. Font collections (`.ttc`/`.otc`) can be processed via manual mode.
- **Automatic deduplication.** Duplicate names are removed automatically, ensuring you only add each font once.
- **Manual mode.** Paste PostScript names directly if you already know them. You can also merge manually entered names with extracted ones.
- **Reorder and sort.** Use drag‑and‑drop to reorder names, or click to automatically sort by weight so that lighter styles come first.
- **Preset management.** Save your current list of PostScript names as a preset in local storage. Presets can be renamed, deleted, exported to JSON and imported later.
- **Real‑time command generation.** As you edit the list, the tool generates an `defaults write` command you can copy and run in Terminal to set Office’s font priority list. A convenient copy button is provided.
- **No server backend.** The app is a single‑page HTML file built with vanilla JavaScript and uses [opentype.js](https://github.com/opentypejs/opentype.js) for parsing font files and [SortableJS](https://github.com/SortableJS/Sortable) for drag‑and‑drop functionality.
- **macOS‑only utility.** Microsoft’s `defaults` system for font priorities is macOS‑specific; the generated command will not affect Office on Windows.

## How It Works

Office for macOS allows you to specify a list of font PostScript names that it should prioritise when selecting fonts. This tool helps you compile that list. When you drop a font file onto the page, it reads the file in your browser, extracts the PostScript name from the font’s name table and adds it to a sortable list. You can reorder the names or remove items entirely. The resulting list is formatted into a `defaults write com.microsoft.office OfficeFontPreference -array ...` command. Running this command in Terminal writes your custom list into Office’s preferences. To apply the change, quit Word and PowerPoint completely before running the command.

## Installation

This project is designed to be served as a static site. You can run it locally or host it on GitHub Pages.

1. Clone or download this repository:
   ```sh
   git clone https://github.com/GaayDHD/office-font-prioritiser.git
   ```
2. Open `index.html` directly in your browser or serve it via a static server (for example, using `python -m http.server`).

If you prefer not to clone the repo, you can simply visit the live site:
<https://gaaydhd.github.io/office-font-prioritiser/>

## Usage

1. Open the web page on a macOS device running Microsoft Office.
2. Drop up to 50 `.otf`/`.ttf` font files into the upload area or click **Choose fonts** and select them from Finder.
3. The app will extract each font’s PostScript name and list it. Use drag‑and‑drop to reorder or click **Sort by weight** to automatically sort styles.
4. To add names manually, click **Manual mode**, type the PostScript names (one per line) and merge them with the existing list if required.
5. Optionally create a preset by entering a name and clicking **Save**. Presets are stored locally and can be exported/imported as JSON.
6. Copy the generated command using the **Copy** button. Then quit Word and PowerPoint completely.
7. Open Terminal and paste the command. Press **Return** to run it. The next time you launch Word or PowerPoint, your chosen fonts will be prioritised.

## Development

The app is a single‑page HTML document with embedded CSS and JavaScript. External dependencies include:

- **opentype.js** for reading font metadata.
- **SortableJS** for drag‑and‑drop list reordering.

To modify the app:

1. Edit `index.html` directly. The CSS is defined inside a `<style>` tag and the JavaScript at the bottom of the file.
2. Use any local server to test changes. Some browsers restrict font file access from `file://` origins.
3. When finished, commit your changes and push to GitHub. If the site is hosted via GitHub Pages, the page will update automatically when new commits land on the `main` branch.

## Contributing

Contributions are welcome! If you encounter a bug or have a feature request, feel free to open an issue. Pull requests that improve the UI, add compatibility features or enhance performance are encouraged. Please ensure that changes are compatible with macOS and do not introduce any server‑side processing.

## License

This project is released under the MIT License. See [LICENSE](LICENSE) for details.
