# Mat Stüdyo visual system

## 1. Style constants

### STYLE_BLOCK

```
Modern matte studio still life in the style of a minimalist architectural maquette. Materials: matte cream ceramic and plaster, powder-coated metal, brushed brass, linen; no glass, no gloss, no reflections. Soft diffused daylight from the upper left, long soft translucent shadow falling to the lower right, no specular highlights. Three-quarter high camera angle (about 25 degrees above the subject), 50mm equivalent lens, mild perspective, subject sharp, background a seamless {BACKDROP} studio sweep without horizon line. Palette limited to cream, sand, terracotta (#A8471F) and brushed brass (#C6A063) accents, at most three hues in the frame, accents under 15 percent of the frame. The subject fills about half of the frame with calm negative space around it and rests on a low plinth of three thin stacked slabs (cream, sand, and one with a terracotta edge). Calm, premium, editorial product photography, high resolution, clean edges.
```

### AVOID_BLOCK

```
Avoid: parchment, old paper, ink, fountain pen, inkwell, wax seal, map, floor plan drawing, ruler, coffee stain, handwriting, text, letters, numbers, logo, watermark, people, hands, screens, watercolor, illustration, sketch, film grain, lens flare, bokeh, vignette, dark corners.
```

### Backdrop colors and target RGB

| Key | Description | Hex | Target RGB |
| --- | --- | --- | --- |
| KREM | warm cream (#F1E2CD) | #F1E2CD | (241, 226, 205) |
| KUM | warm sand (#E7D5BC) | #E7D5BC | (231, 213, 188) |
| SEFTALI | soft peach (#F0D3C0) | #F0D3C0 | (240, 211, 192) |

`buildPrompt(id)` = STYLE_BLOCK with `{BACKDROP}` replaced by the backdrop description, then ` SUBJECT: ` + subject + framing (`FRAMING_169` or `FRAMING_43`) + ` ` + AVOID_BLOCK.

- FRAMING_169: `Wide 16:9 composition, subject centered, generous calm space on both sides.`
- FRAMING_43: `4:3 composition, subject centered, filling about half of the frame height, calm space around.`

## 2. Rule summary

- Matte materials only: ceramic, plaster, powder-coated metal, brushed brass, linen.
- Soft diffused daylight from the upper left; long soft translucent shadow to the lower right; no specular highlights.
- Three-quarter high camera angle (about 25 degrees above the subject), 50mm equivalent, mild perspective.
- Seamless studio sweep backdrop, no horizon line.
- Palette: cream #F1E2CD, sand #E7D5BC, peach #F0D3C0, terracotta #A8471F, brass #C6A063; brand green #0E5B4E rarely; deep green #0E3B34 rarely; charcoal #1B1815 very little. At most three hues in the frame; accents under 15 percent.
- Signature three-slab plinth (cream, sand, and one with a terracotta edge), unless the subject itself acts as the plinth.
- Subject occupies about 45–60 percent of the frame, with calm negative space around it.

## 3. Forbidden

No parchment, old paper, ink, fountain pen, inkwell, wax seal, map, floor-plan drawing, ruler, coffee stain, handwriting, text, letters, numbers, logo, watermark, people, hands, screens, watercolor, illustration, sketch, film grain, lens flare, bokeh, vignette, or dark corners.

No glass, gloss, or reflections.

## 4. Formats

- 16:9 → 1600×900
- 4:3 → 1600×1200
- WebP quality 85
- File size at most 200 KB (if over: try q80, then q75; if still over, leave at q75 and mark)

If the generator native size is smaller than the target, use the largest producible size and Lanczos-upscale.

## 5. Automatic gates (G1–G7)

All must pass.

- **G1 size:** output is exactly the target pixel size.
- **G2 backdrop color:** four corner patches (each 6% of width × 6% of height); Euclidean distance in sRGB from the target RGB; median of the four ≤ 28 and the largest ≤ 45.
- **G3 brightness:** Y = 0.2126R + 0.7152G + 0.0722B (sRGB/255, no linearization); whole-image mean between 0.60 and 0.92.
- **G4 edge calm:** four edge strips (each 2% thick); gray-value standard deviation; the largest ≤ 9 (0–255 scale).
- **G5 warm tone:** mean HSV of the four corner patches; hue 15°–50° and saturation 0.08–0.35.
- **G6 not empty:** whole-image brightness standard deviation ≥ 8.
- **G7 file size:** file ≤ 200000 bytes; if over, retry q80 then q75; if still over, keep q75 and mark.

If a candidate fails, regenerate with the same prompt plus: `Important: the backdrop must be a clean seamless {BACKDROP} with an even light tone, no dark vignette, no gray cast, and the whole subject must sit fully inside the frame with generous margin.` At most two retries. If all three attempts fail, keep the attempt with the fewest gate violations and set `gate_failed`: true.
