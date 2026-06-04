# Desktop Claude Code — Setup & Troubleshooting (Windows)

This is the runbook for getting Claude Code working on the desktop so you can move
between cloud sessions and local sessions without losing context. Read top-to-bottom
the first time; bookmark for next time.

---

## 1. Install

**Download:** https://claude.ai/download → pick "Claude desktop app for Windows" → run the installer → sign in with the same Anthropic account you use in the browser.

**Alternative (terminal-only):**
```powershell
npm install -g @anthropic-ai/claude-code
```
Needs Node 18+. If you don't have Node: https://nodejs.org/ → download LTS → install → close + reopen PowerShell → `node --version` to confirm.

To launch: open the Claude Desktop app, OR run `claude` in any PowerShell window inside a folder you want to work on.

---

## 2. Where Claude reads config on Windows

| Purpose                       | Path                                                |
|-------------------------------|-----------------------------------------------------|
| User-wide settings            | `%USERPROFILE%\.claude\settings.json`               |
| Project settings (auto-load)  | `<repo>\.claude\settings.json`                      |
| User-wide skills              | `%USERPROFILE%\.claude\skills\<name>\SKILL.md`      |
| Project skills (auto-load)    | `<repo>\.claude\skills\<name>\SKILL.md`             |
| Keybindings                   | `%USERPROFILE%\.claude\keybindings.json`            |
| MCP server defs               | Inside `settings.json` under `mcpServers`           |

`%USERPROFILE%` is usually `C:\Users\<your-username>`. Open File Explorer, paste `%USERPROFILE%\.claude` into the address bar; if the folder doesn't exist, create it.

---

## 3. Connectors (MCP servers)

Connectors in the browser are called **MCP servers** on desktop — same idea, different config surface. Edit `%USERPROFILE%\.claude\settings.json` and add an `mcpServers` block. **Restart Claude after editing.**

### Minimal starter file

```jsonc
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": { "GITHUB_PERSONAL_ACCESS_TOKEN": "ghp_PUT_YOUR_TOKEN_HERE" }
    }
  },
  "permissions": {
    "allow": [
      "Bash(git status)",
      "Bash(git diff:*)",
      "Bash(git log:*)",
      "Bash(ls:*)",
      "Read(./**)"
    ]
  }
}
```

### How to get the GitHub token
github.com → top-right avatar → Settings → Developer settings → Personal access tokens → **Fine-grained tokens** → Generate new token → give it `Contents: Read+Write` and `Pull requests: Read+Write` on `seangoesnorth-png/seangoesnorth.com` → copy the `ghp_...` value → paste into the env block above.

### Filesystem access
**No MCP needed.** Desktop Claude reads/writes local files natively via its Read / Edit / Write tools. The reason the cloud session couldn't read `G:\...\lincoln.mp4` is the cloud sandbox — that disappears on desktop.

### Google Drive / Dropbox
No first-party MCPs. Community packages exist (search `mcp drive`, `mcp dropbox` on npm), but the simpler move is: open the file in your browser → download to `Downloads` → Claude reads it from disk. The whole reason you wanted Drive/Dropbox in the cloud was to bridge into the sandbox; on desktop the local filesystem IS the bridge.

### Substack / Buffer
No official MCPs. Buffer has a real HTTP API — once on desktop, I can `curl` it directly with an access token (Buffer → Settings → Apps → Create access token). Substack has no public API; copy-paste into the editor or use post-by-email.

---

## 4. Skills

Skills are folders with a `SKILL.md` describing when to invoke them.

**User-wide:** create `%USERPROFILE%\.claude\skills\<skill-name>\SKILL.md`.
**Project-only:** create `<repo>\.claude\skills\<skill-name>\SKILL.md`.

Built-in skills (`verify`, `code-review`, `simplify`, `run`, `init`, `claude-api`, `deep-research`, etc.) come with the install. They show up automatically.

If skills aren't appearing:
- Check the path — `SKILL.md` must be **inside** a named subfolder (`skills/MY_SKILL/SKILL.md`, not loose at `skills/MY_SKILL.md`)
- Restart Claude after adding
- Project skills only show when you open Claude inside that project

---

## 5. Common errors & fixes

| Symptom                                          | Fix |
|--------------------------------------------------|-----|
| "MCP server failed to start"                     | Run the `command` in PowerShell standalone: `npx -y @modelcontextprotocol/server-github`. If that fails, install Node, or check your network. |
| "Permission prompt on every command"             | Add the command pattern to `permissions.allow[]` in settings.json — examples in the starter above. Run `/permissions` in Claude for the live editor. |
| "Skills don't show up"                           | Check folder structure, restart Claude, confirm `SKILL.md` is properly formatted (frontmatter + description). |
| "Browser connector worked, MCP doesn't"          | Different system. Browser connectors use OAuth popups; desktop MCPs use stdio/HTTP. Re-auth via the MCP package's docs. |
| "GitHub MCP returns 401"                         | Token is wrong scope, expired, or pointing at a different account. Regenerate with the scopes from §3. |
| "Claude can't find the repo"                     | Either `cd` into it in PowerShell before running `claude`, or use Claude Desktop's "Open folder" option. |
| "I lose context between sessions"                | Commit work-in-progress to a branch (`git commit`) and reference branch names — Claude reads commits to pick up state. |

---

## 6. Smoke test

Open a fresh local Claude session inside your repo clone and try:

```
What branch am I on?
Read prompts/_lincoln_handoff.md
List all files in assets/carousels/3prompts/
```

If those all work without permission prompts, you're set.

---

## 7. For this project specifically

Once local Claude is alive:

1. **Pull the latest** — branch `claude/ccb-review-WrV25` on `seangoesnorth-png/seangoesnorth.com`
2. **Lincoln video** — paste this prompt:
   > Read `prompts/_lincoln_handoff.md` and execute it on `lincoln.mp4` in `G:\01_SeanGoes_North_Active\03_Assets\`.
3. **Substack post** — paste the post text from the earlier message into Substack's editor; or ask local Claude to email it via your post-by-email address.
4. **Carousel iteration** — `prompts/` has the existing brief; ask local Claude to re-render or tweak.

---

## 8. When something specific breaks

Paste the exact error message into a chat with cloud-me (this session) and I'll patch the runbook + give you the exact fix. The runbook gets better every time something fails.
