# 02 - Messaging: Discord

## Goal
Talk to Jarvis from my phone and PC through Discord direct messages.

## Why Discord
I chose Discord over Telegram because I already use it daily.

## Steps

### 1. Create the bot (Discord Developer Portal)
1. Make a private server.
2. Create a New Application, then open the Bot page.
3. Turn on Message Content Intent and Server Members Intent.
4. Reset Token and copy it. This is a secret and never goes in the repo.
5. OAuth2 -> URL Generator: scopes `bot` and `applications.commands`;
   permissions View Channels, Send Messages, Read Message History.
6. Open the generated link and add the bot to the private server.

### 2. Store the token on the Mac
Run `read` on its own line (never paste it together with other lines),
then paste the token at the hidden prompt:

    read -s "TOKEN?Paste Discord bot token, then press Enter: "
    printf 'DISCORD_BOT_TOKEN=%s\n' "$TOKEN" >> ~/.openclaw/.env
    unset TOKEN
    chmod 600 ~/.openclaw/.env

### 3. Configure OpenClaw
Config references the token by environment variable name, so the token
itself is never written into the config:

    channels: { discord: { enabled: true,
      token: { source: "env", provider: "default", id: "DISCORD_BOT_TOKEN" } } }

Applied with `openclaw config patch --file <file> --dry-run`, then without
`--dry-run`.

### 4. Install the Discord plugin
    openclaw plugins install @openclaw/discord
    openclaw gateway restart

### 5. Pair my account
DM the bot, then:

    openclaw pairing list discord
    openclaw pairing approve discord <CODE>

### 6. Verify
    openclaw channels status --probe

## Problems and fixes
- Developer Portal would not save: "Private application cannot have a
  default authorization link". Could not turn Public Bot off, so I left it on
  for now. OpenClaw's pairing policy controls who can use the bot.
- Bot offline, token saved empty: I pasted several lines at once and the
  `read` command swallowed the next line. Fix: run `read` alone.
- Token exposed in terminal output and chat: reset it in the portal, then
  re-save it. Treat any exposed token as compromised.
- Log: "no channel plugin is installed or loadable". Fix: install
  @openclaw/discord, then restart. Do not run `doctor --fix` first, because it
  can remove dangling channel config.
- Warning: guild messages blocked because no guilds are allowlisted. Safe
  default. I use DMs only for now.

## Security notes
- Token lives only in ~/.openclaw/.env (chmod 600) on the jarvis account
- Default DM policy is pairing: only approved users can talk to the bot
- To do: make the app private again and allowlist only my user ID

## Status
- [x] Bot created and online
- [x] Plugin installed
- [x] Account paired
- [ ] Server allowlist (optional)