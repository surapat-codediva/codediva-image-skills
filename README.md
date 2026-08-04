# codediva-image-skills

Claude Code skills for text-to-image generation and image editing via the
Wanxiang (`wan2.6-t2i` / `wan2.6-image`) models on Alibaba MaaS.

## Skills

- **wan-text-to-image** — generate a brand-new image from a text prompt.
- **wan-image-edit** — edit/retouch an existing image with an instruction.
- **grill-wan-t2i** — interviews you one question at a time (with recommended
  defaults) about subject, style, composition, mood, and size before
  generating, then hands off to `wan-text-to-image`.
- **grill-wan-image-edit** — same grilling treatment for edits: what to
  change, what to preserve, style consistency, before handing off to
  `wan-image-edit`.

All four skills share `_shared/wan-api-key.md` (API key resolution) and
`_shared/wan-async-call.md` (submit + poll the async generation task).

## Install

This is a private repo, so both installers below need a GitHub account with
access to it (`gh auth login`, or an SSH key added to GitHub).

### Claude Code plugin

```bash
/plugin marketplace add surapat-codediva/codediva-image-skills
/plugin install codediva-image-skills@codediva
```

Managed, read-only, updates when this repo updates.

### npx (skills.sh, any agent)

```bash
# install both skills into the current project
npx skills@latest add surapat-codediva/codediva-image-skills --all

# or install a single skill
npx skills@latest add surapat-codediva/codediva-image-skills -s wan-text-to-image

# install globally (user-level) instead of per-project
npx skills@latest add surapat-codediva/codediva-image-skills --all -g
```

Copies editable skill files into your project — hack on them freely, pull
updates later with `npx skills update`.

## Setup

You need an Alibaba MaaS workspace with access to the Wanxiang `wan2.6-t2i`
and `wan2.6-image` models — specifically its API key and its
workspace-specific DashScope-native base URL (each MaaS workspace has its
own, shaped like `https://ws-<workspace-id>.<region>.maas.aliyuncs.com/api/v1`).

On first use, the skill will ask for both and store them in `.env` at your
project root as `WAN_API_KEY=...` and `WAN_BASE_URL=...`. It also makes sure
`.env` is listed in `.gitignore` so neither value gets committed. See
`.env.example` for the expected shape.

## Usage

Once installed, just ask the agent naturally:

- "สร้างภาพ..." / "generate an image of a fox reading a book" → `wan-text-to-image`
- "แก้ภาพนี้..." / "edit this image, make the sky sunset orange" → `wan-image-edit`
- "grill me before you generate this" / "ถามให้ครบก่อนสร้างภาพ..." → `grill-wan-t2i`
- "grill me before you edit this" / "ถามให้ครบก่อนแก้ภาพ..." → `grill-wan-image-edit`

Generated images are downloaded locally (the API's URLs expire after 24h) —
the agent will ask where to save each file.
