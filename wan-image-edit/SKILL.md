---
name: wan-image-edit
description: Edit, retouch, or modify an existing image via the Wanxiang wan2.6-image model on Alibaba MaaS. Use when the user says "แก้ภาพ...", "แก้ไขภาพ...", "edit this image...", or wants to modify a photo or a previously generated image — not for creating a brand-new image from scratch (that's wan-text-to-image).
---

# Wan Image Edit

Modify an existing image using the Wanxiang `wan2.6-image` model via Alibaba MaaS.

## Steps

1. **Resolve the API key.** Follow `../_shared/wan-api-key.md` to load `WAN_API_KEY` and `WAN_BASE_URL`. Complete when `WAN_API_KEY` holds a value — never call the API without it.

2. **Resolve the source image.** Either:
   - a local file path the user names or points to, or
   - an image generated earlier in this same session (by `wan-text-to-image` or a prior edit) — reuse that file directly.

   Base64-encode it (`base64 -i <file> | tr -d '\n'`) and hold it in a temp file — the request body embeds it as a `data:image/<ext>;base64,<data>` URI, and it's too large to pass inline as a shell argument.

   Complete when a concrete, readable image file is in hand and base64-encoded.

3. **Determine parameters.** Pull from the user's request, asking only for what's missing:
   - `prompt` — the edit instruction (required).
   - `size` — ask only if the user wants a different size/aspect than the source; default: keep the source size.
   - `n` — number of variations; ask if not specified; default `1`.

   Complete when prompt, size, and n are all settled (user-given or default).

4. **Call the API.** Follow `../_shared/wan-async-call.md` to submit and poll. The request's `content` carries both the instruction and the source image:
   ```json
   {"role": "user", "content": [
     {"text": "<resolved prompt>"},
     {"image": "data:image/<ext>;base64,<encoded source image>"}
   ]}
   ```
   with `model: "wan2.6-image"` and `parameters: {"n": <resolved n>}` (add `"size"` only if the user asked for a different one than the source).

   Complete when the task reaches `SUCCEEDED` (image URLs in hand) or a genuine error has been surfaced and the step has stopped.

5. **Save the output.** Download each image URL from the response to a temp file — the links expire in 24 hours. Ask the user where to save the result before writing the final file(s).

   Completion criterion: every requested image exists as a file at the location the user gave.
