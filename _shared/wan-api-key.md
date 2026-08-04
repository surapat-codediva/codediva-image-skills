# Wan API key setup

Shared by `wan-text-to-image` and `wan-image-edit`. Not a skill itself — no frontmatter, not invocable.

Both `WAN_API_KEY` and `WAN_BASE_URL` are workspace-specific — every Alibaba MaaS workspace has its own base URL, so there's no shared default.

1. Check `./.env` (project root) for `WAN_API_KEY=` and `WAN_BASE_URL=` lines. Load whichever are present and skip asking for those this run.
2. If `WAN_API_KEY` is missing, ask the user for their Alibaba MaaS API key directly in chat. Never proceed to generate or edit an image without it.
3. If `WAN_BASE_URL` is missing, ask the user for their workspace's DashScope-native base URL (shape: `https://ws-<workspace-id>.<region>.maas.aliyuncs.com/api/v1`) — not the OpenAI-compatible-mode endpoint; the wan2.6 image models aren't reachable through compatible mode.
4. Once given, persist both:
   - Create `./.env` if it doesn't exist, or replace the existing `WAN_API_KEY=` / `WAN_BASE_URL=` lines if they do — don't duplicate either line.
   - Ensure `.gitignore` (project root) lists `.env`; append it if absent.
5. Completion criterion: `WAN_API_KEY` and `WAN_BASE_URL` both hold values sourced from `./.env`.
