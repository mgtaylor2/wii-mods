# Claude Code CLI Tips

These are the contextual tips Claude Code shows in the spinner while working, extracted from the binary (`/opt/claude-code/bin/claude` v2.1.142).

---

## Onboarding

- New to Claude Code? Run `/powerup` for a quick interactive tutorial
- Start with small features or bug fixes, tell Claude to propose a plan, and verify its suggested edits

## Plan Mode

- Use Plan Mode to prepare for a complex request before making changes. Press `Shift+Tab` twice to enable.
- Use `/config` to change your default permission mode (including Plan Mode)

## Multi-session

- Use git worktrees to run multiple Claude sessions in parallel.
- Running multiple Claude sessions? Use `/color` and `/rename` to tell them apart at a glance.

## Terminal Setup

- Run `/terminal-setup` to enable convenient terminal integration like `Shift+Enter` for new line and more (macOS: `Option+Enter`)
- Press `Shift+Enter` to send a multi-line message (macOS: `Option+Enter`)

## Customization

- Use `/memory` to view and manage Claude memory
- Use `/theme` to change the color theme
- Try setting environment variable `COLORTERM=truecolor` for richer colors
- Set `CLAUDE_CODE_USE_POWERSHELL_TOOL=1` to enable the PowerShell tool (preview)
- Use `/statusline` to set up a custom status line that will display beneath the input box

## Steering Claude While It Works

- Hit `Enter` to queue up additional messages while Claude is working.
- Send messages to Claude while it works to steer Claude in real-time
- Ask Claude to create a todo list when working on complex tasks to track progress and remain on track

## IDE Integration

- Open the Command Palette (`Cmd+Shift+P`) and run "Shell Command: Install 'claude' command in PATH" to enable IDE integration
- Connect Claude to your IDE with `/ide`

## GitHub & Slack

- Run `/install-github-app` to tag @claude right from your Github issues and PRs
- Run `/install-slack-app` to use Claude in Slack

## Permissions

- Use `/permissions` to pre-approve and pre-deny bash, edit, and MCP tools

## Images

- Did you know you can drag and drop image files into your terminal?
- Paste images into Claude Code using `Ctrl+V` (not `Cmd+V` on Mac!)
- `Ctrl+V` to paste images from your clipboard

## Rewind

- Double-tap `Esc` to rewind the conversation to a previous point in time
- Double-tap `Esc` to rewind the code and/or conversation to a previous point in time

## Conversations

- Run `claude --continue` or `claude --resume` to resume a conversation
- Name your conversations with `/rename` to find them easily in `/resume` later

## Skills / Custom Commands

- Create skills by adding `.md` files to `.claude/skills/` in your project or `~/.claude/skills/` for skills that work in any project

## Mode Cycling

- `Shift+Tab` to cycle between default mode, auto-accept edit mode, and plan mode

## Agents

- Use `/agents` to optimize specific tasks. E.g. Software Architect, Code Writer, Code Reviewer
- Use `--agent <agent_name>` to directly start a conversation with a subagent
- You can "fan out subagents" — ask a complex question and Claude sends a team. Each one digs deep so nothing gets missed.

## Desktop & Remote

- Run Claude Code locally or remotely using the Claude desktop app
- Continue your session in Claude Code Desktop with `/open-in-desktop`
- Run tasks in the cloud while you keep coding locally (remote control)
- Control this session from `claude.ai/code` using `/remote-control`

## Mobile

- Get pinged on your phone when long tasks finish — enable push notifications in the Claude mobile app

## Voice

- Use `/voice` to enable push-to-talk dictation

## Fullscreen TUI

- Try smoother rendering, lower memory usage, mouse support, and better formatting of copied text with `/tui fullscreen`

## Claude API

- Build your AI product with Claude API. Run `/claude-api` to get started

## Opus Plan Mode

- Your default model setting is Opus Plan Mode. Press `Shift+Tab` twice to activate Plan Mode and plan with Claude Opus.

## Plugin Nudges (shown when relevant files are detected)

- Working with HTML/CSS? Install the frontend-design plugin: `/plugin install frontend-design`
- Working with Vercel? Install the vercel plugin: `/plugin install vercel`
- Working with Stripe? Install the stripe plugin: `/plugin install stripe`

## Recurring Tasks

- `/loop` runs any prompt on a recurring schedule. Great for monitoring deploys, babysitting PRs, or polling status.

## Goal-driven Tasks

- Set an objective with `/goal all tests pass` and Claude keeps going until they do. Step away and come back to a finished task.
- `/goal` keeps Claude working until a condition is met. Good for "tests pass", "build succeeds", or long refactors.

## Feedback

- Use `/feedback` to help us improve!

---

## Configuring Tips

Tips are controlled via `~/.claude/settings.json`:

```json
{
  "spinnerTipsEnabled": false
}
```

To add custom tips (replacing the defaults):
```json
{
  "spinnerTipsOverride": {
    "excludeDefault": true,
    "tips": ["Your custom tip here"]
  }
}
```

To add custom tips alongside the defaults:
```json
{
  "spinnerTipsOverride": {
    "excludeDefault": false,
    "tips": ["Your custom tip here"]
  }
}
```
