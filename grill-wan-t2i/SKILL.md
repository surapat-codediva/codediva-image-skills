---
name: grill-wan-t2i
description: Interview the user relentlessly about requirements — subject, style, composition, mood, palette, aspect ratio — before generating a brand-new image via wan-text-to-image. Use when the user wants to be grilled/interviewed before an image is created, or combines a "grill" trigger phrase with a request to generate a new image (e.g. "grill me before generating...", "ถามให้ครบก่อนสร้างภาพ..."). Not for a plain "สร้างภาพ..." / "generate an image of..." request with no grilling intent (that's wan-text-to-image), and not for editing an existing image (that's grill-wan-image-edit).
disable-model-invocation: true
---

# Grill Wan Text-to-Image

Interview the user one question at a time — walking down each requirement, resolving what's still open, each with a recommended default — before handing off to `wan-text-to-image` to actually generate the image. Nothing gets generated until the user has confirmed the resolved requirements.

## Steps

1. **Set expectations.** Tell the user you'll ask about the image one thing at a time, each with a recommended answer, and that nothing generates until they confirm. Complete when acknowledged.

2. **Interview one question at a time**, waiting for the user's answer before asking the next — asking multiple at once is bewildering. Skip any branch already answered by the original request or clearly inferable from context; state the inference instead of asking. For each question, give your recommended answer. Suggested branches, in order (adapt or skip based on the subject — not every image needs every branch):
   - **Subject & scene** — what exactly is depicted, and in what setting/context?
   - **Style** — photorealistic, illustration, anime, 3D render, painterly, etc.?
   - **Composition & framing** — camera angle, shot distance, focal point?
   - **Mood & color palette** — lighting, tone, dominant colors?
   - **Aspect ratio / size** — maps to `wan-text-to-image`'s `size` parameter.
   - **Number of variations** — maps to `n`.
   - **Anything to avoid** — text/watermarks, specific elements, clashing styles.

   Complete when every relevant branch is resolved (user-given, inferred, or explicitly waived).

3. **Summarize and confirm.** Restate the resolved requirements as a single composed prompt plus `size`/`n`, and get explicit confirmation before generating. If the user requests changes, loop back to step 2 for just those branches. Complete when the user confirms.

4. **Hand off.** Follow `../wan-text-to-image/SKILL.md` starting at its "Determine parameters" step, using the prompt/size/n resolved in step 3 — its parameters are already settled, so don't re-ask. Complete when that skill's steps finish (images saved).
