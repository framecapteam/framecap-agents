# FrameCap for AI agents

Let your AI agent watch YouTube videos and video files with [FrameCap](https://getframecap.com): frames with their time on each one, plus the transcript.

## Set it up in one step

You need FrameCap in your browser with FrameCap Pro active. Paste this line into your AI agent (Claude Code, Codex, Cursor, Gemini CLI, Windsurf, Antigravity, VS Code or any other):

```
Set up FrameCap for me: read https://raw.githubusercontent.com/framecapteam/framecap-agents/main/AGENTS.md and follow every step.
```

Your agent then:
1. checks that Node.js is installed
2. finds the browser profile with FrameCap Pro (it asks if there's more than one)
3. asks you three things once: mute the video while it captures, close its tab when done, and where captures go
4. adds FrameCap to every AI app on your computer, for all your projects
5. learns when to use FrameCap
6. runs a test with a short video (click **Allow and capture** in your browser once)
7. tells you in plain English when it's ready

You do this once. After that, paste a YouTube link into your agent and ask, for example: "Watch this with FrameCap and list the steps."

## What's in here

| File | What it is |
|---|---|
| [AGENTS.md](AGENTS.md) | The setup steps your agent follows |
| [skills/framecap/SKILL.md](skills/framecap/SKILL.md) | Tells your agent when and how to use FrameCap |

## Or run one command

```
npx -y framecap-mcp@latest install
```

It adds FrameCap to every AI app on your computer (Claude Code, Codex, Cursor, Antigravity, VS Code, Windsurf, Gemini CLI, Claude Desktop). It asks a few things once: the browser profile with FrameCap Pro, whether to mute the video and close its tab when done, and where captures go. `npx -y framecap-mcp settings` changes them later; `npx -y framecap-mcp uninstall` takes FrameCap out again. The commands for each app one by one are in FrameCap's Settings, under **Connect your AI agent**, and at https://getframecap.com/mcp.

## Help

framecap.team@gmail.com · https://getframecap.com/contact
