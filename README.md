# FilePeek

FilePeek is a single-file, browser-based inspection tool for examining, previewing, and editing files directly in your browser. All analysis and transformations run locally on the client side using Web APIs and client-side JavaScript libraries. No file data is uploaded to a remote server.

## What It Does

When you drop a file (or supply a CORS-enabled public URL), FilePeek analyzes the file headers, calculates integrity metrics, and opens an inspection view specific to that format:

* **Header Analysis & Format Identification**: Reads the first 16 bytes and tests against known magic signatures (PNG, JPEG, GIF, RIFF/WebP/WAV/AVI, PDF, ZIP/Office Open XML, PE/EXE, ELF, GZIP, BZIP2, TAR, 7z, Ogg, WebM/Matroska, MP3 ID3v2, WOFF, WOFF2, TTF, OTF). It warns if the detected signature contradicts the file extension.
* **Integrity Hashes**: Computes SHA-256 and SHA-1 checksums via the Web Cryptography API (`crypto.subtle`).
* **Shannon Entropy**: Calculates byte entropy (0 to 8 bits/byte) over the first 64 KB to indicate whether the file is likely plain text/code, repetitive/sparse, or compressed/encrypted, accompanied by a 256-bin byte frequency histogram.
* **Base64 Data URI**: Encodes files up to 512 KB into copyable data URIs.
* **Images**: Reads and displays EXIF tags (via ExifReader), extracts a 6-color dominant palette, previews the image, strips EXIF metadata via canvas re-encoding, and converts between PNG, JPEG, and WebP.
* **PDFs**: Renders page previews through PDF.js, provides page-by-page navigation, and exports extracted text layers as `.txt`.
* **Audio & Video**: Renders waveform visualisations for audio streams via Web Audio API, supports native playback, and extracts video frames at current playback time as PNG snapshots.
* **Archives (ZIP, JAR, APK, Office documents)**: Reads archive file listings via JSZip and extracts individual uncompressed files.
* **Vector Graphics (SVG)**: Counts elements and paths, previews the graphic, edits raw SVG XML in edit mode, and generates a minified SVG file.
* **Fonts (TTF, OTF, WOFF, WOFF2)**: Dynamically loads the font into the document via `FontFace` and provides a live typography tester with an adjustable size slider.
* **Delimited Tables (CSV, TSV)**: Parses and renders tabular rows (previewing up to 100 rows) and exports table contents as JSON.
* **JSON**: Validates formatting, pretty-prints structures, provides an in-browser source editor, and converts flat object arrays to CSV.
* **Plain Text & Code**: Displays line and character counts, provides an editable textarea in edit mode, and previews text content.
* **Binary Files**: Generates a paginated hex dump, searches byte sequences or text patterns, and extracts printable ASCII strings (minimum length of 4 characters).
* **Hex Byte Editor**: Allows editing individual bytes in the first 1 KB of any binary or media file with bidirectional keyboard navigation, updating the ASCII column in real time, and downloading the modified binary file.
* **Diff Comparison**: Compares the currently opened file side-by-side against a second file (line-by-line text diff for textual files, or offset-by-offset hex diff for binary files).

## Limitations

* **Client Memory & Performance**: Processing large files (above a few hundred megabytes) depends on browser memory limits. Features like full binary dumps, complete string extraction, and client-side audio decoding read slices into memory and may cause tab lag on very large files.
* **Base64 Cap**: Live base64 generation is capped at 512 KB to avoid locking the UI thread.
* **Binary Hex Editor Scope**: The interactive byte-level editor modifies the first 1,024 bytes loaded into memory.
* **CORS Restrictions**: Loading files via public URL requires the hosting server to send appropriate `Access-Control-Allow-Origin` headers.
* **Format-Specific Extraction**: Archive extraction is currently limited to ZIP-based containers (ZIP, JAR, APK, Office XML). Formats like 7z, TAR, and GZIP have signature detection but do not yet have in-browser extraction trees.

## Dependencies

FilePeek loads the following vendor libraries via CDN:

* [ExifReader](https://github.com/mattiasw/ExifReader) (v4.44.1) for image metadata extraction
* [PDF.js](https://mozilla.github.io/pdf.js/) (v3.11.174) for PDF rendering and text parsing
* [Marked](https://github.com/markedjs/marked) (v12.0.0) for Markdown parsing
* [JSZip](https://stuk.github.io/jszip/) (v3.10.1) for unpacking ZIP containers

## Running Locally

Because FilePeek is a self-contained HTML page, you can run it without a build step or node package installations:

1. Open `index.html` directly in any modern browser (Chrome, Firefox, Safari, Edge).
2. Alternatively, serve the directory using any static web server:
   ```bash
   npx serve .
   # or
   python -m http.server 8000
   ```
