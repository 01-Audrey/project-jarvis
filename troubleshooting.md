# Troubleshooting

Every error I hit, and how I fixed it.

---

## Homebrew: "only supported on Apple Silicon processors"
- **When:** trying to run `brew install node@22`
- **Cause:** my MacBook is Intel (`uname -m` returned `x86_64`)
- **Fix:** install Node.js from the official installer instead of Homebrew

---

## npm EACCES on global install
- **Error:** `permission denied, mkdir '/usr/local/lib/node_modules/openclaw'`
- **Cause:** standard macOS user can't write to the system npm folder
- **Fix:**
  ```
  mkdir -p ~/.npm-global
  npm config set prefix ~/.npm-global
  echo 'export PATH="$HOME/.npm-global/bin:$PATH"' >> ~/.zshrc
  source ~/.zshrc
  ```
  Then re-run the OpenClaw installer.

---

## Gemini API 429 RESOURCE_EXHAUSTED
- **Error:** "You exceeded your current quota" during the wizard's model step
- **Cause:** free-tier rate/quota limit on the key or chosen model
- **Status:** skipped the model step for now. To investigate: check limits in
  Google AI Studio, try a different Flash/Flash-Lite model ID, or switch provider.
