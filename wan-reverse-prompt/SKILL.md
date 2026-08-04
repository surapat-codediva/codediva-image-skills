---
name: wan-reverse-prompt
description: Reverse-engineer an image into a Wanxiang wan2.6-t2i prompt. Analyze the image, draft a prompt, grill to refine, then optionally generate.
disable-model-invocation: true
---

# Wan Reverse Prompt

Reverse-engineer an image into a prompt for `wan2.6-t2i`. Analyze → draft → grill one question at a time → refine → optionally hand off to generate.

## Steps

1. **Get the image.** Use `read` to view the file the user provides, or use an image generated earlier in the session. If no image, ask for one. Complete when the image is loaded.

2. **Analyze and draft prompt.** Study the image across these dimensions, in order:
   - **Subject** — what, pose, expression, clothing, details.
   - **Style** — photorealistic, illustration, anime, 3D render, watercolor, etc.
   - **Composition** — camera angle, shot distance, focal point, depth of field.
   - **Mood & lighting** — time of day, light direction, warmth, atmosphere.
   - **Colors** — dominant palette, saturation, harmony.
   - **Background** — setting, props, textures, depth.
   - **Text/graphics** — visible text, borders, overlays.

   Compose one detailed English prompt following the same dimension order. Present in a code block. Complete when the draft is shown.

3. **Grill one at a time.** Ask about each dimension, one question per turn, waiting for the user's answer. State what you observed, ask if correct. Skip dimensions that are unambiguous. Use Thai to match the user's language. After each answer, revise the prompt and show the updated version in a code block.

   When the user confirms the prompt is satisfactory (or waives further changes), ask: "ต้องการ generate ภาพจาก prompt นี้เลยไหม?"
   - Yes → step 4.
   - No → done.

   Complete when the user confirms or waives.

4. **Hand off to generate.** Follow `../wan-text-to-image/SKILL.md` from its "Determine parameters" step. The prompt is already resolved. Ask for `size` and `n` if not discussed (default `1280*1280`, `1`). Complete when that skill finishes.
