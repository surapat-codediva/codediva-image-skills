# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A Claude Code **plugin/skills package** — no application code, no build, no tests. Everything is markdown instruction files (`SKILL.md`) that get loaded and followed by an agent at runtime. There is nothing to compile, lint, or run; "development" here means editing skill instructions and verifying them by actually invoking the skill against the live Wanxiang API.

Four skills, all wrapping the Wanxiang (`wan2.6-t2i` / `wan2.6-image`) models on Alibaba MaaS:

- `wan-text-to-image/SKILL.md` — generate a new image from a text prompt.
- `wan-image-edit/SKILL.md` — edit an existing image (local file or one generated earlier in the session) with an instruction.
- `grill-wan-t2i/SKILL.md` — interviews the user one question at a time (subject, style, composition, mood, size), each with a recommended default, before handing off to `wan-text-to-image`'s "Determine parameters" step with the resolved prompt/size/n. `disable-model-invocation: true` — only runs when the user explicitly invokes it (e.g. `/grill-wan-t2i`), never auto-triggered by the model.
- `grill-wan-image-edit/SKILL.md` — same grilling pattern for edits (what to change, what to preserve, style consistency) before handing off to `wan-image-edit`'s "Determine parameters" step. Also `disable-model-invocation: true`, explicit-only.

## Architecture

- `_shared/wan-api-key.md` and `_shared/wan-async-call.md` are shared instruction fragments, not skills themselves (no frontmatter, not directly invocable). All four `SKILL.md` files reference them via relative links (`../_shared/...`) instead of duplicating the logic — when changing API-key resolution or the submit/poll flow, edit the shared file once rather than each skill.
- The `grill-*` skills don't duplicate the API-call steps either: they resolve requirements interactively, then hand off directly into the matching base skill's "Determine parameters" step (already-resolved prompt/size/n in hand, so that step doesn't re-ask). When changing `wan-text-to-image`'s or `wan-image-edit`'s step structure or step names, check the corresponding `grill-*` skill's handoff step still points at a step that exists.
- `.claude-plugin/plugin.json` and `.claude-plugin/marketplace.json` define the installable plugin. `plugin.json`'s `skills` array must stay in sync with the actual skill directories; `marketplace.json` describes the plugin for `/plugin marketplace add`.
- Two install paths exist for the same skills: as a Claude Code plugin (`/plugin install`, read-only/managed) or via `npx skills@latest add` (copies editable files into a consuming project). Keep `SKILL.md` files self-contained enough to work correctly either way.

## Key API integration details (from the shared instruction files)

- Both `WAN_API_KEY` and `WAN_BASE_URL` are **workspace-specific** — every Alibaba MaaS workspace has its own base URL (shape `https://ws-<workspace-id>.<region>.maas.aliyuncs.com/api/v1`), so there is no shared default. Skills resolve these from `./.env` in the consuming project, prompting and persisting on first use.
- Only the **async** generation path works reliably: sync mode (`multimodal-generation/generation`) returns a misleading `InternalError.JsonParseError` for both wan2.6 models — always submit with `X-DashScope-Async: enable` against `services/aigc/image-generation/generation` and poll `tasks/<task_id>` (this was verified live against the workspace on 2026-08-03; re-verify before reverting to sync).
- Always write the JSON request body to a file and POST with `--data-binary @file` — inline `-d` with a large base64 image can exceed shell arg-length limits silently.
- Generated image URLs in the poll response expire after 24 hours — download immediately rather than handing the user a link.

## Editing skills

When changing a `SKILL.md`, keep the frontmatter `description` field precise: it's what the agent uses to decide *when* to invoke this skill vs. the other three (text-to-image vs. image-edit, plain vs. grill-first), including the Thai-language trigger phrases already present. The `grill-*` descriptions hinge on a "grill" trigger phrase to stay disambiguated from their plain counterparts — don't let the plain skills' descriptions drift into also matching grill-style phrasing. Each skill's steps use explicit "Complete when ..." completion criteria per step — preserve that pattern when editing so steps remain independently verifiable.
