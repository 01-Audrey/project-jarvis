# 02 - Messaging Channels

## Goal
Talk to Jarvis from my phone by connecting one messaging channel.

## Plan
Start with **Telegram** (easiest: only needs a bot token), then consider
iMessage later.

## Steps (to fill in as I go)
1. Create a bot with @BotFather and copy the token (**never commit it**)
2. Run `openclaw channels add`
3. Restrict the bot to my own account only (do not set the DM policy to open)
4. Send a test message and confirm Jarvis replies

## Commands
```
openclaw channels add
```

## What happened
_TBD_

## Problems and fixes
_TBD_

## Security notes
- Allow only my own Telegram user ID
- Token is stored only on the `jarvis` account, never in this repo

## What I learned
_TBD_
