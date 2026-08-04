---
name: grill-wan-image-edit
description: Interview the user relentlessly about the edit — what to change, what to preserve, style consistency — before editing an existing image via wan-image-edit. Use when the user wants to be grilled/interviewed before an image is edited, or combines a "grill" trigger phrase with a request to edit/modify an existing image (e.g. "grill me before you edit this", "ถามให้ครบก่อนแก้ภาพ..."). Not for a plain "แก้ภาพ..." / "edit this image..." request with no grilling intent (that's wan-image-edit), and not for generating a brand-new image (that's grill-wan-t2i).
disable-model-invocation: true
---

# Grill Wan Image Edit

Interview the user one question at a time about the edit — resolving what's still open, each with a recommended default — before handing off to `wan-image-edit` to actually perform it. Nothing gets edited until the user has confirmed the resolved requirements.

## Steps

1. **Resolve the source image first** — needed before the interview can be concrete. Either a local file path the user names/points to, or an image generated earlier in this session. Complete when a concrete, readable image file is in hand (same resolution as `wan-image-edit` step 2; base64-encoding happens later, in the handoff step).

2. **Set expectations.** Tell the user you'll ask about the edit one thing at a time, each with a recommended answer, and that nothing changes until they confirm. Complete when acknowledged.

3. **Interview one question at a time**, waiting for the user's answer before asking the next. Skip any branch already answered by the original request or clearly inferable from the source image and context; state the inference instead of asking. For each question, give your recommended answer. Suggested branches, in order (adapt or skip based on the edit — not every edit needs every branch):
   - **What exactly should change** — the specific edit instruction.
   - **What must stay untouched** — elements, composition, subject identity to preserve.
   - **Style/lighting/color consistency** — match the original, or deliberately shift it?
   - **Aspect ratio / size** — keep the source size (default), or resize? Maps to `size`.
   - **Number of variations** — maps to `n`.
   - **Anything to avoid introducing.**

   Complete when every relevant branch is resolved (user-given, inferred, or explicitly waived).

4. **Summarize and confirm.** Restate the resolved edit instruction plus `size`/`n`, and get explicit confirmation before editing. If the user requests changes, loop back to step 3 for just those branches. Complete when the user confirms.

5. **Hand off.** Follow `../wan-image-edit/SKILL.md` starting at its "Determine parameters" step (the source image is already resolved from step 1 here — reuse it rather than re-resolving), using the prompt/size/n resolved in step 4. Complete when that skill's steps finish (images saved).
