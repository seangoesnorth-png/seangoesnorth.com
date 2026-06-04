# HANDOFF — Lincoln video text animation

**Status:** Work blocked in the cloud session — egress allowlist won't let us pull the MP4 from Dropbox/Drive/anywhere. Restart in a **local Claude Code session** that runs on the PC, where the file is reachable directly.

## How to start the local session

**Windows PowerShell:**
```powershell
# one-time install (skip if already installed)
npm install -g @anthropic-ai/claude-code

cd G:\01_SeanGoes_North_Active\03_Assets
claude
```

That `claude` command launches a Claude Code session that runs ON the PC — full filesystem access, no allowlist, can read `lincoln.mp4` directly, can install/run ffmpeg locally.

## Paste this prompt into the new local Claude

> I'm continuing work from a cloud session that was blocked by network policy. The video file `lincoln.mp4` is in the current directory (`G:\01_SeanGoes_North_Active\03_Assets`). I want you to animate text on it per this spec, then save the result beside it as `lincoln_animated.mp4`. Also clone or pull `https://github.com/seangoesnorth-png/seangoesnorth.com` branch `claude/ccb-review-WrV25` into a working folder and drop the final output at `assets/video/lincoln_animated.mp4`, commit, and push.
>
> **Animation spec:**
> - Output 9:16 vertical. If source isn't already 9:16, scale to fit and letterbox/pillarbox with **black** padding.
> - Keep the original audio, untouched.
> - **Centered** text reveals, **white** with a **thin black outline** (~2px) for legibility over any footage.
> - Fonts: **Instrument Serif** for the prose (italic emphasis on the words below); **JetBrains Mono** for the sober-count kicker. If those fonts aren't installed locally, fall back to Georgia / Consolas (or install via Google Fonts ttfs).
> - Each line fades IN (~0.4s), HOLDS, fades OUT (~0.4s) before the next line appears. Pace based on the source duration — use `ffprobe` to read it first.
>
> **Text beats (verbatim):**
>
> 1. "Lincoln failed for almost *thirty* years before he held a country together."
> 2. *(beat — black/no text)*
> 3. "I failed for thirty, too."
> 4. *(beat)*
> 5. "Then I started from *scratch* —"
> 6. "and spent the last two years and ten months healing the damage I'd done."
> 7. *(beat)*
> 8. ***"Good things take time."*** *(longer hold, slightly larger)*
> 9. **`2 YEARS · 10 MONTHS · SOBER`** (JetBrains Mono, holds to end of clip)
>
> Italicized words ride one beat brighter — render them larger or in `--accent-yellow` (`#F5D547`) per the seangoesnorth design system if it reads cleanly, otherwise just italics in white.
>
> Use `ffmpeg` with `drawtext` filters (one per beat) — or `subtitles` + an ASS file if you prefer per-line styling. Either is fine. Don't re-encode the audio. Don't change the source resolution beyond the 9:16 pad/scale.

## Context this session built (for reference)

- Branch in use: `claude/ccb-review-WrV25`
- Recent commits include 3 PROMPTS carousel frames + `/prompts/` free page (Body / Mind / Craft)
- Site design system: navy `#0A0E12` bg, cream `#E8EEF2` text, accent yellow `#F5D547`, accent red `#E04545`, accent cyan `#4FB8D9`, Instrument Serif + JetBrains Mono
- 3 PROMPTS carousel pillar accents: BODY = yellow, MIND = purple, CRAFT = orange
- Sean's sober milestone in copy: **2 years, 10 months**
