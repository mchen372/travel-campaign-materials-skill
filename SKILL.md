---
name: travel-campaign-materials
description: Use this skill when creating tourism campaign design materials from a brief, especially Chinese travel vertical node campaigns that require raster image generation with ChatGPT Image 2 / image2.0, style exploration, visual exploration, user approval or automatic decision mode, master visual design, and multi-size adaptation using a maintained style library and fixed material specifications.
---

# Travel Campaign Materials

This skill helps an AI agent produce a full set of tourism campaign materials from a short user brief, such as "毕业旅行季" or "夏日出游季".

Use it for:

- Travel vertical campaign posters, banners, opening screens, social posters, WeChat assets, push images, and internal banners.
- Workflows that need style exploration, visual exploration, master visual design, and multi-size adaptation.
- Requests where the user wants an agent to use a maintained style library instead of inventing styles freely.

Do not use it for:

- Generic UI screens or app product design.
- One-off image generation unrelated to tourism campaign materials.
- Final production when the user only wants to discuss strategy and explicitly says not to create assets yet.

## Required References

Load references only when needed:

- `references/workflow.md`: read when planning or executing the full workflow.
- `references/style-library.md`: read before choosing, explaining, or generating style directions.
- `references/material-spec.md`: read before multi-size adaptation or final delivery.
- `references/prompt-templates.md`: read when a concrete prompt is needed for demand parsing, exploration generation, master visual generation, adaptation, or safety checking.

Use local visual references from:

```text
style-library/
```

## Core Workflow

Follow this order:

1. Parse the user brief.
2. Determine `approval_mode`.
3. Present 3-5 candidate style directions from the style library.
4. Ask the user to choose one style, unless `approval_mode = auto`.
5. Generate 5 visual explorations only for the selected style.
6. Number visual exploration outputs as `1.1`, `1.2`, etc.
7. Ask the user to choose one visual direction, unless `approval_mode = auto`.
8. Create a horizontal master visual with real title text.
9. Adapt the master visual to the fixed material sizes.
10. Check safety zones, readability, and element overlap.
11. Deliver final files and a concise output list.

## Image Generation Hard Rules

This skill is for real bitmap campaign visuals, not code-drawn mockups.

For any style preview image, exploration image, master visual, adapted material, or final delivery image:

- Use ChatGPT Image 2 / image2.0 through the available image generation capability.
- Prefer the built-in `image_gen` tool when available.
- Output raster images such as PNG or JPG.
- Do not create SVG, HTML, CSS, canvas, Mermaid, PDF, or JS-generated drawings as substitutes for visual designs.
- Do not write scripts such as `generate-explorations.js` to draw placeholder cards, vector layouts, or synthetic mockups.
- Do not satisfy visual-generation steps with text-only plans, wireframes, or thumbnails.
- If the environment cannot access image generation, stop and tell the user that image generation is unavailable instead of fabricating visuals in code.

Allowed non-image-model processing:

- Renaming, moving, copying, compressing, or stitching already generated bitmap images.
- Creating contact sheets only from real generated bitmap images.
- Recording prompts, decisions, and output metadata in Markdown or JSON.

## approval_mode

Default:

```text
approval_mode = ask_user
```

Set:

```text
approval_mode = auto
```

if the initial brief includes expressions such as:

- 无需询问
- 不用问我
- 你来定
- 自动决定
- 直接做
- 直接输出

In `ask_user` mode, ask for user approval at key decision points.

In `auto` mode, do not stop for approval; choose automatically and record the chosen option and reason.

If the user changes mode mid-workflow, follow the newest instruction.

## Exploration Defaults

Style exploration:

```text
3-5 candidate style directions, each with one theme-specific style preview image
```

In `ask_user` mode, show 3-5 theme-specific style preview images, candidate style names, and reasons, then wait for the user to choose one style.

In `auto` mode, choose one style automatically from the 3-5 candidates and record the reason.

Style preview images are generated for the current brief. They must not be raw style-library reference images. Use style-library images only as style references and evidence for why the style was selected.

Visual exploration:

```text
1 selected style × 5 visual directions = 5 exploration images
```

Never generate a 15-image batch across multiple styles. The workflow is sequential: first decide the style, then generate visual directions for that one style.

Number outputs as:

```text
style_number.visual_number
```

Examples:

- `1.1`: style 1, visual 1
- `1.5`: style 1, visual 5
- `2.3`: only valid when style 2 was selected first, visual 3

Allow users to choose or combine directions, such as:

- `2.3`
- `2.3 + 2.4`
- `2.3构图 + 2.2色彩 + 2.1字体`

## Style Rules

Only choose from maintained style types unless the user explicitly asks to add a new style.

Current style types:

- 手帐拼贴风
- 电商大字报风
- 电影实景风
- 运营3D风
- 手绘风
- 微软风
- 新中式

Before style selection or generation, read `references/style-library.md`.

Style selection must be completed before visual exploration begins.

During style selection, generate one theme-specific preview image for each candidate style using the current campaign theme, title, audience, and mood. These previews help the user judge style fit; they are not the 5 visual exploration images.

## Style Reference Binding Rules

Do not generate from the style name alone.

Before generating any style preview image, visual exploration image, or master visual:

- Select 2-4 concrete reference images from the matching `style-library/` style folder.
- Record the exact reference image paths in the output record.
- Treat the selected images as knowledge references only: inspect them, learn from them, and abstract their reusable style traits.
- Do not use style-library images as init images, edit bases, image-to-image sources, trace targets, or direct visual copies.
- Do not ask the image model to recreate, modify, continue, or closely imitate any specific reference image.
- Explicitly summarize reusable high-level traits from the references: composition logic, palette range, typography mood, texture language, lighting, layout rhythm, and density.
- The prompt must include both the current campaign brief and the extracted traits from the selected reference images.
- If no usable reference images exist for the selected style, stop and tell the user that the style library is missing references instead of generating from the style name alone.

When generating explorations, use the selected style-library references as a knowledge anchor. The style name is only a label; the abstracted traits from reference images define the style. Preserve originality and avoid outputs that are substantially similar to any one reference.

## Master Visual Rules

The master visual is the source design for downstream adaptation.

Default rules:

- Use a horizontal master visual by default.
- Include real title text; do not create an empty visual-only image.
- Include optional modules only when useful: subtitle, benefit points, event tag, button, QR code, logo.
- Record which modules may be hidden in smaller sizes.
- In `ask_user` mode, show the master visual and ask for confirmation.
- In `auto` mode, continue and record the reason.

## Multi-Size Adaptation Rules

Before adapting sizes, read `references/material-spec.md`.

Adapt from the master visual instead of redesigning each size from scratch.

Use one of three strategies:

- Same-ratio scaling: for sizes close to the master ratio.
- Layout reconstruction: for vertical, square, or very different ratios.
- Module cropping/hiding: for small or constrained sizes.

Always check:

- Safety zones.
- Title readability.
- Logo placement.
- Important face or destination landmark cropping.
- Button, QR code, and benefit-point spacing.
- Text and element overlap.

## WeChat Delivery Rule

For WeChat public account materials:

- Create `1280 × 545 px` horizontal image.
- Create `545 × 545 px` square image.
- Final delivery requires one stitched image: `1825 × 545 px`.
- Place the horizontal image on the left.
- Place the square image on the right.

See `references/material-spec.md` for the detailed rule.

## Output Record

For every run, record:

- Original user brief.
- Parsed requirements.
- `approval_mode`.
- Candidate style directions.
- Generated style preview images.
- Style-library reference image paths used for each preview, exploration, and master visual.
- Generated exploration numbers.
- User-selected or auto-selected number.
- Master visual decision.
- Adapted material list.
- Safety-check result.
- Final delivery file list.

Keep the user-facing response concise: summarize what was created, where it is saved, and any unresolved confirmation items.
