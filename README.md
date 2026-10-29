# Project Jarvis

Self-hosted AI assistant inspired by J.A.R.V.I.S. Runs 24/7 on a MacBook with OpenClaw to handle reminders and messages through Discord. Every step documented.

> Status: In progress. Started October 28, 2026.

## What works so far
- [x] Install OpenClaw under a dedicated macOS user
- [x] Chat with it in the browser dashboard
- [x] Connect Discord (paired DMs only)
- [x] Reminders delivered to my Discord DM
- [x] Stays online 24/7 (plugged in, lid open, caffeinate)
- [x] Safety guardrails: command approvals, file-access lock, media tools denied

## In progress
- [ ] Reliable model quota (free Gemini tier runs out quickly; adding a second provider as fallback)
- [ ] Make the Discord bot private and allowlist only my user ID
- [ ] Email: read-only, on a test inbox
- [ ] A separate restricted agent for family

## Stack
- Hardware: MacBook Pro (Intel), macOS 15
- Agent framework: OpenClaw
- Model: Google Gemini Flash-Lite (free tier)
- Messaging: Discord

## Docs
- [Setup](docs/01-setup.md)
- [Messaging (Discord)](docs/02-messaging.md)
- [Email (planned)](docs/03-email.md)
- [Always-on](docs/04-always-on.md)
- [Guardrails](docs/05-guardrails.md)
- [Troubleshooting](docs/troubleshooting.md)

## Progress log
| Date | What I did |
|------|-----------|
| 2026-10-28 | Created repo, installed OpenClaw, connected Gemini and Discord, fixed sleep |
| 2026-10-29 | Reminders working, tightened command and file permissions, investigated quota limits |

## Lessons learned
- An agent makes several model calls per message, so free API tiers run out fast
- A second model from the same provider does not protect against a provider-wide limit
- Agent defaults are permissive; lock down command execution and file access early
- Check which provider a prompt is for before pasting an API key
- Treat any key that appears in a log or chat as exposed and rotate it

## Security notes
- Runs on a wiped Mac under a dedicated standard user, with no personal accounts
- No API keys, tokens, or IDs are committed to this repo
- Command execution requires approval; file tools are limited to the workspace