# MASTER SETUP PROMPT — paste into local Claude Code

> This prompt is meant to be pasted into a **local** Claude Code session running
> on the Windows desktop. It walks the local Claude through one-time setup of
> the environment, configuration, repo, and credentials, then surfaces the
> pending work items so the user can pick what to run.

---

You are a fresh local Claude Code instance running on Sean's Windows PC. Your job is to set up his environment top-to-bottom and then surface the pending work.

**Operating rules — read these first:**
- **Be interactive.** Ask before installing software. Ask before writing secrets to disk. Ask before destructive actions (deleting files, force-push, etc.).
- **Never invent values for tokens, passwords, or credentials.** If you need one and the user hasn't given it, stop and ask.
- **Stop at the first failure.** Don't proceed past a step that errored — diagnose, fix, then continue.
- **Summarize after each major step** (one sentence: what you did + what's next).
- **Commit work-in-progress to branch `claude/ccb-review-WrV25` on `seangoesnorth-png/seangoesnorth.com`** so cloud-Claude can see it next session.

---

## Phase 1 — Environment check

1. Confirm you're on Windows. Print `$PSVersionTable` to verify PowerShell is available.
2. Check `%USERPROFILE%\.claude\` exists. If not, create it.
3. Check `git --version`. If not installed: present `winget install Git.Git` and ask before running.
4. Check `node --version` (need 18+, for MCP servers). If missing: present `winget install OpenJS.NodeJS.LTS` and ask before running.
5. Check `ffmpeg -version`. If missing: present `winget install Gyan.FFmpeg` and ask before running (needed for the Lincoln video task).

Report what's installed vs missing. Resolve missing pieces with user confirmation before continuing.

---

## Phase 2 — Configure `settings.json`

Target file: `%USERPROFILE%\.claude\settings.json`

If it already exists, **read it first and merge** — don't blindly overwrite. Show the user the proposed diff before writing.

Baseline content to ensure is present:

```jsonc
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": { "GITHUB_PERSONAL_ACCESS_TOKEN": "<ASK_USER>" }
    }
  },
  "permissions": {
    "allow": [
      "Bash(git status)",
      "Bash(git diff:*)",
      "Bash(git log:*)",
      "Bash(git pull:*)",
      "Bash(git push:*)",
      "Bash(git add:*)",
      "Bash(git commit:*)",
      "Bash(ls:*)",
      "Bash(dir:*)",
      "Bash(ffmpeg:*)",
      "Bash(ffprobe:*)",
      "Read(./**)"
    ]
  }
}
```

For the GitHub token, walk the user through:
1. Open https://github.com/settings/personal-access-tokens/new
2. Token name: `claude-code-local`
3. Expiration: 90 days
4. Repository access: only `seangoesnorth-png/seangoesnorth.com`
5. Repository permissions: Contents = Read+Write, Pull requests = Read+Write, Metadata = Read
6. Generate, copy `ghp_...`, paste here
7. You then write it into the settings file

Ask if they use other connectors (Notion, Linear, Slack, etc.) — if yes, add the relevant MCP entries with the same ask-for-credentials pattern.

---

## Phase 3 — Get the repo

1. Ask where the repo should live (default: `C:\Users\<user>\code\seangoesnorth.com`).
2. If a clone already exists: `cd` there, `git fetch origin`, `git checkout claude/ccb-review-WrV25`, `git pull`.
3. If not: `git clone https://github.com/seangoesnorth-png/seangoesnorth.com.git <path>`, then checkout the branch.
4. Confirm the working tree is clean (`git status`).

---

## Phase 4 — Smoke test

Run, in order:
- `git status` — confirm branch + clean tree
- `ls prompts/` — should show `_desktop_setup.md`, `_lincoln_handoff.md`, `_master_setup_prompt.md`, plus the carousel brief
- `ls assets/carousels/3prompts/` — should show 3 PNG files
- Verify GitHub MCP is alive: list one tool from it (e.g. attempt to read a file via the MCP)

Report results. If anything fails, stop and ask.

---

## Phase 5 — Read the runbooks

Read these three files into context so you know what's pending:

1. `prompts/_desktop_setup.md` — the desktop setup reference (everything you just did is documented there; use it for troubleshooting)
2. `prompts/_lincoln_handoff.md` — the Lincoln video animation spec
3. Skim `prompts/SGN_3PROMPTS_carousel_CCB_brief.md` if present, or the latest carousel-related commit messages

---

## Phase 6 — Offer pending work

Surface the pending work items to the user as a numbered list:

1. **Lincoln video animation** — read `G:\01_SeanGoes_North_Active\03_Assets\lincoln.mp4`, apply the text-overlay animation per `prompts/_lincoln_handoff.md`, save as `lincoln_animated.mp4` beside the source, optionally push to `assets/video/lincoln_animated.mp4` in the repo.
2. **Substack post on Opus 4.8** — draft is in the conversation history of the cloud session; if not pasted in here yet, ask the user for it. Either paste-and-publish manually, or send via post-by-email through their Gmail (the Gmail MCP is connected in cloud but won't be on desktop unless added).
3. **Continue carousel work** — frames 03/04/05 are in `assets/carousels/3prompts/` from the cloud session; ask if they want frames 01/02/06/07 generated to complete the 7-slide carousel.
4. **Anything else the user names.**

Ask the user which to start. Do not autonomously pick — let them choose.

---

## Phase 7 — Execute

For whichever task they pick: do it carefully, commit changes to `claude/ccb-review-WrV25`, push, and tell them what's done + what's next.

When the session ends, the work is preserved in the branch so the next session (cloud or local) can pick up.

---

## Reference: the cloud session's state

As of the cloud handoff (commit `4cfa581`), the branch has:
- Free `/prompts/` page on the website (Body / Mind / Craft, copy-paste, no gate)
- 3 PROMPTS carousel frames (slides 03/04/05) in seangoesnorth design system (Instrument Serif title, JBM mono prompt cards, dot-pattern bg)
- Homepage link card for `/prompts/` added alongside the existing gated GitHub prompts link
- Substack + LinkedIn drafts for the Opus 4.8 post (in cloud session chat, not committed)
- The Lincoln video work could not start in the cloud — network-blocked from pulling the source MP4 — which is why we're handing off to you.

You're picking up from there.
