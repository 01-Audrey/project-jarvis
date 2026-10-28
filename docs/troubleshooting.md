# Troubleshooting

Every error I hit, and how I fixed it.

---

## Homebrew: "only supported on Apple Silicon processors"
- Cause: my MacBook is Intel (uname -m returned x86_64)
- Fix: install Node.js from the official installer instead

## npm EACCES on global install
- Error: permission denied, mkdir '/usr/local/lib/node_modules/openclaw'
- Cause: a standard macOS user can't write to the system npm folder
- Fix: mkdir -p ~/.npm-global, then npm config set prefix ~/.npm-global,
  add ~/.npm-global/bin to PATH in ~/.zshrc, then re-run the installer

## Gemini 429 RESOURCE_EXHAUSTED
- Likely cause: the default model was a Pro model (not on the free tier)
- Fix: openclaw models list --provider google --refresh, then
  openclaw models set google/gemini-3.6-flash

## Pasted the key into the wrong provider prompt
- Error: "openai-api-key: Authentication failed"
- Cause: the wizard defaulted to OpenAI and I pasted a Google key. OpenClaw
  saved it as an OpenAI credential and its memory feature sent it to OpenAI.
- Fix: openclaw models auth list, then openclaw models auth logout <id>,
  rotate/delete the key at the provider (local removal does not revoke it),
  then openclaw models auth login --provider google --force
- Lesson: read the provider menu before pasting. Error lines can include
  a partial key, so check logs before sharing them.

## Stale auth profiles and 401 errors before success
- Cause: old saved profiles from earlier attempts; OpenClaw rotated through
  them until one worked
- Fix: openclaw models auth login --provider google --force

## Gateway: ECONNREFUSED right after start
- Cause: normal warm-up, about 30 seconds
- Fix: sleep 30, then openclaw gateway status

## Harmless messages I ignored (for now)
- "vector recall degraded": semantic memory needs an embeddings provider
- "SQLite reclamation Worker failed": cleanup noise on one-shot commands