---
name: grill-wan-t2i
description: Grill the user one question at a time about image requirements before generating via wan-text-to-image.
disable-model-invocation: true
---

# Grill Wan Text-to-Image

Interview the user relentlessly about the image, resolving each requirement one at a time with a recommended default, before handing off to `wan-text-to-image`. Nothing generates until confirmed.

## Steps

1. **Grill one at a time.** Walk down the decision tree below, one question per turn. For each, state your recommended answer and wait. Skip branches already answered or inferable from context — state the inference. Adapt or skip based on the subject.

   - **Subject & scene** — what, setting, context.
   - **Style** — photorealistic, illustration, anime, 3D render, painterly, etc.
   - **Composition & framing** — camera angle, shot distance, focal point.
   - **Mood & color palette** — lighting, tone, dominant colors.
   - **Size** — maps to `wan-text-to-image`'s `size`.
   - **Variations** — maps to `n`.
   - **Avoid** — text/watermarks, specific elements, clashing styles.

   After each answer, move to the next unresolved branch. Complete when every relevant branch is resolved (user-given, inferred, or waived).

2. **Confirm.** Restate the resolved requirements as a composed prompt plus `size`/`n`. If the user requests changes, loop back to step 1 for those branches only. Complete when confirmed.

3. **Hand off.** Follow `../wan-text-to-image/SKILL.md` from "Determine parameters" — prompt/size/n are settled, don't re-ask. Complete when that skill finishes.
