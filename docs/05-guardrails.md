# 05 - Guardrails

## Goal
Limit what the agent can do before connecting email or other accounts.

## What I found
- Default command policy was security=full, ask=off, meaning any shell command
  could run without asking.
- File tools are not covered by command approvals. A directory listing of the
  home folder ran with no prompt.

## Fixes (applied with `openclaw config patch`, each dry-run first)
1. Command approvals: tools.exec.mode = "ask" (allowlist, ask on miss, deny
   if no prompt can be shown). Also set the stored host approvals to the
   same values so nothing overrides it.
2. Deny media generation tools (image, music, video) and turn on tool loop
   detection.
3. Restrict file tools to the workspace: tools.fs.workspaceOnly = true.
4. chmod 700 ~/.openclaw so other local users cannot read it.

## Checks
    openclaw approvals get          (Effective column should say allowlist / on-miss)
    openclaw config get tools.fs    (workspaceOnly true)
    ls -ld ~/.openclaw              (drwx------)

## Reminders
Scheduled jobs deliver through Discord. Discord DM targets need the
`user:<id>` prefix. Using `channel:` with a user ID fails with "Unknown
Channel". Test with an isolated cron job that announces to the DM.

## Still to do
- Make the Discord bot private and allowlist only my user ID
- Review other tools (browser, file transfer) with tool policy
- Add a second provider as a quota fallback