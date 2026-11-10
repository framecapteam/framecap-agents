---
name: framecap
description: Watch YouTube videos and video files with FrameCap - frames with their time on each one, plus the transcript. Use when the user shares a YouTube link (or Shorts) or a video file and wants it watched, explained, summarized or followed step by step, or wants code, commands, slides or on-screen steps copied from it.
---

# FrameCap

FrameCap captures a video in the user's own browser. You get:
- contact sheets: images that each hold several frames in time order, with a `t=MM:SS` chip on every frame
- `transcript.txt`: what was said, with times
- `README.txt`: how to read the two

## When to use it

- The user shares a YouTube link, a Shorts link or a video file and asks anything about it.
- You need what was on the screen (code, terminal commands, slides, settings, clicks in an app), not only what was said.

## How

1. Call `capture_youtube_video` with the link, or `capture_local_video` with the file's full path.
   - For a tutorial: `what` both, `gap_seconds` 2 to 5 (1 for fast code), `frames_per_picture` 6 to 12.
   - For one part of a long video: `start` and `end`, like 12:00 and 25:30.
   - For slides or a talk: `smart_frames` true (keeps a frame only when the picture changes).
   - If you have the FrameCap tools, always use them, never the terminal command. Some apps (Antigravity, for example) run terminal commands in a closed-off space that can't open the user's browser, so the terminal command waits and fails there.
   - No FrameCap tools at all? Then run `npx -y framecap-mcp capture <link or file> --what both --gap 2` (see `npx -y framecap-mcp --help`).
2. If the reply has a `capture_id`, call `get_capture` with it until the capture is done.
3. Read `README.txt` first, then the sheets in order, then `transcript.txt`.
4. To read small text or code, crop the one frame you need and look at it at full size.
5. When you tell the user where something happens, give its time (t=MM:SS).

## Good to know

- FrameCap runs in one browser profile, the one with FrameCap Pro on (one key works in 2). The user's choices are saved once for every app: that profile (opened even when the browser is closed), muting the video, closing the tab when done, and where captures go. You don't need the user to open anything.
- The YouTube tab that opens must stay open until the capture is done. The user can keep working in other apps.
- The user wants something different (another profile, sound on, keep the tab, another folder)? `npx -y framecap-mcp settings` with `--pick <number>`, `--mute on|off`, `--close-tab on|off` or `--captures project|folder`.

## If it fails

- **"Activate FrameCap Pro"**: the profile that opened has no active key. Ask the user to click **Enter Pro key** in the FrameCap menu there, or pick the profile that has Pro: `npx -y framecap-mcp settings --pick <number>` (`npx -y framecap-mcp --check` lists them).
- **The video opens in the wrong browser or profile**: `npx -y framecap-mcp settings --pick <number>`, or run the setup again: https://raw.githubusercontent.com/framecapteam/framecap-agents/main/AGENTS.md
- **Waiting, no result**: the first capture needs **Allow and capture** clicked at the bottom right of the YouTube tab that opened. Ask the user to look there.
- **"Your AI agent's FrameCap connection is out of date"** (shown in the YouTube tab): run `npx -y framecap-mcp@latest install`, then ask the user to restart this app.
- **Anything else**: the Troubleshooting table in the same AGENTS.md.
