# Yujia V2.3 batch plan schema

Create `batch-plan.json` before generation.

## Required JSON structure

```json
{
  "skill": "yujia-v2-3",
  "count": 10,
  "alternate_sides": true,
  "one_file_one_photo": true,
  "one_human_per_photo": true,
  "images": [
    {
      "number": 1,
      "file_stub": "01_住宅公园_左侧三分之一",
      "side": "LEFT",
      "subject_id": "S01",
      "identity": "oval face; straight brows; narrow nose bridge; medium-light skin; tall/slender athletic frame",
      "hairstyle": "long loose waves",
      "top_style": "fitted short-sleeve crew-neck athletic top",
      "scene": "residential park path",
      "action_prop": "walking with folded light jacket",
      "palette": "warm ivory top + sage leggings + white shoes",
      "lighting": "tree-filtered directional daylight from camera-right",
      "difference_from_previous": "first image"
    }
  ]
}
```

## Absolute batch-output invariants

- `one_file_one_photo` must be `true`.
- `one_human_per_photo` must be `true`.
- `count: N` means N independent generated image files, never N panels in one file.
- Every plan row assumes a scene with no other visible humans anywhere, including reflections/screens/posters/backgrounds.

## Planning rules

For N≤10, every value below must be unique across rows:

- `subject_id` (always unique at any N)
- `identity` (must describe a genuinely different person)
- `hairstyle`
- `top_style`
- `scene`
- `action_prop`
- `palette`
- `lighting`


Each `difference_from_previous` entry after image 01 must name at least three obvious differences from the immediately previous image, including at least one of identity/hairstyle/top and at least one of scene/lighting.

## Anti-token-variation rule

Do not pretend two rows are diverse by changing only adjectives. These pairs count as repeats:

- `sunny alley` vs `bright sunny alley`;
- `high ponytail` vs `high ponytail with loose strands`;
- `racerback tank` vs `racerback sport tank`;
- `left side sunlight` vs `warm left side sunlight`.

Use materially different visual categories.
