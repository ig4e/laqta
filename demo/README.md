# Laqta OCR demo

Real screenshots and an X11 screen recording of Laqta, not mock-ups.

- Feature commit: b8612e08d0e060e16597a75888ca6fb7b1de9dbc; clippy fix: 860a764213d04f841de872f6b4c95408c3b8a499.
- Debian container, Rust 1.99.1, Xvfb 1600x900, Openbox, Mesa llvmpipe Vulkan (software renderer).
- Built with `cargo build -p laqta`; started `/target/debug/laqta -sandbox` and kept that instance alive.
- Triggered `--action ocr`, selected the test text, accepted the 146 MB first-use download prompt. ONNX Runtime and both OCR models installed. The first pass opened the legacy editable text window with all four lines recognized.
- Subsequent OCR stayed on the frozen screenshot: reading sweep, sequential word reveal, automatic copy notice, four-word drag selection, Copy button and Ctrl+C notices.
- Clipboard content was independently pasted into xterm and captured in `clipboard.txt`: `Drag over these words`.
- Dragging outside the selection drew a new region over the dark panel and read it again. Word highlights remained visible on both backgrounds; word boxes closely followed this sample, with approximate padding. The status bar fit below both selections without clipped text.
- With overlay focus, Esc first clears selected words and then closes the text session. A normal `--action rectangle-region` followed by Shift+C also read in place (08-region-ocr.png).
- No crash observed during these interactions. `laqta.log` preserves stderr, including environment warnings.

## Limitation observed

On this Xvfb/Openbox setup the override-redirect overlay initially left keyboard focus on Openbox. Pointer selection and the Copy button worked, but Ctrl+C and Esc required `xdotool search --class laqta-overlay windowfocus`. The recorded keyboard interactions use that explicit focus. This platform/focus issue was not redesigned or fixed here. Windows/macOS, Wayland, real GPU hardware, reduced motion, multi-monitor/DPI behavior, and every toolbar action were not verified.

## Validation

- Initial `cargo clippy --workspace --all-targets -- -D warnings` failed on redundant_closure in overlay_text.rs:482. Replaced the forwarding closure with the action function. The final full workspace clippy passed (exit 0).
- `cargo fmt --all`, then `cargo fmt --all --check`: passed.
- `cargo test -p laqta-ui --lib`: 105 passed.
- `cargo test -p laqta-app`: all suites passed.

## Evidence

01-reading.png: moving light band and Reading text bar.
02-read.png: word highlights and All text copied.
03-selected.png: four words selected in blue.
04-copied.png: Ctrl+C and Copied 4 words.
06-dark-redrawn.png: new selection, automatically reread on dark background.
07-region-toolbar.png and 08-region-ocr.png: normal capture and Shift+C.
first-use.png and model-state.png: download confirmation and legacy first-use result.
demo.gif: 35-second real recording, scaled to 1000 px wide at 8 fps (about 1.7 MB).
demo.mp4: original 1600x900 recording at 12 fps.