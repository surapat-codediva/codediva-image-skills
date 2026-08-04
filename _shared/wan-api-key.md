# Wan API key setup

Shared by `wan-text-to-image` and `wan-image-edit`. Not a skill itself — no frontmatter, not invocable.

Both `WAN_API_KEY` and `WAN_BASE_URL` are workspace-specific — every Alibaba MaaS workspace has its own base URL, so there's no shared default. Assume the user has never done this before and doesn't know what "workspace" or "endpoint" mean — explain, don't just ask for a value.

1. Check `./.env` (project root) for `WAN_API_KEY=` and `WAN_BASE_URL=` lines. Load whichever are present and skip asking for those this run. If both are already there, this whole section is silent — don't mention setup at all, just proceed.

2. If `WAN_API_KEY` is missing, don't just ask "what's your WAN_API_KEY?" — explain what it is and where to get it, in the user's language (reply in Thai if the user has been writing Thai). Cover, in plain terms:
   - This is the secret key that lets the skill talk to Alibaba's image-generation service on the user's behalf.
   - It comes from the Alibaba Cloud **Model Studio** (百炼) console, under API key management for the workspace they want to use — if they don't have one yet, they (or whoever manages the account) need to create one there first.
   - It will be saved only in a local `.env` file in this project, never sent anywhere except Alibaba's API, and excluded from git — safe to paste directly into chat.

   Never proceed to generate or edit an image without it.

3. If `WAN_BASE_URL` is missing, explain that this is the specific web address for *their* workspace (not a one-size-fits-all URL — every workspace gets its own). Cover:
   - It looks like `https://ws-<workspace-id>.<region>.maas.aliyuncs.com/api/v1` — a "ws-" workspace ID followed by a region, not the general `dashscope.aliyuncs.com` address.
   - It's usually shown next to the API key in the same Model Studio workspace settings/API reference page — if they copied the key from there, the base URL is likely right beside it.
   - It must be the DashScope-native endpoint, not an "OpenAI-compatible mode" endpoint — the wan2.6 image models aren't reachable through compatible mode, so if they're offered a choice, pick the native one.

4. Once given, persist both — do this silently, no need to narrate the file-writing to the user:
   - Create `./.env` if it doesn't exist, or replace the existing `WAN_API_KEY=` / `WAN_BASE_URL=` lines if they do — don't duplicate either line.
   - Ensure `.gitignore` (project root) lists `.env`; append it if absent.
   - Confirm briefly to the user that setup is done and saved for next time, so they know they won't be asked again.

5. Completion criterion: `WAN_API_KEY` and `WAN_BASE_URL` both hold values sourced from `./.env`.
