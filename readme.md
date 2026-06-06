# Seettu Scheduler

A browser-based randomised slot draw tool for chit-fund (seettu) groups. The organiser is always locked to Month 1, and all remaining participants are shuffled using a cryptographically secure algorithm before being assigned to monthly slots one by one.

---

## Features

- **Manual name entry** — type each participant name and press Add or Enter; chips update live
- **Organiser always locked to Month 1** — separate dedicated field, cannot be shuffled
- **Cryptographically secure shuffle** — uses `window.crypto.getRandomValues()` (Fisher-Yates), not `Math.random()`
- **Animated draw sequence** — names tumble before locking in one by one with a visual flash
- **Duplicate name validation** — prevents the same name being entered twice
- **Live slot counter** — total months and participant count update as names are added or removed
- **Reset without data loss** — clears the result table but keeps the name list intact
- **Zero dependencies** — pure HTML, CSS, and vanilla JS; no build step, no CDN required at runtime

---

## How to Use

### 1. Enter the Organiser
Type the organiser's name in the **Organiser** field at the top. This person is automatically assigned **Month 1** and is excluded from the shuffle.

### 2. Add Participants
Type each participant's name in the **Participants** field and click **Add** or press **Enter**. Each name appears as a chip tag showing their provisional slot number (M2, M3, …).

To remove a participant before generating, click the **×** on their chip.

### 3. Generate the Schedule
Click **Generate Schedule**. The draw runs in three phases:

| Phase | What happens |
|---|---|
| Shuffle | All pending rows animate with random names simultaneously |
| Lock sequence | Each row locks in one at a time with a brief "Locking…" flash |
| Complete | Button turns green — schedule is final |

### 4. Reset
Click **Reset** in the results panel to clear the draw without losing your name list. You can re-generate as many times as needed before the official draw.

---

## Shuffle Algorithm

The Fisher-Yates shuffle is used with `window.crypto.getRandomValues()` as the entropy source, ensuring the result is cryptographically random rather than pseudorandom.

```js
function secureShuffle(arr) {
  const a = [...arr];
  for (let i = a.length - 1; i > 0; i--) {
    const buf = new Uint32Array(1);
    window.crypto.getRandomValues(buf);
    const j = buf[0] % (i + 1);
    [a[i], a[j]] = [a[j], a[i]];
  }
  return a;
}
```

The organiser's name is separated from the participant array before the shuffle runs and is never included in the randomisation pool.

---

## File Structure

```
seettu-scheduler/
└── index.html      # Single self-contained file — open in any modern browser
```

No server, no framework, no package manager required.

---

## Browser Compatibility

| Browser | Support |
|---|---|
| Chrome 90+ | ✅ Full |
| Firefox 88+ | ✅ Full |
| Safari 15+ | ✅ Full |
| Edge 90+ | ✅ Full |

Requires `window.crypto.getRandomValues()` — available in all modern browsers.

---

## Customisation

### Change the Number of Participants
There is no hard limit. Add as many names as needed before generating.

### Change Timing
The animation delay between each row locking can be adjusted in the script:

```js
// Lock sequence delay (default 700ms between each row)
setTimeout(lockNext, 700);

// Initial wait before sequence begins (default 900ms)
setTimeout(lockNext, 900);

// "Locking…" flash duration before final name appears (default 350ms)
setTimeout(() => { ... }, 350);
```

### Change Animation Tick Rate
The shuffle animation updates every 60ms by default:

```js
const animInterval = setInterval(() => { ... }, 60);
```

---

## Limitations

- Results are not persisted — refreshing the page clears everything
- No export function (screenshot the table or copy names manually)
- Designed for single-session use; no backend or database

---

## License

MIT — free to use, modify, and distribute.
