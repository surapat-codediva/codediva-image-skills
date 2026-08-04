# Wan async call & poll

Shared by `wan-text-to-image` and `wan-image-edit`. Not a skill itself — no frontmatter, not invocable. Verified live against the workspace's endpoint on 2026-08-03; sync mode (`multimodal-generation/generation`) errors with a misleading `InternalError.JsonParseError` for both wan2.6 models on this workspace — always use the async path below instead.

## Submit the task

```bash
curl -sS "$WAN_BASE_URL/services/aigc/image-generation/generation" \
  -H "Authorization: Bearer $WAN_API_KEY" \
  -H "Content-Type: application/json" \
  -H "X-DashScope-Async: enable" \
  --data-binary @<request-body-file>
```

Always write the JSON body to a file first and post it with `--data-binary @file` — a large base64 image or a `-d` argument that grows past the shell's arg-length limit both fail silently or noisily inline.

Request body shape (`content` items vary by caller — see each skill's own step for what goes in `content`):

```json
{
  "model": "<wan2.6-t2i | wan2.6-image>",
  "input": {
    "messages": [
      {"role": "user", "content": [ /* {"text": "..."} and/or {"image": "..."} */ ]}
    ]
  },
  "parameters": {"n": <count>, "size": "<width>*<height>"}
}
```

Success response: `{"output": {"task_id": "...", "task_status": "PENDING"}, "request_id": "..."}`. Any other shape at this stage is a genuine error — surface it raw and stop.

## Poll for the result

```bash
curl -sS "$WAN_BASE_URL/tasks/<task_id>" -H "Authorization: Bearer $WAN_API_KEY"
```

Poll every few seconds until `output.task_status` is `SUCCEEDED` or `FAILED`. On `SUCCEEDED`, each generated image is a URL at `output.choices[].message.content[].image` — valid for 24 hours only, so download it immediately rather than handing the user a link. On `FAILED`, surface the raw response and stop.

Completion criterion: `task_status` reached a terminal state (`SUCCEEDED` or `FAILED`) and, on success, every image URL has been downloaded.
