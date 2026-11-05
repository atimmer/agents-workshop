# Agent icons (for the setup agent)

This guide is for step 10 of `setup.md`. Use it only if you have an image-generation tool.

T3 Code looks for a project icon in the project folder. It tries, in order, `favicon.svg`, `favicon.ico`, `favicon.png`, `public/favicon.*`, and a few others. A plain `favicon.png` in the agent folder is enough. T3 caches the result: "no icon found" for about 1 minute, and a found icon for about 10 minutes. A new icon usually shows up within a minute or two; a *replaced* icon can take up to 10 minutes. If it doesn't appear, reload or restart T3 Code.

## 1. The five styles

Show these to the user, one line each, in their language:

1. **Cut-paper**: a solid colored tile with one large dark paper-cut shape and cream cut-outs. Calm, editorial.
2. **Pixel art**: a chunky 16-bit game sprite with a few bright colors on a dark tile. Playful, retro.
3. **Clay 3D**: a soft, rounded clay figure with gentle light, on a pastel background. Friendly, tactile.
4. **Line icon**: one thick white rounded line drawing on a vivid color. Clean, modern.
5. **Enamel pin**: a glossy enamel badge with gold outlines and flat bright colors. Bold, collectible.

## 2. Samples, choice, and the final icons

1. Generate one sample per style for the first agent (five images). Use the prompt template below with that agent's object and color.
2. Show the five samples **in the chat**, in style order, each labelled with its number and style name, e.g. `![1 – Cut-paper](/Users/sam/…/sample-1.png)`. T3 Code shows a Markdown image with an absolute local path. On Windows, use forward slashes (`C:/Users/Sam/…`). Then ask the user to pick a number.
3. In the chosen style, generate the icon for each agent as it's created (setup builds one workflow at a time). Each agent gets its own color and an object that looks clearly different from the others.

| Agent | Object | Accent color |
| --- | --- | --- |
| WordPress site | a browser window with a large magnifying glass in front of it | cobalt `#4A6CF7` |
| Email assistant | a wide sealed envelope | coral `#FF806E` |

Later agents: pick a new object that suits their task, and a new color.

## Prompt template

Fill in `<…>`, and use the style line from the table below.

```text
Square app icon, 1024x1024, for a personal AI agent called "<agent name>".
Style: <style line>.
Subject: <object>, as one large, simple central motif filling about 80% of the canvas, fully visible, not cropped.
Accent color: <name and hex>, used as the main color.
Must stay recognisable at 16x16 pixels: big shapes, few details, strong contrast.
Exactly one icon. No text, letters, numbers, logos, watermark, frame, mockup, or multiple variants.
```

| # | Style line |
| --- | --- |
| 1 | Minimal cut-paper. Edge-to-edge solid accent-color background, one enormous deep-ink (#182536) silhouette made of two or three chunky shapes with broad warm-ivory (#FFF1D9) cut-outs. Flat, gently irregular hand-cut edges, no texture, no shadows, no gradients. |
| 2 | Crisp pixel art on a 32x32 grid, scaled up with hard pixel edges. Dark navy background tile, 4–6 colors, accent color dominant, 1-pixel dark outline. No anti-aliasing. |
| 3 | Soft 3D clay render. Rounded plasticine shapes with subtle fingerprint texture, soft studio light and a gentle shadow, on a light pastel tint of the accent color. Matte, friendly, slightly chubby proportions. |
| 4 | Bold monoline icon. Edge-to-edge accent-color background, one white drawing with a single thick, rounded stroke of even width, plus at most one white filled shape. Flat, no shadows. |
| 5 | Enamel pin badge. Glossy hard-enamel fill in the accent color plus one or two supporting colors, raised gold metal outlines, a small highlight glint, on a plain off-white background. |

## Saving

Keep each generated image at its own path (`<in.png>`), never at `favicon.png` itself, so resizing reads and writes different files.

1. If a `favicon.png` already exists in the agent's folder, ask before replacing it.
2. Resize to 256×256 into `<TARGET>/favicon.png`. If no resize tool works, copy the larger PNG there instead; T3 scales it down.
   - macOS: `sips -z 256 256 "<in.png>" --out "<TARGET>/favicon.png"`
   - Windows (PowerShell):
     ```powershell
     Add-Type -AssemblyName System.Drawing
     $src = [System.Drawing.Image]::FromFile("<in.png>")
     $dst = New-Object System.Drawing.Bitmap 256, 256
     $g = [System.Drawing.Graphics]::FromImage($dst)
     try {
       $g.InterpolationMode = "HighQualityBicubic"
       $g.DrawImage($src, 0, 0, 256, 256)
       $dst.Save("<TARGET>\favicon.png", [System.Drawing.Imaging.ImageFormat]::Png)
     } finally { $g.Dispose(); $dst.Dispose(); $src.Dispose() }
     ```
   - Linux: `magick "<in.png>" -resize 256x256 "<TARGET>/favicon.png"` (or `convert` with ImageMagick 6)
3. Write the chosen style number and the prompt you used into the agent's `memory.md`, so that future agents can get a matching icon.
4. Commit both: `git -C "<TARGET>" add -- favicon.png memory.md`, then `git -C "<TARGET>" commit -m "Add agent icon"`. If the agent has a private GitHub backup, `git -C "<TARGET>" push`.
