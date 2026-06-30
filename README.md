# WakeWord Sampler

A browser-based tool for recording positive and negative wake word samples for training custom wake word models — works with [microWakeWord](https://github.com/TaterTotterson/microWakeWord-Trainer-AppleSilicon), [openWakeWord](https://github.com/dscripka/openWakeWord), and any other model trainer that accepts WAV sample files.

## What it does

- **Positive samples**: Record yourself saying your wake word ("alfred", "hey alfred", etc.)
- **Negative samples**: Record words that should *not* trigger your wake word ("alexa", "hey siri", "ok google", names of family members, etc.)
- **Manual mode**: Click record, say the word, click stop. Review, then keep or discard.
- **Batch mode**: Walk away from the mic, a 3-second countdown gives you time to reach your testing distance, then it auto-records for a fixed duration. Recordings stack up for review.

All audio is captured at **16 kHz mono** — the standard format for wake word model training. Silence is auto-trimmed with 100ms padding. Output is 16-bit PCM WAV, compatible with microWakeWord, openWakeWord, and most other wake word trainers that accept WAV sample files.

## Quick start

### Option A: Standalone (no server)

1. Download `index.html`
2. Open it in Chrome or Safari
3. Select your wake word, choose positive or negative mode
4. Record samples — they'll download as `.wav` files to your Downloads folder
5. Move the files into your microWakeWord trainer's sample directory

### Option B: With microWakeWord Trainer

The recorder integrates with the [microWakeWord Trainer](https://github.com/TaterTotterson/microWakeWord-Trainer-AppleSilicon) API. When hosted alongside the trainer, samples upload directly to the trainer's sample pipeline instead of downloading manually.

1. Copy `index.html` into the trainer's web directory
2. Open it in your browser
3. Samples are uploaded via `/api/upload_personal_sample` and automatically categorized
4. Negative samples are marked via `/api/captured_audio/{id}/mark_negative`

## File naming

| Type | Pattern | Example |
|---|---|---|
| Positive | `{wake_word}_real_{timestamp}.wav` | `alfred_real_1719654321000.wav` |
| Negative | `negative_{word}_{timestamp}.wav` | `negative_alexa_1719654321000.wav` |

## Where files go

- **Standalone mode**: Browser Downloads folder as `.wav` files
- **Trainer mode**: Uploaded to the trainer's API, stored in its sample directory (typically `~/microwakeword-trainer/samples/` or equivalent)

## Audio processing

1. Recorded via `MediaRecorder` (webm/opus)
2. Decoded at 16 kHz mono via `AudioContext`
3. Silence trimmed (threshold: 0.01 amplitude, 100ms padding on both ends)
4. Re-encoded as 16-bit PCM WAV
5. Saved or uploaded

## Configuration

Edit the `<select id="wordSelect">` in `index.html` to add your own wake words:

```html
<select id="wordSelect" class="word-select">
  <option value="alfred">alfred</option>
  <option value="hey_alfred">hey_alfred</option>
</select>
```

## Browser requirements

- Chrome 90+ or Safari 14+ (needs `MediaRecorder` + `AudioContext` at 16 kHz)
- Microphone permission
- HTTPS or localhost (required for `getUserMedia`)

## License

MIT
