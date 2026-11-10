# Set up FrameCap for this user

You are an AI agent, and the user asked you to set up FrameCap. Read "How FrameCap works" first, then do the steps in order.

Before each step, tell the user in one short, plain sentence what you're about to do. Ask before you change a file you didn't create. Never remove or replace the user's other MCP servers or settings.

## How FrameCap works (read this first)

- **FrameCap is a browser extension** (getframecap.com). It captures YouTube videos and video files: frames with their time on each one, plus the transcript.
- **It runs in one browser profile.** The browser can be Chrome, Edge, Brave or another Chromium browser. A browser can have several profiles (for example "Personal" and "Work"), and each profile has its own extensions.
- **FrameCap Pro is turned on per profile.** The user pastes their key under **Enter Pro key** in the FrameCap menu. One key works in **2** browsers or profiles at a time. Captures for agents need FrameCap Pro.
- **How a capture runs:**
  1. You call the MCP server (`framecap-mcp`).
  2. It opens the video in the user's browser.
  3. FrameCap there captures it and saves a ZIP to the Downloads folder.
  4. The server unpacks the ZIP and tells you where the files are.
- **The user's choices are asked once and saved for every AI app on the computer** (in `~/.framecap/settings.json`):
  - the browser profile with FrameCap Pro (captures open there, even when the browser is closed)
  - whether to mute the video while it captures
  - whether to close the video tab once the capture is saved
  - where captures go
- **The browser has to show the video to capture it.** It opens a tab for each capture. Muting and closing the tab keep that out of the user's way. The user can keep working in other apps meanwhile.
- **The first capture asks once.** FrameCap shows **Allow and capture** at the bottom right of the YouTube page. After one click, later captures start on their own.
- **Apps load MCP servers when they start.** After setup, the user restarts your app once.

## Step 1: Node.js

Run `node -v`. You need version 18 or newer.

If it's missing or older, tell the user: "Please install Node.js (the LTS version) from https://nodejs.org, then tell me when it's done." Wait for them, then go on.

## Step 2: Find the browser profile with FrameCap Pro

Run `npx -y framecap-mcp --check`. Under "FrameCap in this computer's browsers", it lists every profile that has FrameCap, numbered, with marks like `turned off` or `opens links now`.

Leave out profiles marked `turned off`. Then:

- **One profile left:** that's the one. Tell the user which one you'll use.
- **More than one:** ask the user. For example: "FrameCap is in Brave (profile Personal) and Chrome (profile Work). Which one has FrameCap Pro turned on, and which do you use most?" If both have Pro, use the one they use most.
- **None:** ask the user to install FrameCap from https://getframecap.com and turn on FrameCap Pro, then run the check again.
- **A profile is marked `2 FrameCap copies on`:** ask the user to turn all but one off in the browser's extensions page. Two copies clash.

Note the profile's number from the list. For any line that starts with `FIX`, tell the user what it means in plain words and help them fix it.

## Step 3: Ask the user three things, once

Ask these together, in plain words, with the suggested answer first:

1. "Should FrameCap **mute the video** while it captures? (Suggested: yes. The captions give the words, so sound isn't needed.)"
2. "Should FrameCap **close the video tab** once the capture is saved? (Suggested: yes, so tabs don't pile up.)"
3. "Where should captures go: **in the project** you're working in (suggested), or always in **one FrameCap folder** in your home folder?"

If the user just says "go ahead" or "you choose", use the suggested answers.

## Step 4: Add FrameCap to the AI apps on this computer

Run this, with the profile's number from step 2 and the answers from step 3:

```
npx -y framecap-mcp install --pick <number> --mute on --close-tab on --captures project
```

- Use `off` for a "no" answer.
- Use `--captures folder` for one FrameCap folder.
- If only one profile has FrameCap turned on, you can leave out `--pick`.

It saves the choices, then adds FrameCap, for every project, to each AI app it finds: Claude Code, Codex, Cursor, Antigravity, VS Code, Windsurf, Gemini CLI and Claude Desktop. Everything else in their settings stays as it is, and each changed file keeps a backup. If an app already had a `framecap` entry, it's replaced. Tell the user which apps got FrameCap and what it saved.

If your app isn't in its list, or it says `left alone` for your app, add FrameCap by hand:

| App | Where |
|---|---|
| Claude Code | `claude mcp add --scope user framecap -- npx -y framecap-mcp` |
| Codex | `codex mcp add framecap -- npx -y framecap-mcp` |
| Cursor | `~/.cursor/mcp.json` |
| Gemini CLI | `~/.gemini/settings.json` |
| Windsurf | `~/.codeium/windsurf/mcp_config.json` |
| Claude Desktop | `claude_desktop_config.json`: on Windows in `%APPDATA%\Claude\`, on macOS in `~/Library/Application Support/Claude/` |
| VS Code | Ask the user to run **MCP: Open User Configuration**, then add it there, under `servers` (not `mcpServers`) |
| Antigravity | Ask the user to open **Manage MCP Servers**, then **View raw config** |
| Any other app with MCP servers | Wherever the app keeps its MCP servers |

On Windows, start the command with `cmd /c`, because apps there can't start `npx` directly. The `claude` and `codex` lines become `... -- cmd /c npx -y framecap-mcp`.

For a settings file, merge this into its `mcpServers` list and keep every other server:

```json
{
  "mcpServers": {
    "framecap": { "command": "npx", "args": ["-y", "framecap-mcp"] }
  }
}
```

On Windows: `"command": "cmd"` and `"args": ["/c", "npx", "-y", "framecap-mcp"]`.

No MCP servers at all? Add this line to your user-wide instructions instead (for example `~/.codex/AGENTS.md` or `~/.gemini/GEMINI.md`):

```
To see a YouTube video or a video file, run: npx -y framecap-mcp capture <YouTube link or file path> (options: npx -y framecap-mcp --help). Then read README.txt in the folder it prints.
```

## Step 5: Learn when to use FrameCap

If your app has skills, save this file:
https://raw.githubusercontent.com/framecapteam/framecap-agents/main/skills/framecap/SKILL.md

Save it as:
- Claude Code: `~/.claude/skills/framecap/SKILL.md`
- Codex: `~/.codex/skills/framecap/SKILL.md`

Other apps can skip this: FrameCap's server tells you how to use it.

## Step 6: Test it with a short video

First tell the user:

"Your browser will now open a short YouTube video in [browser, profile]. At the bottom right of that page, FrameCap asks **Allow and capture**: please click it. You only do this once."

Then run:

```
npx -y framecap-mcp capture https://www.youtube.com/watch?v=jNQXAC9IVRw --what both --timeout 3
```

It uses the choices saved in step 4.

- **It prints a folder** with `README.txt`: it works. Tell the user where the test capture is; they can delete it.
- **"Activate FrameCap Pro"**: Pro isn't on in that profile. Ask the user to click **Enter Pro key** in the FrameCap menu there and paste their key. Or pick the profile that has Pro: `npx -y framecap-mcp settings --pick <number>`. Then run the test again.
- **"No capture after 3 minutes"**: ask the user whether the video opened in their browser.
  - **It opened:** see Troubleshooting, then run the test again.
  - **Nothing opened:** some apps run terminal commands in a closed-off space that can't open windows on the user's screen. Skip this test. After the restart in step 7, test through the FrameCap tool instead: the app starts FrameCap's server itself, outside that space.

## Step 7: Tell the user it's ready

Say it in plain English, along these lines:

> FrameCap is set up, using [browser], profile [name]. Restart [your app] once so it loads FrameCap. After that, in any project, paste a YouTube link and say, for example: "Watch this with FrameCap and list the steps." The browser doesn't need to be open: FrameCap opens it when needed. [It mutes the video and closes the tab when it's done, as you chose.] You only had to do this once. To change your choices later: `npx -y framecap-mcp settings`.

## Troubleshooting

| What happens | Why, and what to do |
|---|---|
| The video opens in the wrong browser or profile | Pick the right one: `npx -y framecap-mcp settings --pick <number>` (numbers from `--check`). If your app's FrameCap entry has its own `FRAMECAP_BROWSER`, that one wins: run `npx -y framecap-mcp install` again to replace it. |
| The terminal test says the video is opening, but nothing opens | Your app runs terminal commands in a closed-off space that can't open windows. Restart the app and capture through the FrameCap tool instead (for example: "Capture https://www.youtube.com/watch?v=jNQXAC9IVRw with FrameCap"). |
| The video opened, but no "Allow and capture" | It's at the bottom right of the YouTube tab: ask the user to look there. If FrameCap Pro isn't on in that profile, FrameCap shows a message at the top of the page instead, and you get "Activate FrameCap Pro". |
| "Activate FrameCap Pro" | The profile that opened has no active Pro key. The user turns it on there (**Enter Pro key**), or you pick the profile that has it (`settings --pick`). |
| "This key is already active in the most browsers it allows" | One key works in 2 browsers or profiles. In the one they no longer use, the user opens FrameCap Settings and clicks **Remove from this browser**, then pastes the key in the new one. |
| Captures land in a strange folder | Choose: `npx -y framecap-mcp settings --captures project` or `--captures folder`. |
| The browser asks where to save each download | Ask the user to turn that off in the browser's download settings. |
| `npx` is not recognized | Node.js isn't installed, or the terminal was open before it was. Install it, then open a new terminal. |
| `spawn npx ENOENT` on Windows | Use the `cmd /c` form. |
| A long video answers with a `capture_id` | That's normal: call `get_capture` with it until the capture is done. |
| The user closed the video tab mid-capture | Start the capture again and ask them to leave the tab open until it's done. |

To take FrameCap out of every app: `npx -y framecap-mcp uninstall`.

More help: https://getframecap.com/mcp
