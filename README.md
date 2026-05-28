# NFC Bridge

A single-file, offline converter between **Flipper Zero `.nfc`** dumps and **Chameleon Ultra `.json`** slot files. MIFARE Classic 1K \& 4K, both directions.

Runs entirely in your browser — no server, no build step, no internet connection, no upload. Nothing leaves your machine.

**[▶ Open the live tool](https://bosund.github.io/NFC_bridge/)**

## Why

Flipper Zero and Chameleon Ultra both emulate MIFARE Classic, but they use different dump formats and neither imports the other's natively. Converting by hand is error-prone: a common failure is carrying over a stale slot buffer, where a 1K card ends up wrapped in a 4K-sized data structure padded with leftover garbage blocks. A reader activates the card but then rejects it during sector authentication (red light), even though the keys and anti-collision data are correct.

NFC Bridge writes **only** the blocks that belong to the declared card type — 64 for 1K, 256 for 4K — and drops anything beyond that boundary. The output is always internally consistent, so the type declaration (`tag` / `Mifare Classic type`) never disagrees with the data length.

## Usage

1. Open `index.html` in any modern browser (double-click it).
2. Pick a direction at the top:

   * **Flipper `.nfc` → Chameleon `.json`**
   * **Chameleon `.json` → Flipper `.nfc`**
3. Drag a file in, or click to choose one.
4. Review the parsed UID / SAK / ATQA / type and the block-by-block hex view.
5. Download the converted file, or copy it to the clipboard.

In the hex view, sector trailers are highlighted in amber. When converting a messy Chameleon JSON, any non-empty blocks beyond the card type's limit are shown in red and marked `DROPPED` — these are excluded from the output.

## Live version

If hosted via GitHub Pages, the tool is usable directly in the browser without downloading anything.

## Format details

**Flipper `.nfc`** is the standard `Filetype: Flipper NFC device` text format (Version 4). UID, ATQA, SAK and `Mifare Classic type` are read from the header; blocks are read from `Block N:` lines. `??` (unknown) bytes are converted to `00`.

**Chameleon Ultra `.json`** uses the ChameleonUltraGUI slot schema: `uid` (space-separated hex string), `sak` (integer), `atqa` (byte array), `ats`, `tag` (`1001` = MIFARE Classic 1K, `1002` = 4K), and `data` (array of 16-byte blocks). The round trip is byte-for-byte faithful for the blocks within the card type.

## Limitations

* MIFARE Classic 1K and 4K only — not Ultralight, DESFire, NTAG, etc.
* Assumes a 4-byte UID structure.
* Does not recover or crack keys; it only converts dump files you already have.

## Disclaimer

Intended for working with credentials you own or are authorized to test. You are responsible for complying with the laws and policies that apply to you.

## License

MIT

