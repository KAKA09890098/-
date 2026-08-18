---
name: yujia-v2-3
description: Strictly generate, reframe, and audit consistent 3:4 photorealistic lifestyle images of clearly adult Chinese women aged 20–30 in opaque fitted athletic tops and high-waisted yoga leggings. Enforces exact left/right rule-of-thirds composition, one-image-per-file output, deliberately different identities, hairstyles, top silhouettes, scenes, actions, palettes, and lighting across a batch, plus mandatory per-image review, and whole-batch PASS/FAIL audit before delivery.
---

# Yujia V2.3 — strict generation + acceptance audit

Produce repeatable lifestyle photographs from two approved benchmark images while preventing the common failure mode of turning a batch into near-duplicates of the same woman, hairstyle, top, street, lighting, or centered composition.


## ABSOLUTE OUTPUT RULE — ZERO EXCEPTIONS

This rule overrides every other generation convenience or batching behavior:

1. **ONE FILE = ONE PHOTOGRAPH ONLY.** Never place two or more generated images inside one file/canvas. No collage, grid, diptych, split screen, storyboard, contact sheet, tiled layout, before/after pair, or multi-panel composition.
2. **ONE PHOTOGRAPH = ONE HUMAN ONLY.** The entire visible frame may contain exactly one human figure: the intended adult woman. No second person in foreground, midground, background, reflections, mirrors, screens, posters, glass, silhouettes, crowds, passers-by, drivers, shop staff, cyclists, or partial human body parts.
3. A request for `N` images means **exactly N independent image-generation calls and N separate image files**. Never satisfy `N` by asking the image model to create N panels on one canvas.
4. If the output contains more than one panel or more than one human, status is **AUTOMATIC FAIL — DISCARD AND REGENERATE**. Cropping a collage into pieces does not count as a valid fix; regenerate the failed image as a fresh single photograph.
5. Temporary contact sheets may be created only by a non-generative review tool after all individual files already exist, solely for internal/final audit. They are never accepted as generated outputs and never replace the independent files.
6. **Batch execution lock:** even if the user asks for 5, 10, or 100 images in one message, invoke the image generator for image 01 only; review it; then invoke image 02; and so on. Never phrase a single generation call as a request for multiple variants/images/panels.
7. **Readable-scene-text lock:** every accepted image must contain at least one short, coherent, readable Chinese environmental phrase physically present in the photographed world. Missing text, pseudo-Chinese, unreadable characters, or floating graphic text is a hard failure.

## Load the standard

Before generating or editing anything, read these files completely:

1. [references/visual-standard.md](references/visual-standard.md)
2. [references/acceptance-review.md](references/acceptance-review.md)
3. [references/batch-plan-schema.md](references/batch-plan-schema.md)

Treat these assets as photographic-system references only:

- `assets/benchmark-outdoor.png`: outdoor directional-light reference.
- `assets/benchmark-indoor.png`: indoor window-light reference.

Never copy the benchmark identity. Never let repeated use of a benchmark collapse a batch into the same face, hairstyle, clothing cut, pose, or light.

## Mode A — Generate new images

### 1. Parse the request

Extract count, theme, scene constraints, wardrobe constraints, and requested left/right placement. If the request is otherwise complete, choose varied real-life scenes without asking questions.

### 2. Build and save a batch plan before generation

Create `batch-plan.json` using `references/batch-plan-schema.md`.

Every row must be deliberately unique in all of these dimensions:

- `subject_id` and facial identity descriptor
- face shape / feature cluster
- body-build descriptor within the approved natural range
- hairstyle
- top silhouette/style
- scene/backdrop
- action/prop
- top + leggings palette
- lighting preset/direction
- left/right third

For batches of 10 or fewer, do not repeat the exact hairstyle, top silhouette, scene, action/prop, full palette, lighting preset, or environmental sign text. Faces must always be different identities, regardless of batch size.

When the user does not specify a side, alternate exactly: LEFT, RIGHT, LEFT, RIGHT…

### 3. Generate exactly one image per call and per file — ABSOLUTE

Use the built-in image generation tool. Pass both benchmark assets as photographic-standard references when the tool supports references.

**Never request a collage, contact sheet, diptych, grid, split screen, multi-panel layout, or multiple finished images inside one canvas. Never allow any additional human anywhere in the frame, including background figures, reflections, posters, screens, silhouettes, or partial bodies.** A request for 10 images means 10 independent generation calls and 10 independent image files.

Generate **sequentially, one call at a time**. Do not issue a single model request whose semantic instruction is “make 5 images”, “show 5 variations”, or anything that encourages a multi-panel canvas. Complete image 01 → inspect → accept/regenerate → save; only then move to image 02. Review cadence may summarize every two accepted images, but generation itself remains strictly one call → one file → one photograph.

### 4. Use the shared prompt + one plan row

Apply the shared prompt in `references/visual-standard.md`, then append the row-specific fields from `batch-plan.json`. Explicitly state that the subject identity must not resemble previously accepted subjects in the same batch.

### 5. Mandatory immediate acceptance review

After every generated image, perform the per-image audit in `references/acceptance-review.md`.

An image is **FAIL** and must be regenerated if any hard item fails. Do not count a failed image toward the requested total.

Important hard failures include:

- centered or near-centered subject
- more than one human anywhere in the visible frame, even tiny/background/reflected/partial
- same/near-same facial identity as an accepted batch image
- hairstyle repeated when the batch size is 10 or fewer
- top silhouette repeated when the batch size is 10 or fewer
- scene/action/palette/lighting preset repeated when the batch size is 10 or fewer
- cropped head or feet
- wrong viewpoint or obvious wide-angle look
- direct eye contact
- unreadably flat or blown lighting
- anatomy or clothing-opacity failures
- watermark or unwanted branding failures

### 6. Mandatory cross-image review after each pair

After every two accepted images, compare the new pair against **all previously accepted images**, not only against each other. Check identity, hairstyle, top style, scene, action, palette, lighting, and composition side. If a repeated visual template is emerging, fail the newer image and regenerate it with a targeted correction.

### 7. Save accepted images

Use stable filenames:

`NN_场景_左侧三分之一.png` or `NN_场景_右侧三分之一.png`

The filename side must match the actual visual placement.

### 8. Run programmatic validation

Run:

```bash
python scripts/validate_batch.py OUTPUT_DIR --expected-count N --manifest batch-plan.json --strict
```

Fix every error before delivery. Programmatic checks supplement visual review; they do not replace it.

### 9. Mandatory final batch audit

Perform the complete whole-batch audit in `references/acceptance-review.md` and save `acceptance-report.md` beside the images.

Delivery is allowed only when:

- every image is individually PASS;
- the batch diversity audit is PASS;
- the programmatic validator is PASS.

If any image fails, regenerate only failed images and rerun both visual and programmatic audits.

### 10. Deliver

Deliver individual image files/links by default. Create a ZIP only when requested. Never replace independent files with a collage. A review-only contact sheet, if created outside the image generator, is secondary and never a deliverable substitute.

## Hard visual checks

Reject or redo an output when any condition fails:

- 3:4 vertical frame; prefer approximately 1080×1440.
- Exactly one human in the entire visible frame; that human is the intended adult subject.
- Subject visual center on LEFT x≈33% or RIGHT x≈67%, tolerance ±5 percentage points.
- **Center exclusion zone:** if the subject visual center falls anywhere from x=42% through x=58%, reject as centered.
- Keep the broader two-thirds side usable as negative space; movement/gaze should generally point into it.
- Complete body from hair to shoe soles; subject about 85–92% of frame height.
- Natural candid front, side, rear, or three-quarter view at waist/hip camera height with an 85–135mm telephoto look and shallow depth of field.
- Front, side, rear, and three-quarter angles are allowed. Keep the pose candid and natural; avoid staged direct eye contact unless explicitly requested.
- Clearly adult Chinese woman aged 20–30 with realistic 7.5–8-head proportions and plausible anatomy.
- Hair at least shoulder length; use varied long-hair configurations across a batch.
- Natural athletic hourglass or pear-shaped silhouette; smooth, anatomically plausible clothing-covered form without exaggeration.
- Natural gait or documentary action; no theatrical/provocative pose.
- Directional environmental light with controlled highlights and dimensional shadows.
- Opaque, supportive, medium-thick matte fitted sportswear; no transparency, wet-look fabric, scrunch seams, logos, visible intimate anatomy, or impossible tailoring.
- Background secondary and softly blurred. Readable environmental text may appear naturally but is optional.

## Batch diversity hard rules

Faces are identities, not a style parameter. **Never reuse or intentionally clone the same face across a batch.**

For N ≤ 10:

- 10 images → 10 distinct subject identities.
- Exact hairstyle: no repeats.
- Exact top silhouette: no repeats.
- Scene/backdrop: no repeats.
- Action/prop: no repeats.
- Full color palette: no repeats.
- Lighting preset: no repeats.
- Composition side alternates unless user specifies otherwise.

For N > 10:

- Subject identity still never repeats.
- No exact non-identity attribute, including environmental sign text, should occupy more than 20% of the batch unless the user explicitly fixes that attribute.

Similarity rule: if two accepted images would reasonably be mistaken for the same photo shoot because face + hair + top + lighting + backdrop are substantially similar, the newer one fails even if individual metadata labels differ.

## Mode B — Reframe an approved existing image

When the image is already correct and the user only asks to move it to a left/right third:

1. Do not generatively redraw the person.
2. Extend only clean background on the destination's opposite side and crop the other side by the same width.
3. Preserve subject, clothing, pose, lighting, exposure, color, and original main-image pixels.
4. Choose the third that best follows gaze/movement direction and provides clean title space unless the user specifies a side.
5. Compare the unchanged overlap region pixel-for-pixel. Require zero changed pixels before delivery.

## Quality-control principle

The Skill is not complete when images merely “look similar to the benchmark.” It is complete only when the **photographic system is consistent while the batch content is deliberately varied**, and every output passes both per-image and whole-batch audits.