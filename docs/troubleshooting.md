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
---

## Reminder fired but was never delivered
- Symptom: reminder only appeared after I messaged the bot
- Log: "delivery failed (required): Unknown Channel"
- Cause: the job used a channel: target with my user ID. DMs need user:<id>.
- Fix: schedule with --announce --channel discord --to "user:<id>" and tell
  the agent to always use that target.

---

## Command policy defaulted to full access
- Cause: OpenClaw default is security=full, ask=off
- Fix: tools.exec.mode = "ask", plus stored host approvals set to
  allowlist / on-miss / deny. Verify with `openclaw approvals get`.

---

## File listing ran without approval
- Cause: file tools are not covered by command approvals
- Fix: tools.fs.workspaceOnly = true and chmod 700 ~/.openclaw

---

## 429 quota errors with very little usage
- Evidence: about 40 model calls in a day, roughly 4 to 5 per chat message
- Cause: agents make several long-prompt calls per message, and free tiers are small
- The fallback model was on the same Google project, and the log showed it
  was never used (zero calls to it)
- Plan: add a second provider (Groq) with its own quota

---

## Groq plugin refused to install
- Error: requires plugin API >=2026.10.8, runtime exposes 2026.10.4
- Cause: OpenClaw version too old for the plugin
- Fix: back up ~/.openclaw, then run `openclaw update`, then
  `openclaw doctor` (without --fix first), and recheck guardrails
- Status: in progress