# js-objectdetect

> Real-time object detection in the browser — no plugins, no server, no dependencies.



`js-objectdetect` is a JavaScript library for real-time object detection using the **Viola-Jones framework**, compatible with OpenCV HAAR cascade classifiers. It runs entirely in the browser using the HTML5 Canvas API.

---

## Features

- Zero dependencies — pure vanilla JavaScript
- Works with images, video files, and live webcam (WebRTC)
- Compatible with OpenCV stump-based HAAR cascade classifiers
- Supports IE 9+, Chrome, Firefox, Safari, Opera Mobile
- Includes a jQuery plugin and classifier converter

---

## Quick Start

A local HTTP server is required (browsers block Canvas and camera APIs on `file://`).

```bash
git clone https://github.com/mtschirs/js-objectdetect.git
cd js-objectdetect
python -m http.server 8080
# Then open: http://localhost:8080/examples/example_webcam.htm
```

---

## Installation

No package manager needed — just include the scripts:

```html
<script src="js/objectdetect.js"></script>
<script src="js/objectdetect.frontalface.js"></script>
```

---

## Usage

```javascript
// Create a detector: processWidth, processHeight, scaleFactor, classifier
const detector = new objectdetect.detector(320, 240, 1.1, objectdetect.frontalface);

// Detect on any image, video, or canvas element
// Returns: [[x, y, width, height, neighbours], ...]
const detections = detector.detect(imageElement, 1);

// Scale coords back to display size and draw boxes
const scaleX = displayWidth / 320;
const scaleY = displayHeight / 240;
detections.forEach(([x, y, w, h]) => {
  ctx.strokeRect(x * scaleX, y * scaleY, w * scaleX, h * scaleY);
});
```

### Webcam (WebRTC)

```javascript
navigator.mediaDevices.getUserMedia({ video: true }).then(stream => {
  video.srcObject = stream;
  video.onloadedmetadata = () => requestAnimationFrame(detect);
});

function detect() {
  requestAnimationFrame(detect);
  const detections = detector.detect(video, 1);
  // draw bounding boxes...
}
```

> Webcam requires **HTTPS** or **localhost** — plain HTTP is blocked by modern browsers.

---

## API

### `new objectdetect.detector(width, height, scaleFactor, classifier)`

| Parameter | Description |
|-----------|-------------|
| `width` / `height` | Internal processing size. Lower = faster, less accurate. Recommended: `320 × 240`. |
| `scaleFactor` | Scale step between detection passes (`1.1`–`1.4`). Lower = more thorough, slower. |
| `classifier` | A loaded classifier object (see below). |

### `detector.detect(source, minNeighbours)`

Runs detection on an `HTMLImageElement`, `HTMLVideoElement`, or `HTMLCanvasElement`. Returns an array of `[x, y, width, height, neighbours]`. Higher `minNeighbours` reduces false positives.

---

## Classifiers

| Classifier | Detects |
|------------|---------|
| `objectdetect.frontalface` | Face (front) — most reliable |
| `objectdetect.frontalface_alt` | Face (front) — alternate training |
| `objectdetect.eye` | Eyes |
| `objectdetect.nose` | Nose |
| `objectdetect.hand` | Open hand |
| `objectdetect.upperbody` | Upper torso |
| `objectdetect.profileface` | Face (side view) |

### Custom Classifiers

Convert any OpenCV stump-based HAAR XML file using the included converter:

```bash
node js/converter/converter.js haarcascade_fullbody.xml > js/objectdetect.fullbody.js
```

---

## Examples

| File | Description |
|------|-------------|
| `examples/example_image.htm` | Detection on a static image |
| `examples/example_video.htm` | Frame-by-frame video detection |
| `examples/example_webcam.htm` | Live webcam detection |
| `examples/example_gesture_input.htm` | Hand gesture UI control |
| `examples/example_glasses.htm` | AR glasses overlay on faces |

---

## Browser Support

| Browser | Image / Video | Webcam |
|---------|--------------|--------|
| Chrome, Firefox, Edge | ✅ | ✅ |
| Safari 6+ | ✅ | ⚠️ Requires user gesture |
| Internet Explorer 9+ | ✅ | ❌ No WebRTC |

---

## Performance Tips

- Use **320×240** for a good speed/accuracy balance
- Increase `scaleFactor` to `1.2`–`1.4` for faster (less thorough) detection
- Increase `minNeighbours` to `2`–`3` to reduce false positives
- Skip frames on slow devices — detect every 2nd frame and interpolate

---

## Credits

- **Martin Tschirsich** — author
- **Paul Viola & Michael Jones** — Viola-Jones detection framework (2001)
- **Rainer Lienhart** — extended HAAR features used in OpenCV

---

## License

[MIT](LICENSE) — classifier files may carry their own licenses; see individual files for details.
