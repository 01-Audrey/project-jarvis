# 04 - Always-On and Guardrails

## Goal
Keep Jarvis online 24/7 on the MacBook, and stop it from burning through
the free API quota.

## Findings
- Closing the lid sleeps a MacBook (Clamshell Sleep) unless an external
  display is connected. The caffeinate tool cannot override that.
- `caffeinate -s` only works on AC power. I was on battery, so it did nothing
  (PreventSystemSleep stayed 0) and the Mac idle-slept with the lid open.
- Fix: add `-i` so idle sleep is blocked even on battery.

## Steps
Keep the Mac awake (run in the jarvis account):

    nohup caffeinate -i -m -s > /dev/null 2>&1 &

Check it:

    pmset -g batt
    pmset -g assertions | grep -i -E "caffeinate|PreventUserIdleSystemSleep"
    pmset -g log | grep -E "Sleep  |Wake  " | tail -n 8

Rules for the always-on setup:
- Keep the MacBook plugged in with the lid open
- Lock the screen instead of logging out (logging out stops the jarvis session)
- A dark or locked screen is fine; only a full system sleep takes Jarvis offline

## Result
Locked the Mac, plugged in, waited about 15 minutes, DM'd Jarvis from my
phone, and it replied.

## Quota problem and guardrails
Jarvis tried to generate an avatar image, which used up the free Gemini
quota (HTTP 429). OpenClaw can pick an image provider automatically from the
configured key.

Fix, applied with `openclaw config patch`:
- Denied the media generation tools: image_generate, music_generate, video_generate
- Turned on tool loop detection (off by default)
- Added a fallback model (google/gemini-3.5-flash-lite) so a quota error on
  the main model does not stop Jarvis

Config shape:

    tools: { deny: ["image_generate", "music_generate", "video_generate"],
             loopDetection: { enabled: true } }
    agents: { defaults: { model: { primary: "google/gemini-3.6-flash",
              fallbacks: ["google/gemini-3.5-flash-lite"] } } }

No built-in "quota almost used" alert exists that I could find. The 429 shows
up in Discord when it happens, and AI Studio shows usage.

## What I learned
- Always check whether a keep-awake tool also works on battery
- The agent can spend quota on its own via tools; deny what you do not need
- A fallback on the same provider may share the same quota pool

## Still to do
- Make the keep-awake persist across restarts
- Automatic login or a manual routine after reboot