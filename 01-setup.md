# 01 - Setup: Installing OpenClaw

## Goal
Get OpenClaw installed on a clean MacBook Pro and running under its own
dedicated macOS user, so the agent is isolated from my personal accounts.

## Environment
- Hardware: MacBook Pro, **Intel (x86_64)**
- macOS: 15.8.1
- Account: dedicated **Standard** user named `jarvis` (no GitHub or personal logins here)
- Docs are written from my separate PC, so no Git credentials live on the agent's account

## Steps

### 1. Create a dedicated user
System Settings -> Users & Groups -> Add User -> `jarvis` (Standard).

Why: OpenClaw can read files, run commands and send messages, so I keep it
away from my personal files and credentials.

### 2. Check the machine
```
uname -m
sw_vers
```
Result: `x86_64`, macOS 15.8.1.

### 3. Install Node.js
Homebrew refused to install ("only supported on Apple Silicon processors"),
so I installed Node.js directly from the official installer instead.

```
node --version   # v24.22.0
npm --version    # 11.19.1
```

### 4. Fix npm permissions (see Troubleshooting)
```
mkdir -p ~/.npm-global
npm config set prefix ~/.npm-global
echo 'export PATH="$HOME/.npm-global/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
```

### 5. Install OpenClaw
```
curl -fsSL https://openclaw.ai/install.sh | bash
```
Result: OpenClaw 2026.10.4 installed, onboarding wizard started.

### 6. Onboarding wizard
- Chose: Quick start -> One agent
- Provider: Google (Gemini API key from AI Studio)
- Entered the key in the wizard only (never pasted anywhere else)
- Got a 429 quota error, so skipped the model step for now (see Troubleshooting)

## What happened
Install succeeded on the second attempt after the npm permission fix.
The model connection is still pending.

## Status
- [x] Dedicated user created
- [x] Node.js installed
- [x] OpenClaw installed
- [ ] Model connected
- [ ] `openclaw doctor` passing
- [ ] Dashboard chat working

## What I learned
- Intel Macs can't use Homebrew anymore, but the Node installer works fine.
- A standard macOS user can't write to system npm folders, so use a
  per-user global folder.
- Free API tiers can hit quota errors even on the first request.
