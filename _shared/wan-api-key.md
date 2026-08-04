# Wan API key setup

Shared by `wan-text-to-image` and `wan-image-edit`. Not a skill itself — no frontmatter, not invocable.

Base URL (export as `WAN_BASE_URL`): `https://ws-qzv3410z961fsw01.eu-central-1.maas.aliyuncs.com/api/v1` — this is the DashScope-native API, not the OpenAI-compatible-mode one; the wan2.6 image models aren't reachable through compatible-mode on this workspace.

1. Check `./.env` (project root) for a `WAN_API_KEY=` line. If present, load that value into `WAN_API_KEY` and stop here — never ask the user again this run.
2. If missing, ask the user for the Alibaba MaaS API key directly in chat. Never proceed to generate or edit an image without it.
3. Once given, persist it:
   - Create `./.env` if it doesn't exist, or replace the existing `WAN_API_KEY=` line if it does — don't duplicate the line.
   - Ensure `.gitignore` (project root) lists `.env`; append it if absent.
4. Completion criterion: `WAN_API_KEY` holds a value sourced from `./.env`.
