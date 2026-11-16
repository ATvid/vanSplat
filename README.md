# Van Splat

An interactive 3D scene rendered entirely with gaussian splats, finished in a pixel-art style: a black van flies through clouds against a red sky on four jets where its wheels should be. Click the van and the side doors slide open on a crew of four men typing on laptops.

Open `index.html` in any browser with WebGL2. There is no build step and there are no dependencies.

## Controls

| Input | Action |
| --- | --- |
| Click / tap the van, or Enter | Open or close the side doors facing you |
| Drag, or arrow keys | Orbit the camera |
| Wheel / pinch | Zoom |
| Pixel size slider | Size of one art pixel, 1 to 12 screen pixels |
| C | Next palette: crimson, midnight, pico-8, c64, game boy, 1-bit |
| O | Toggle the one-pixel outline |
| D | Toggle ordered dithering |
| Space | Pause the animation (the camera still orbits) |
| R | Show the raw gaussians without the pixel-art pass |
| H | Hide or show the control panel |

Every key also has a button in the panel, which reports the splat count, render resolution, frame rate and palette.

## How it works

- **Splats are generated in code.** Each shape (van body, jets, crew, clouds) is a signed distance function. A grid walk drops one flat disc gaussian on the surface per cell, oriented along the SDF gradient and coloured with banded toon lighting. About 63,000 splats in total.
- **Real gaussian splatting.** Every splat carries a full 3D covariance. The vertex shader projects it to a 2D screen-space covariance (EWA splatting), and the splats are depth-sorted on the CPU each frame with a counting sort and alpha-blended back to front.
- **Animation by rigid groups.** Each splat belongs to a group (door panel, head, hand, cloud). Group matrices live in a small float texture, so doors slide, heads nod and hands type without touching the splat data. Flames, laptop screens and LEDs are a small block of splats rewritten every frame.
- **Pixel art pass.** The splats render into a low-resolution target. A second pass applies a hard dithered alpha cut, snaps every pixel to a fixed palette with ordered dithering, adds a one-pixel outline and paints the dithered red sky. The canvas is scaled up with nearest-neighbour sampling.
- Clouds that would drift between the camera and the van dissolve with a screen-door dither so the van is never hidden.
