# Finish Line

A single-file web app for recording sailing race finish times from a phone. Built for race committee use: tap a button the moment a boat crosses the line, the time is locked to the second, then attach the sail number by voice or keypad. Results export to CSV.

No installation, no dependencies, no build step — everything lives in one HTML file (`finish-line.html`).

## How it works

The core design principle is **time first, number second**. The timestamp is captured at the instant the MARK FINISH button is pressed, not when the sail number has been entered. This means:

- Accuracy is limited only by your reaction on the button, never by typing or speech-recognition delay.
- If several boats finish in quick succession, you can tap MARK repeatedly and assign the sail numbers afterwards. Unassigned finishes are highlighted in orange in the list until a number is added.

## Using the app

1. **Start race now** (optional) — records the race start so each finish also gets an elapsed time. Skip this if you only need clock times.
2. **MARK FINISH** — tap as the boat crosses the line. The finish time is locked immediately.
3. **Enter the sail number** — if voice is on and the microphone is available, the app listens automatically after each mark: say the digits ("four two seven" or "four twenty-seven" both work; national letters like "GBR" are also picked up). The keypad works at any time as a fallback, or tap *assign later* to deal with it after the rush.
4. **Correct mistakes** — tap any sail number in the list to edit it, or ✕ to delete an entry entirely.
5. **Export CSV** — opens the iOS share sheet, so you can save to Files, AirDrop to a laptop, or attach to an email. On desktop browsers it downloads directly. **Copy** puts the CSV text on the clipboard for pasting straight into Excel.

Finishes are saved in the browser's local storage, so they survive closing the app or locking the phone. *Clear all* wipes the list when you're ready for the next race.

## CSV format

| Column | Example | Notes |
|---|---|---|
| Position | 1 | Finishing order |
| Sail number | GBR427 | Blank if never assigned |
| Finish time | 14:32:07 | Local clock time, to the second |
| Elapsed | 1:02:37 | Blank if no race start was set |
| Date | 2026-07-05 | ISO format |

## Deployment

The microphone requires a secure (HTTPS) page in a real browser, so the file needs to be hosted rather than opened locally.

**Option A — Netlify Drop (quickest):** go to <https://app.netlify.com/drop>, drag `finish-line.html` in (rename it `index.html` first for a clean URL), and you get an HTTPS link within seconds.

**Option B — GitHub Pages (best for iterating):**
1. Create a repository and add the file as `index.html`.
2. In the repo: Settings → Pages → deploy from the `main` branch.
3. The app appears at `https://<username>.github.io/<repo>/`.

**On the iPhone:** open the URL in Safari, tap MARK once and allow microphone access when prompted, then use **Share → Add to Home Screen**. The app then launches full-screen with its own icon, like a native app.

## Limitations and practical notes

- **Voice needs a data connection.** iOS speech recognition is processed server-side, so with no signal offshore the mic won't respond — the keypad and the timestamps themselves work fully offline.
- **Voice does not work inside app webviews** (including the Claude app's artifact viewer). Use Safari.
- **Times come from the phone's clock.** If results must reconcile with another timekeeper, check both devices against the same reference (e.g. GPS time) beforehand.
- **Storage is per-browser, per-device.** Clearing Safari website data deletes saved finishes, and Safari may evict local storage from sites unused for a long period — export the CSV promptly after each race rather than treating the app as an archive.
- **One race at a time.** The app holds a single finish list; export and clear between races.

## Tech notes

Plain HTML/CSS/JavaScript, no frameworks. Speech input uses the Web Speech API (`webkitSpeechRecognition`), available in Safari on iOS 14.5+ and in Chrome/Edge on desktop. CSV export prefers the Web Share API with a file payload (native iOS share sheet) and falls back to a blob download, with clipboard copy as a final fallback. State persists via `localStorage`.
