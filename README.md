# Analog Clock

A real-time analog clock built with HTML, CSS, and JavaScript. The clock face is a circular watch image loaded from an external URL as a CSS background, and the three hands — hour, minute, and second — are plain white `div` elements that rotate in real time using `setInterval` and CSS `transform: rotate()`. No canvas, no SVG, no library. Just 13 lines of JavaScript and a handful of CSS rules.

---

## What This Project Does

On page load, `setInterval` starts firing every 1000ms. Each tick reads the current system time using JavaScript's `Date` API, calculates the correct rotation angle in degrees for each hand, and applies that rotation directly to the corresponding `div` via `element.style.transform`. The three hands pivot around their bottom center point — anchored to the center of the clock face — and rotate independently to always show the correct time.

A small "INDIA" label sits in the bottom-right corner of the clock face, styled as a black box with white monospace text, similar to the branding text found on real watch dials.

---

## How the JavaScript Works — `Script.js`

The entire logic is a single `setInterval` callback running every 1000ms:

```js
setInterval(() => {
  date = new Date();
  hourtime    = date.getHours();
  minutestime = date.getMinutes();
  secondtime  = date.getSeconds();

  hourRotation   = 30 * hourtime + minutestime / 2;
  minuteRotation = 6  * minutestime;
  secondRotation = 6  * secondtime;

  hour.style.transform   = `rotate(${hourRotation}deg)`;
  minute.style.transform = `rotate(${minuteRotation}deg)`;
  second.style.transform = `rotate(${secondRotation}deg)`;
}, 1000);
```

**Degree calculations — why these numbers:**

| Hand | Formula | Logic |
|---|---|---|
| Hour | `30 * hourtime + minutestime / 2` | A full clock circle is 360°. 12 hours → 30° per hour. The `+ minutestime / 2` adds fractional movement: 60 minutes = 30°, so 1 minute = 0.5°. At 3:30, the hour hand sits halfway between 3 and 4, not snapped to 3. |
| Minute | `6 * minutestime` | 60 minutes → 360°, so 1 minute = 6°. |
| Second | `6 * secondtime` | 60 seconds → 360°, so 1 second = 6°. |

The minute and second hands share the same degree-per-unit value (6°) because both divide 360 by 60. The hour hand moves slower because it divides 360 by 12 (30°) and gets a further fractional nudge from the current minutes.

**Note:** There is no immediate call before the interval, so the hands start at 0° for the first second and then snap to the correct position on the first tick. The clock also uses `getHours()` (0–23 format), but since the formula multiplies by 30 and uses modulo implicitly through the rotation wraparound, hours above 12 naturally map to the correct position (e.g., 15:00 → `30 * 15 = 450°`, which visually equals 90° — the 3 o'clock position).

---

## How the CSS Works — `Style.css`

**Clock face:**
The `.container` div is sized using viewport-relative units — `46vw × 46vw` — making the clock scale proportionally with any browser window size. The clock face image is applied as a CSS background:

```css
background: url(https://illustoon.com/photo/5053.png) no-repeat;
background-size: 100%;
```

This is an external PNG of a circular watch face (loaded from illustoon.com). No image file is bundled in the repository.

**The three hands — shared base style:**
```css
#hour, #minute, #second {
  background-color: #fff;
  position: absolute;
  border-radius: 4px;
  transform-origin: bottom;
  width: 1%;
  left: 49.3%;
}
```

All three hands are white rectangles (`background-color: #fff`), absolutely positioned, and `border-radius: 4px` gives them slightly rounded tips. The critical property is `transform-origin: bottom` — this anchors the rotation pivot to the bottom edge of each div. Since all three hands are positioned so their bottom edge sits at the clock center, they all rotate around that single center point regardless of their individual lengths.

`width: 1%` is 1% of the container's width — a very thin sliver that looks like a proper clock hand. `left: 49.3%` centers each hand horizontally at the clock center.

**Individual hand sizes and positions:**

| Hand | Height | `top` value | Effect |
|---|---|---|---|
| `#hour` | `22%` of container | `28%` from top | Shorter hand, positioned so its bottom reaches the center |
| `#minute` | `27%` of container | `23%` from top | Longer hand, same center anchor |
| `#second` | `27%` of container | `23%` from top | Same dimensions as minute hand — distinguished only by speed of rotation |

The minute and second hands share identical dimensions (`height: 27%`, `top: 23%`). They are visually indistinguishable by size — the only difference is how fast each rotates.

**"INDIA" label:**
```css
#name {
  height: 3vw;
  width: 10vw;
  background-color: #000000;
  color: #fff;
  position: absolute;
  bottom: 0;
  right: 0;
  font-size: 3vw;
  font-family: monospace;
  font-weight: bolder;
  letter-spacing: 0.2vw;
  box-shadow: 1px 2px 15px 3px #000;
}
```

Positioned at `bottom: 0; right: 0` — the bottom-right corner of the clock face. All sizing uses `vw` units so it scales with the container. The black background with white monospace text and subtle `box-shadow` mirrors the branding plaques found on real mechanical watch dials.

**Black body background:**
`body { background-color: black; }` — the page outside the clock is pure black, making the circular clock face appear to float in darkness.

---

## Tech Stack

| Technology | Role |
|---|---|
| HTML5 | 3 hand `div`s, 1 label `div`, all inside a `.container` |
| CSS3 | `transform-origin: bottom` for pivot, `vw`-based responsive sizing, external background image for clock face |
| JavaScript (Vanilla) | Single `setInterval` — `Date` API, 3 degree calculations, 3 `style.transform` updates per tick |

---

## Project Structure

```
Clock/
├── Index.html       # 3 hand divs + 1 INDIA label div, all inside .container
├── Script.js        # setInterval every 1000ms — date extraction, degree math, transform updates
├── Style.css        # Hand sizing, transform-origin, clock face background, INDIA label styling
└── Assets/
    └── Watch.png    # Local watch image asset (not used in current CSS — external URL used instead)
```

---

## How to Run

1. Clone the repository
   ```bash
   git clone https://github.com/tripathipawan/Clock.git
   ```
2. Open `Index.html` directly in any modern browser — the clock immediately shows your system's real current time. No server, no build step, nothing to install. Requires an internet connection to load the external clock face image from illustoon.com.

---

## Repository

[https://github.com/tripathipawan/Clock](https://github.com/tripathipawan/Clock)
