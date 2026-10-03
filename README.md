# ContraryLive

**Two perspectives. One live news stream.**

ContraryLive is a browser application where two AI personalities discuss news with each other. **Light** looks for a constructive angle. **Dark** pushes back with a dry, sardonic view of the risks and downsides. Viewers watch the conversation rather than chatting with the personalities.

Each story appears as an inline source card, followed by alternating responses. Named typing indicators show when the next response is being generated, and older messages fade away as the conversation moves on.

**Version:** 0.1.0, prototype baseline.  
**Maintainer:** Bryce Lovell, [BryceWDesign](https://github.com/BryceWDesign).  
**License:** [PolyForm Strict 1.0.0](LICENSE), noncommercial source-available software.

## Quick start

### Windows PowerShell

Python and a desktop browser with WebGPU support are required for the local AI option. Clone the repository and start a static server:

```powershell
git clone https://github.com/BryceWDesign/ContraryLive.git
cd ContraryLive
py -m http.server 8000 --bind 127.0.0.1
```

If you already have the project, open a terminal in the folder containing `index.html` and run only the server command. If `py` is unavailable, install Python or use another local static server.

### macOS or Linux

```sh
git clone https://github.com/BryceWDesign/ContraryLive.git
cd ContraryLive
python3 -m http.server 8000 --bind 127.0.0.1
```

### Start the conversation

1. Open [http://localhost:8000/index.html](http://localhost:8000/index.html) in desktop Chrome or Edge with WebGPU available.
2. Leave **Local AI in your browser** selected and choose a model. The app obtains its model list from WebLLM and prefers `Llama-3.2-3B-Instruct-q4f16_1-MLC` when available. Choose a smaller model if your device cannot run it.
3. Click **Start**. The first local run downloads model assets, which can require substantial bandwidth and time. The status line and progress bar report loading progress.
4. Select **Relaxed**, **Normal**, or **Fast** pacing. **Normal** is the default.
5. Use **Pause** and **Resume** to control the dialogue. A response already being generated can still finish and appear after you press Pause. News polling continues while paused.
6. To finish, close the browser tab and press **Ctrl+C** in the server terminal.

Python serves the static page; the local AI runs in the browser. There is no npm installation, build step, application backend, or API key configuration. An internet connection is required for news and runtime/model downloads.

Use localhost rather than opening `index.html` directly. Embedded document previews may block the external connections the app needs. Publishing the source on GitHub does not automatically host a running website.

## Conversation behavior

- **Light** is a warm, quick-witted optimist who looks for specific reasons for hope, help, or opportunity.
- **Dark** is a dry pessimist who challenges the optimism with concrete risks and counterpoints. The persona instructions direct both personalities to avoid cruelty toward victims.
- Each story receives five to eight generated messages. Speakers alternate within a story, and the starting speaker alternates between stories.
- Gold Light bubbles and purple Dark bubbles appear on opposite sides of the conversation.
- Inline topic cards identify the category, configured feed source, and headline. HTTP(S) story links are clickable.
- **Light is typing...** and **Dark is typing...** appear while the corresponding response is being generated.
- Pacing combines the selected delay with an allowance for message length.
- Both personalities share one inference engine. Their prompts request one or two short sentences, under 40 words, and discourage repeated points.

Responses are probabilistic. The selected model and external service changes can affect the dialogue. Persona instructions and word limits are prompts, not guarantees of every response's content or length.

## News sources

| Category | Configured sources |
| --- | --- |
| World | BBC, Al Jazeera, The Guardian, NPR |
| Gaming | IGN, PC Gamer, GameSpot, Eurogamer |
| Weather and natural events | NASA EONET open events, USGS magnitude 4.5+ daily earthquake feed |

All ten sources are attempted at startup. Three sources are then attempted every 60 seconds in rotation, so each source is normally revisited in about three to four minutes. Items are sorted by their supplied timestamp within each category. Selection weights are 50% world, 25% gaming, and 25% weather, adjusted to the categories with queued stories.

The app uses headlines and short feed descriptions. It does not read full articles, cluster the same event across sources, rank breaking news, or verify generated claims. **The source card identifies the input story; it does not verify everything the AI says.** There is no maximum story-age cutoff, and EONET open events may describe ongoing events.

Feed requests try the source directly, then AllOrigins and corsproxy.io public proxies. Availability depends on those external services. Failed source requests are skipped without a per-source health display. When no stories are queued, the app displays **Scanning for fresh headlines...** and waits for more.

## AI engines and privacy

**Local AI requires no paid inference API.** WebLLM uses your computer's resources to generate responses in the browser. Local mode still contacts external services for runtime assets, model downloads, and news.

The **No download (free web AI, experimental)** option sends persona instructions, story input, and recent conversation context to the configured Pollinations endpoint. The option's label describes the prototype integration; provider availability, pricing, limits, and terms are controlled externally and are not guaranteed.

The app does not persist conversation history:

| State | Bound or lifetime |
| --- | --- |
| Visible chat elements | Eight non-fading elements, including topic cards and bubbles; older elements are removed after fading |
| Generated-message history | Six entries in memory; each prompt includes at most four entries from the current story |
| News queues | Up to 40 items per category |
| Seen headline keys | Up to 800 keys |
| Conversation lifetime | Ends on page reload or tab closure |

WebLLM may cache downloaded model assets in browser storage. Browser history, caches, extensions, and external services operate separately from the app's in-memory conversation handling.

## Project structure

| File | Purpose |
| --- | --- |
| `index.html` | Interface, persona prompts, inference integration, news polling, and conversation loop |
| `README.md` | Usage and project documentation |
| `LICENSE` | PolyForm Strict 1.0.0 terms |
| `.gitignore` | Exclusions for local editor files |

External libraries and model assets load at runtime. This version does not include a test suite or a GitHub Actions workflow. It is a prototype, with runtime behavior dependent on browser capabilities and external services.

## License and commercial inquiries

Copyright (c) 2026 Bryce Lovell. All rights reserved.

The complete [PolyForm Strict License 1.0.0](LICENSE) governs use of the software. It permits noncommercial use as described in the license and does not grant permission to distribute the software or make changes or new works based on it. ContraryLive is **source-available, not open source**.

For commercial licensing or other rights not granted by the license, contact Bryce Lovell through a [repository licensing inquiry](https://github.com/BryceWDesign/ContraryLive/issues/new). Keep confidential information out of public issues.

External libraries, models, news content, and services retain their own licenses and terms. The project license does not grant ownership of those materials or establish authorship of generated output.