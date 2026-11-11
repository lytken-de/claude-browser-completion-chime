# claude-browser-completion-chime

A lightweight Tampermonkey userscript that plays an audio notification when Claude.ai finishes generating a response. Features customizable volume control and a minimum response time threshold to prevent unnecessary chimes on short replies.

## Why

Claude can take a while on longer answers, and it's easy to switch to another tab and forget to check back. In the browser there's no built-in sound when a response is done. This script adds one.

## How it works

While Claude is generating a response, Claude.ai shows a **stop button**. The script watches for that button:

1. The stop button appears → Claude is working.
2. The stop button disappears → Claude is done → the chime plays (and optionally a browser notification).

If the stop button was only visible for a short moment (shorter than `MIN_DURATION_MS`), no chime is played.

The chime is embedded in the script, so nothing is fetched from the network.

## Installation

1. Install the [Tampermonkey](https://www.tampermonkey.net/) extension in your browser.
2. In Chromium-based browsers, open the extension's details page and turn on **Allow User Scripts**. Without it, no userscripts run.
3. Open [`claude-browser-completion-chime.user.js`](https://raw.githubusercontent.com/lytken-de/claude-browser-completion-chime/main/claude-browser-completion-chime.user.js). Tampermonkey offers to install it.
4. Reload Claude.ai and **click once anywhere on the page**. Browsers only allow audio after you've interacted with the page.

Tampermonkey checks this repository for updates automatically.

## Settings

Edit the `CONFIG` block at the top of the script:

| Setting | Default | What it does |
|---|---|---|
| `SHOW_NOTIFICATION` | `true` | Also shows a browser notification when the tab isn't focused. Clicking it brings you to the tab. You're asked for permission on your first click on the page. |
| `ONLY_IN_BACKGROUND` | `false` | `true` = only chime when the tab isn't focused. |
| `MIN_DURATION_MS` | `1500` | How long the stop button must have been visible before a chime is played. This does **not** delay the chime; it filters out quick replies. Use e.g. `10000` to only be notified about long waits. |
| `VOLUME` | `1.0` | `1.0` = the sound file's own volume, `0.5` = half. |
| `KEEP_AWAKE` | `false` | Plays a very faint noise while Claude works, so Bluetooth headphones/speakers don't fall asleep and swallow the chime. Only needed if the chime goes missing on Bluetooth. |
| `SILENCE_BEFORE_MS` | `0` | Extra silence before the chime. |
| `DEBUG` | `false` | `true` = logs `[Claude-chime]` state changes to the browser console. |

## Testing

Click once on the Claude.ai page, then open the browser console and run:

```js
claudeChimeTest()
```

## Troubleshooting

| Problem | Likely cause / fix |
|---|---|
| No `[Claude-chime] … loaded` line in the console | The script isn't running. Check that it's enabled in Tampermonkey and that **Allow User Scripts** is on. |
| `claudeChimeTest()` is silent | You haven't clicked on the page since it loaded (typing in the console doesn't count), or the tab/site is muted. |
| Works with speakers, not with Bluetooth | The Bluetooth device is asleep when the chime starts. Set `KEEP_AWAKE: true`. |
| No chime on quick replies | Expected: the reply was shorter than `MIN_DURATION_MS`. Turn on `DEBUG` to see `Too short – … ms`. |
| No chime when switching chats in the same tab | By design: switching away would otherwise look like "done". Keep long-running chats in their own tab. |

### Note on anti-fingerprinting tools

Some privacy/anti-fingerprinting tools (e.g. AdGuard) block or neutralize parts of the Web Audio API. To avoid that, the chime is decoded once and played directly as an audio buffer, without oscillators or gain automation.

## Limitations

- Relies on Claude.ai's stop button (`data-testid="chat-input-stop"`). If Claude.ai changes its page structure, the script may need an update.
- Unofficial; not affiliated with Anthropic.

## Credits

Chime: "announcement sound" by freesound_community via [Pixabay](https://pixabay.com/), used under the Pixabay Content License.

## License

[MIT](LICENSE)
