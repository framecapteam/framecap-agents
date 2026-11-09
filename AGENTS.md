# Set up FrameCap for this user

You are an AI agent, and the user asked you to set up FrameCap. Read "How FrameCap works" first, then do the steps in order.

Before each step, tell the user in one short, plain sentence what you're about to do. Ask before you change a file you didn't create. Never remove or replace the user's other MCP servers or settings.

## How FrameCap works (read this first)

- **FrameCap is a browser extension** (getframecap.com). It captures YouTube videos and video files: frames with their time on each one, plus the transcript.
- **It runs in one browser profile.** The browser can be Chrome, Edge, Brave or another Chromium browser. A browser can have several profiles (for example "Personal" and "Work"), and each profile has its own extensions.
- **FrameCap Pro is turned on per profile.** The user pastes their key under **Enter Pro key** in the FrameCap menu. One key works in **2** browsers or profiles at a time. Captures for agents need FrameCap Pro.
- **How a capture runs:**
  1. You call the MCP server (`framecap-mcp`).
  2. It opens the video in a browser.
  3. FrameCap in that browser captures it and saves a ZIP to the Downloads folder.
  4. The server unpacks the ZIP into `framecap-captures` and tells you where the files are.
- **So the server must open the exact browser and profile where FrameCap Pro is on.** By default it opens the computer's default browser, in the profile that browser used last. That's often the wrong one.
- **Two settings fix that: `FRAMECAP_BROWSER` (the browser program) and `FRAMECAP_PROFILE` (the profile folder).** With both set, every capture opens in that profile:
  - the browser closed? The server starts it.
  - another profile open? It still goes to the right one.
  - the user busy in another app (VS Code, for example)? It doesn't matter.
- **The first capture asks once.** FrameCap shows **Allow and capture** at the bottom right of the YouTube page. After one click, later captures start on their own.
- **Keep the tab open during a capture.** The YouTube tab that opens must stay open until the capture is done. The user can keep working in other apps.

## What only the user can do

- Install FrameCap and turn on FrameCap Pro (buy it on getframecap.com, then **Enter Pro key** in the FrameCap menu).
- Tell you which browser profile has FrameCap Pro, when there's more than one with FrameCap.
- Click **Allow and capture** once.

## Step 1: Node.js

Run `node -v`. You need version 18 or newer.

If it's missing or older, tell the user: "Please install Node.js (the LTS version) from https://nodejs.org, then tell me when it's done." Wait for them, then go on.

## Step 2: Find the browser profile with FrameCap Pro

Run `npx -y framecap-mcp --check`. Under "FrameCap in this computer's browsers", it lists every profile that has FrameCap:
- the profile's name, with marks like `turned off` or `opens links now`
- its `FRAMECAP_BROWSER:` value
- its `FRAMECAP_PROFILE:` value

Leave out profiles marked `turned off`. Then:

- **One profile left:** that's the one. Tell the user which one you'll use.
- **More than one:** ask the user. For example: "FrameCap is in Brave (profile Personal) and Chrome (profile Work). Which one has FrameCap Pro turned on, and which do you use most?" One key works in 2 profiles. If both have Pro, use the one they use most.
- **None:** ask the user to install FrameCap from https://getframecap.com and turn on FrameCap Pro, then run the check again.
- **A profile is marked `2 FrameCap copies on`:** ask the user to turn all but one off in the browser's extensions page. Two copies clash.

Note that profile's two values exactly as printed. They're plain text with no quotes, even when they have spaces. You use them in steps 3 to 6.

For any line that starts with `FIX`, tell the user what it means in plain words and help them fix it.

If the browser isn't Chrome, Edge or Brave, find the two values yourself: the browser program's full path, and the profile's folder name. The folders are in the browser's user data folder, named `Default`, `Profile 1`, `Profile 2` and so on.

## Step 3: Add the FrameCap MCP server for every project

The server starts with `npx -y framecap-mcp`. On Windows, put `cmd /c` in front (`cmd /c npx -y framecap-mcp`), because apps there can't start `npx` directly.

Add it user-wide, not for one project, so it works everywhere, with the two settings from step 2. Below, `<BROWSER>` and `<PROFILE>` are those values. Wrap each in quotes in a terminal, because they can have spaces.

| App | What to do |
|---|---|
| Claude Code | `claude mcp add --scope user framecap -e "FRAMECAP_BROWSER=<BROWSER>" -e "FRAMECAP_PROFILE=<PROFILE>" -- npx -y framecap-mcp`<br>On Windows end with `-- cmd /c npx -y framecap-mcp` |
| Codex | `codex mcp add framecap --env "FRAMECAP_BROWSER=<BROWSER>" --env "FRAMECAP_PROFILE=<PROFILE>" -- npx -y framecap-mcp`<br>On Windows end with `-- cmd /c npx -y framecap-mcp` |
| Cursor | Add the block below to `~/.cursor/mcp.json` |
| Gemini CLI | Add the block below to `~/.gemini/settings.json` |
| Windsurf | Add the block below to `~/.codeium/windsurf/mcp_config.json` |
| Claude Desktop | Add the block below to `claude_desktop_config.json`: on Windows in `%APPDATA%\Claude\`, on macOS in `~/Library/Application Support/Claude/` |
| VS Code | Ask the user to run **MCP: Open User Configuration** from the Command Palette, then add the block there. VS Code names the list `servers`, not `mcpServers` |
| Antigravity | Ask the user to open **Manage MCP Servers**, then **View raw config**, and add the block there |
| Any other app with MCP servers | Add the block wherever the app keeps its MCP servers |
| No MCP servers at all | Skip this step. Step 4 covers you |

If a server named `framecap` is already there, update its two settings and leave the rest as it is.

**The block.** Merge it into the file's `mcpServers` list. If the file doesn't exist, create it with just this. Keep every other server. In JSON, each `\` in a Windows path is written `\\`.

```json
{
  "mcpServers": {
    "framecap": {
      "command": "npx",
      "args": ["-y", "framecap-mcp"],
      "env": {
        "FRAMECAP_BROWSER": "C:\\Program Files\\BraveSoftware\\Brave-Browser\\Application\\brave.exe",
        "FRAMECAP_PROFILE": "Profile 2"
      }
    }
  }
}
```

The two values there are examples: put in the ones from step 2. On Windows: `"command": "cmd"` and `"args": ["/c", "npx", "-y", "framecap-mcp"]`.

## Step 4: Learn when to use FrameCap

If your app has skills, save this file:
https://raw.githubusercontent.com/framecapteam/framecap-agents/main/skills/framecap/SKILL.md

Save it as:
- Claude Code: `~/.claude/skills/framecap/SKILL.md`
- Codex: `~/.codex/skills/framecap/SKILL.md`

If your app has no MCP servers, add these lines to your user-wide instructions instead (for example `~/.codex/AGENTS.md` or `~/.gemini/GEMINI.md`). Put in the values from step 2:

```
To see a YouTube video or a video file, run: npx -y framecap-mcp capture <YouTube link or file path> (options: npx -y framecap-mcp --help). Then read README.txt in the folder it prints.
Before running it, set two environment variables: FRAMECAP_BROWSER=<BROWSER> and FRAMECAP_PROFILE=<PROFILE>
```

Other apps with MCP servers can skip this step: FrameCap's server tells you how to use it.

## Step 5: Check again

In a terminal, set the two values, then run `npx -y framecap-mcp --check` again:

| Terminal | How to set them |
|---|---|
| PowerShell | `$env:FRAMECAP_BROWSER = "<BROWSER>"; $env:FRAMECAP_PROFILE = "<PROFILE>"` |
| cmd | `set "FRAMECAP_BROWSER=<BROWSER>"` then `set "FRAMECAP_PROFILE=<PROFILE>"` |
| macOS or Linux | `export FRAMECAP_BROWSER="<BROWSER>" FRAMECAP_PROFILE="<PROFILE>"` |

The chosen profile should now be marked `[chosen]`.

## Step 6: Test it with a short video

This test works before your app restarts. Use the same terminal, with the two values still set. First tell the user:

"Your browser will now open a short YouTube video in [browser, profile]. At the bottom right of that page, FrameCap asks **Allow and capture**: please click it. You only do this once."

Then run:

```
npx -y framecap-mcp capture https://www.youtube.com/watch?v=jNQXAC9IVRw --what both --timeout 3
```

- **It prints a folder** with `README.txt`: it works. Tell the user they can delete that test folder (it's in `framecap-captures`).
- **"Activate FrameCap Pro"**: Pro isn't on in that profile. Ask the user to click **Enter Pro key** in the FrameCap menu there and paste their key. Or pick the profile that has Pro (step 2). Then run the test again.
- **"No capture after 3 minutes"**: see Troubleshooting, then run the test again.

## Step 7: Tell the user it's ready

Say it in plain English, along these lines:

> FrameCap is set up, using [browser], profile [name]. Restart [your app] once so it loads FrameCap. After that, in any project, paste a YouTube link and say, for example: "Watch this with FrameCap and list the steps." You only had to do this once: it keeps working as long as FrameCap Pro is active in that profile. The browser doesn't need to be open: FrameCap opens it when needed. Just leave the video tab open until the capture is done.

## Troubleshooting

| What happens | Why, and what to do |
|---|---|
| The video opens in the wrong browser or profile | The two settings aren't set, or aren't reaching the server. Run step 2 again and check the server's settings (step 3). |
| The browser was closed and nothing happened | Without the two settings, the default browser starts in its last profile, which may not have FrameCap. Set them (steps 2 and 3). |
| The video opened, but no "Allow and capture" | It's at the bottom right of the YouTube tab: ask the user to look there. If FrameCap Pro isn't on in that profile, FrameCap shows a message at the top of the page instead, and you get "Activate FrameCap Pro". |
| "Activate FrameCap Pro" | The profile that opened has no active Pro key. The user turns it on there (**Enter Pro key**), or you switch the two settings to the profile that has it. |
| "This key is already active in the most browsers it allows" | One key works in 2 browsers or profiles. In the one they no longer use, the user opens FrameCap Settings and clicks **Remove from this browser**, then pastes the key in the new one. |
| The browser asks where to save each download | Ask the user to turn that off in the browser's download settings. |
| `npx` is not recognized | Node.js isn't installed, or the terminal was open before it was. Install it, then open a new terminal. |
| `spawn npx ENOENT` on Windows | Use the `cmd /c` form from step 3. |
| A long video answers with a `capture_id` | That's normal: call `get_capture` with it until the capture is done. |
| The user closed the video tab mid-capture | Start the capture again and ask them to leave the tab open until it's done. |

More help: https://getframecap.com/mcp
