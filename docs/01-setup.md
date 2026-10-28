# 01 - Setup: Installing OpenClaw

## Goal
Get OpenClaw installed on a clean MacBook Pro, running under its own
dedicated macOS user, connected to a free Gemini model, with the gateway
and dashboard working.

## Environment
- Hardware: MacBook Pro, **Intel (x86_64)**
- macOS: 15.8.1
- Account: dedicated **Standard** user named `jarvis` (no GitHub or personal logins)
- Docs are written and pushed from a separate PC, so no Git credentials
  ever live on the agent's account

## Steps

### 1. Create a dedicated user
System Settings -> Users & Groups -> Add User -> `jarvis` (Standard).
Why: OpenClaw can read files, run commands and send messages, so it stays
away from my personal files and credentials.

### 2. Check the machine
    uname -m
    sw_vers
Result: x86_64, macOS 15.8.1.

### 3. Install Node.js
Homebrew refused to install ("only supported on Apple Silicon processors"),
so I installed Node.js from the official installer (v24.22.0).

### 4. Fix npm permissions
    mkdir -p ~/.npm-global
    npm config set prefix ~/.npm-global
    echo 'export PATH="$HOME/.npm-global/bin:$PATH"' >> ~/.zshrc
    source ~/.zshrc

### 5. Install OpenClaw
    curl -fsSL https://openclaw.ai/install.sh | bash
Result: OpenClaw 2026.10.4 installed.

### 6. Connect a model (Google Gemini, free tier)
1. Create an API key at aistudio.google.com/app/api-keys
2. Sign in (pick **Google** in the menu): openclaw models auth login --provider google
3. List models: openclaw models list --provider google --refresh
4. Choose a **Flash** model (free tier). Avoid Pro and retired models:
   openclaw models set google/gemini-3.6-flash
5. Test: openclaw agent --local --agent main --message "Hello!"
Result: Jarvis replied (HTTP 200).

### 7. Clean up credentials
After a wrong-provider mistake (see Troubleshooting) I removed the bad
profile, rotated the key, and re-logged in:
    openclaw models auth list
    openclaw models auth logout <openai-profile-id>
    openclaw models auth login --provider google --force
Final state: exactly one Google profile.

### 8. Start the gateway and dashboard
    openclaw gateway install
    openclaw gateway start
    sleep 30
    openclaw gateway status
    openclaw health
    openclaw dashboard
The gateway needs about 30 seconds to warm up on this Intel Mac. It binds
to 127.0.0.1 only. Dashboard chat works at http://127.0.0.1:18789/.

## Status
- [x] Dedicated user created
- [x] Node.js and OpenClaw installed
- [x] Model connected (Gemini Flash, free tier)
- [x] Gateway running as a background service
- [x] Dashboard chat working
- [ ] Messaging channel (Discord)
- [ ] Always-on settings (sleep, login behavior)

## What I learned
- Intel Macs can't use Homebrew now, but the official Node installer works.
- Standard macOS users need a per-user npm global folder.
- Check which provider you picked before pasting a key.
- Free-tier access depends on the model: Flash works, Pro does not.
- Removing a key from OpenClaw does not revoke it at the provider.
- Gateway warm-up is slow on older hardware; wait before assuming failure.