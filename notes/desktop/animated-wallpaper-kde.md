# Animated Wallpaper on KDE Plasma (OLED-friendly)

## Why animated wallpapers on OLED?

OLED panels emit light per pixel. Static content burns the same pixels at the same intensity for hours, causing uneven wear (burn-in). An animated wallpaper constantly shifts which pixels are lit, distributing the load evenly across the panel.

Key principles for OLED wallpapers:
- **Dark background** — black pixels are fully off, consuming zero power
- **Slow motion** — subtle enough to not be distracting
- **No bright static elements** — avoid logos, bright borders, or fixed UI-like shapes

---

## Setup: Web Page Wallpaper plugin

KDE Plasma supports rendering a local HTML file as a wallpaper via the **Web Page Wallpaper** plugin.

1. Right-click desktop → *Configure Desktop and Wallpaper*
2. Click *Get New Plugins* → search **"Web Page Wallpaper"**
3. Install, then select it as the wallpaper type
4. Set the path to your HTML file: `file:///home/user/wallpapers/your-wallpaper.html`

This approach is more flexible than video wallpapers:
- No codec/format issues
- Full control over the animation via CSS and JavaScript
- Negligible disk usage compared to video loops
- Parameters can be tweaked directly in the source file

---

## Perlin Flow Field

**File:** `~/wallpapers/perlin-flow.html`

A flow field built on 2D Perlin noise. Each particle follows a direction vector determined by the noise value at its position. As time progresses, the noise field evolves slowly, making the flow patterns shift organically.

### How it works

1. **Perlin noise** generates a continuous smooth scalar field over the 2D canvas
2. Each point in the field maps to an **angle** (`noise_value * π * 4`)
3. Particles move in the direction of that angle at each frame
4. The field shifts over time via a `time` offset added to the Y axis of the noise sample
5. Particles fade in and out over their lifetime using `sin(age/maxAge * π)`

### Tunable parameters

```js
const NUM        = 2000;    // particle count — lower = sparser, higher = denser
const SPEED      = 0.5;     // pixels moved per frame
const SCALE      = 0.0018;  // noise zoom — smaller = smoother, larger = more chaotic
const TIME_SPEED = 0.00025; // how fast the field evolves — lower = more static
const TRAIL      = 0.09;    // fade speed — lower = longer trails, higher = shorter
```

Color is sampled from a narrow hue range around cyan-blue (hue 190–230) and varies
with position in the noise field, creating subtle color gradients across flow streams.

### OLED-specific fix: `destination-out` fade

The naive approach for trail fading — drawing a semi-transparent black rectangle each
frame — causes a persistent haze over time. The reason is 8-bit integer rounding:

```
pixel at value 1/255  →  × 0.92  =  0.92  →  rounds back to 1/255
```

The pixel is stuck and never reaches pure black.

**Solution:** use `destination-out` compositing to reduce pixel *opacity* instead of
blending towards black. When opacity reaches 0, the pixel becomes fully transparent,
revealing the black `body` underneath. Opacity *can* reach zero, so the canvas always
returns to pure black.

```js
ctx.globalCompositeOperation = "destination-out";
ctx.fillStyle = `rgba(0, 0, 0, ${TRAIL})`;
ctx.fillRect(0, 0, W, H);
ctx.globalCompositeOperation = "source-over";
// draw particles normally after restoring source-over
```

The `body` must have `background: #000` for this to work correctly (already set in
the wallpaper file).

---

## Alternatives considered

| Method | Pros | Cons |
|---|---|---|
| Video wallpaper (Smart Video Wallpaper) | Simple setup | Large file size, limited customization |
| `linux-wallpaperengine` (AUR) | Access to Steam Workshop library | Requires owning Wallpaper Engine on Steam |
| HTML/JS wallpaper (chosen) | Fully customizable, lightweight, no assets needed | Requires a KDE plugin |
