# Van Splat

An interactive 3D scene rendered entirely with gaussian splats, finished in a pixel-art style: a black van flies through clouds against a red sky on four jets where its wheels should be. Click the van and the side doors slide open on a crew of four men typing on laptops.

Open `index.html` in any browser with WebGL2. There is no build step and there are no dependencies.

## Controls

| Input | Action |
| --- | --- |
| Click / tap the van | Open or close the side doors facing you |
| Drag | Orbit the camera |
| Wheel / pinch | Zoom |
| Arrow keys, Enter / Space | Orbit and toggle the doors from the keyboard |
| **Raw splats** button | Show the gaussians without the pixel-art pass |

## How it works

- **Splats are generated in code.** Each shape (van body, jets, crew, clouds) is a signed distance function. A grid walk drops one flat disc gaussian on the surface per cell, oriented along the SDF gradient and coloured with banded toon lighting. About 63,000 splats in total.
- **Real gaussian splatting.** Every splat carries a full 3D covariance. The vertex shader projects it to a 2D screen-space covariance (EWA splatting), and the splats are depth-sorted on the CPU each frame with a counting sort and alpha-blended back to front.
- **Animation by rigid groups.** Each splat belongs to a group (door panel, head, hand, cloud). Group matrices live in a small float texture, so doors slide, heads nod and hands type without touching the splat data. Flames, laptop screens and LEDs are a small block of splats rewritten every frame.
- **Pixel art pass.** The splats render into a low-resolution target. A second pass applies a hard dithered alpha cut, snaps every pixel to a fixed palette with ordered dithering, adds a one-pixel outline and paints the dithered red sky. The canvas is scaled up with nearest-neighbour sampling.
- Clouds that would drift between the camera and the van dissolve with a screen-door dither so the van is never hidden.
