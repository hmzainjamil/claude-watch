# claude-watch

> **Claude Code session monitor — real-time usage tracking, cost alerts, and session analytics**

<p align="center">
  <img src="https://img.shields.io/github/stars/hmzainjamil/claude-watch?style=for-the-badge&color=FFD700&labelColor=222" alt="Stars"/>
  <img src="https://img.shields.io/github/forks/hmzainjamil/claude-watch?style=for-the-badge&color=00BFFF&labelColor=222" alt="Forks"/>
  <img src="https://img.shields.io/github/issues/hmzainjamil/claude-watch?style=for-the-badge&color=FF4500&labelColor=222" alt="Issues"/>
  <img src="https://img.shields.io/github/issues-pr/hmzainjamil/claude-watch?style=for-the-badge&color=9B59B6&labelColor=222" alt="PRs"/>
  <img src="https://img.shields.io/github/last-commit/hmzainjamil/claude-watch?style=for-the-badge&color=2ECC71&labelColor=222" alt="Last Commit"/>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Claude_Code-CC785C?style=flat&labelColor=555" alt="Claude Code"/>
  <img src="https://img.shields.io/badge/Python-3776AB?style=flat&labelColor=555&logo=python&logoColor=white" alt="Python"/>
  <img src="https://img.shields.io/badge/yt--dlp-FF0000?style=flat&labelColor=555" alt="yt-dlp"/>
  <img src="https://img.shields.io/badge/ffmpeg-007808?style=flat&labelColor=555" alt="ffmpeg"/>
  <img src="https://img.shields.io/badge/Whisper-412991?style=flat&labelColor=555" alt="Whisper"/>
  <img src="https://img.shields.io/badge/Groq-F55036?style=flat&labelColor=555" alt="Groq"/>
  <img src="https://img.shields.io/badge/MIT-green?style=flat&labelColor=555" alt="MIT"/>
</p>

---

## Why This Exists

Video tutorials are the best way to learn technical topics — but watching a 45-minute lecture to extract 10 key concepts is brutally inefficient. `claude-watch` solves this: paste a URL, it downloads the video, extracts scene-aware frames, transcribes audio, and generates structured study notes in a persistent library.

Unlike `claude-video` which samples frames uniformly (wasteful on static slides), `claude-watch` uses **scene-change detection** so budget goes to actual transitions, not 60 identical frames of the same slide.

---

## At a Glance

| Feature | Detail |
|---------|--------|
| Input | YouTube URL, local file, any yt-dlp-compatible URL |
| Frame extraction | Scene-change aware (ffmpeg) + coverage-floor every 45s |
| Transcription | Native captions (free), Whisper fallback (Groq preferred) |
| Notes format | TLDR → Key Concepts → Notes per scene → Code blocks → Diagrams |
| Library | Persistent `~/claude-watch/library/<slug>/` — re-run = cache hit |
| Max frames | 80 (configurable) |
| Best accuracy | Videos under 30 minutes |
| Whisper limit | 25MB audio (~50min mono 16kHz) |
| Cache key | `YYYY-MM-DD-<title>-<sha1(url+range)[:4]>` |
| License | MIT |

---

## 🧠 CONCEPTS

| Concept | What It Means |
|---------|--------------|
| **Scene-change detection** | ffmpeg detects frame transitions — frame inserted at each visual change |
| **Coverage floor** | Every 45s gets at least one frame — prevents static slides being missed |
| **Slug** | Library folder name = date + title + 4-char URL hash |
| **Cache hit** | Same URL + same focus range = no re-download, only notes regenerate |
| **Whisper** | OpenAI speech-to-text model — used when native captions unavailable |
| **Groq Whisper** | Groq's hosted `whisper-large-v3` — faster + cheaper than OpenAI |
| **Focus range** | `--start`/`--end` flags — creates separate slug, doesn't overwrite full video |
| **SKILL.md** | Claude Code plugin format — defines slash command behavior |
| **Library** | `~/claude-watch/library/` — persistent notes archive across sessions |
| **meta.json** | Per-video metadata file — delete to force re-run |

### 🔥 Hot

| Feature | Why Learners Love It | Source |
|---------|---------------------|--------|
| Scene-aware frames | 10x fewer frames than uniform sampling for same comprehension | [HMZ](https://github.com/hmzainjamil) |
| Persistent library | Notes searchable weeks later — not buried in chat history | [HMZ](https://github.com/hmzainjamil) |
| Code extraction | Every code-on-screen frame → runnable fenced block in notes | [HMZ](https://github.com/hmzainjamil) |

---

## ⚙️ HOW IT WORKS

```
/claude-watch <url> [topic]
  │
  ├─1─ yt-dlp downloads video + extracts native captions
  │
  ├─2─ ffmpeg detects scene changes
  │     + inserts coverage-floor frame every 45s (static slides)
  │
  ├─3─ Transcription:
  │     ├── Native captions available → use them (free)
  │     └── No captions → Groq whisper-large-v3 (fallback OpenAI)
  │
  ├─4─ Claude reads ALL frames as images
  │     + timestamped transcript
  │     → writes notes.md to strict template:
  │         ## TLDR
  │         ## Key Concepts (with timestamps)
  │         ## Notes (per scene: screenshot + text + synthesis)
  │         ## Code & Commands (runnable blocks)
  │         ## Diagrams Referenced
  │         ## Open Questions
  │
  └─5─ Save to ~/claude-watch/library/<slug>/
        notes.md, frames/, meta.json, transcript.vtt
```

**Cache check:** Before step 1, checks if `~/claude-watch/library/<slug>/meta.json` exists. If yes, skips download + transcription. Only regenerates frames + notes if content changed.

---

## 🚀 INSTALL

### Claude Code (plugin)

```bash
# Install via marketplace
claude plugin marketplace add devinilabs/claude-watch
claude plugin install claude-watch@claude-watch
```

### claude.ai (web skill)

```bash
# Download latest .skill file from Releases
# Settings → Capabilities → Skills → + → upload claude-watch.skill
```

### Manual (Codex or custom)

```bash
git clone https://github.com/hmzainjamil/claude-watch ~/.codex/skills/claude-watch
```

### System dependencies

```bash
# macOS
brew install yt-dlp ffmpeg

# Ubuntu/Debian
apt install yt-dlp ffmpeg

# Python deps (for Whisper fallback)
pip3 install openai groq
```

### Configure API keys

```bash
mkdir -p ~/.config/claude-watch
cat > ~/.config/claude-watch/.env << 'EOF'
GROQ_API_KEY=gsk_...
OPENAI_API_KEY=sk-...   # optional fallback
EOF
chmod 0600 ~/.config/claude-watch/.env
```

---

## 📟 USAGE

### Basic usage

```bash
/claude-watch https://youtu.be/<video-id>
/claude-watch https://youtu.be/<video-id> backpropagation intuition
```

### Local file

```bash
/claude-watch ~/Lectures/cs231n.mp4 backpropagation derivation
```

### Focus on a section

```bash
/claude-watch https://youtu.be/<id> --start 5:00 --end 25:00
```

### High-res slides

```bash
/claude-watch <url> --resolution 1024   # for tiny code text on slides
```

### Disable Whisper (frames only when no captions)

```bash
/claude-watch <url> --no-whisper
```

### All flags

```bash
/claude-watch <url> [topic] \
  [--start MM:SS] \
  [--end MM:SS] \
  [--max-frames 80] \
  [--resolution 1024] \
  [--scene-threshold 0.3] \
  [--max-gap 45] \
  [--whisper groq|openai] \
  [--no-whisper] \
  [--out-dir ~/my-notes/]
```

### Force re-run (ignore cache)

```bash
rm ~/claude-watch/library/<slug>/meta.json
/claude-watch <url>
```

---

## ⚙️ CONFIGURATION

| Flag / Config | Default | Description |
|--------------|---------|-------------|
| `--start` | 0:00 | Start timestamp for focus range |
| `--end` | video length | End timestamp for focus range |
| `--max-frames` | 80 | Hard cap on frames sent to Claude |
| `--resolution` | 720 | Frame height in pixels (higher = better for code) |
| `--scene-threshold` | 0.3 | ffmpeg scene change sensitivity (0=all, 1=none) |
| `--max-gap` | 45 | Seconds before coverage-floor frame is inserted |
| `--whisper` | `groq` | Whisper backend: `groq` or `openai` |
| `--no-whisper` | off | Skip Whisper even if no captions |
| `--out-dir` | `~/claude-watch/library/` | Custom output directory |
| `GROQ_API_KEY` | — | Required for Groq Whisper |
| `OPENAI_API_KEY` | — | Optional OpenAI Whisper fallback |

---

## 💡 TIPS AND TRICKS

### Getting Better Notes
| Tip | Detail | Source |
|-----|--------|--------|
| Name your topic | `/claude-watch <url> gradient descent` — topic anchors Claude's synthesis | [HMZ](https://github.com/hmzainjamil) |
| Use focus range | 5-25min segment → sharper notes than full 90min lecture | [HMZ](https://github.com/hmzainjamil) |
| Boost resolution | `--resolution 1024` for slides with 8pt font code | [HMZ](https://github.com/hmzainjamil) |

### Performance
| Tip | Detail | Source |
|-----|--------|--------|
| Let cache do its job | Second run on same URL is free — only notes regenerate | [HMZ](https://github.com/hmzainjamil) |
| Groq Whisper > OpenAI | 3x faster, 50% cheaper — default for a reason | [HMZ](https://github.com/hmzainjamil) |
| Lower scene threshold | `--scene-threshold 0.1` for fast-cut demos with many transitions | [HMZ](https://github.com/hmzainjamil) |

### Library Management
| Tip | Detail | Source |
|-----|--------|--------|
| Search your library | `grep -r "gradient descent" ~/claude-watch/library/ --include="notes.md"` | [HMZ](https://github.com/hmzainjamil) |
| Export to Obsidian | Symlink library to Obsidian vault — notes auto-appear | [HMZ](https://github.com/hmzainjamil) |
| Archive old notes | `zip -r notes-archive.zip ~/claude-watch/library/` quarterly | [HMZ](https://github.com/hmzainjamil) |

### Token Budget
| Tip | Detail | Source |
|-----|--------|--------|
| Stay under 30 min | Claude processes all frames in one pass — longer = token spike | [HMZ](https://github.com/hmzainjamil) |
| Use `--max-frames 40` | Halves token cost for lectures where slides rarely change | [HMZ](https://github.com/hmzainjamil) |
| Split long lectures | Run 3×30min segments instead of 1×90min for better quality + lower cost | [HMZ](https://github.com/hmzainjamil) |

---

## 🔧 TROUBLESHOOTING

| Issue | Cause | Fix |
|-------|-------|-----|
| yt-dlp error | Private video or unsupported URL | Use local file: `/claude-watch ~/video.mp4` |
| No frames extracted | ffmpeg not installed | `brew install ffmpeg` |
| Whisper 413 error | Audio > 25MB | Use `--start`/`--end` to split, or `--no-whisper` |
| Notes lack code blocks | Low resolution — code text blurry | Add `--resolution 1024` |
| Cache not hit | Different `--start`/`--end` creates new slug | This is intended — delete old slug manually if needed |
| Groq rate limit | High volume usage | Set `--whisper openai` as fallback |
| Empty TLDR section | Video has no clear structure | Provide `topic` argument for better anchoring |
| Plugin not found | Not installed | Run `claude plugin install claude-watch@claude-watch` |

---

## 📊 ARCHITECTURE

```
~/claude-watch/
└── library/
    └── 2026-05-24-cs231n-lecture5-a3f2/
        ├── meta.json          ← cache key, source URL, flags used
        ├── notes.md           ← structured study notes
        ├── transcript.vtt     ← timestamped transcript
        └── frames/
            ├── 00m15s.jpg     ← scene-change frame
            ├── 01m02s.jpg
            └── ...

~/.config/claude-watch/
└── .env                       ← API keys (mode 0600)

~/.claude/skills/claude-watch/
└── SKILL.md                   ← slash command definition
```

---

## 🗺️ ROADMAP

| Status | Feature | ETA |
|--------|---------|-----|
| ✅ Done | Scene-aware frame extraction | Shipped |
| ✅ Done | Coverage-floor frames (every 45s) | Shipped |
| ✅ Done | Groq + OpenAI Whisper backends | Shipped |
| ✅ Done | Persistent library with cache | Shipped |
| ✅ Done | Focus range with separate slugs | Shipped |
| 🔄 In progress | Obsidian vault sync | Jun 2026 |
| 📋 Planned | Batch URL processing | Jun 2026 |
| 📋 Planned | Playlist support | Jul 2026 |
| 📋 Planned | Anki flashcard export from notes | Jul 2026 |
| 💡 Idea | Semantic search across library | Q4 2026 |
| 💡 Idea | Quiz generation from notes | Q4 2026 |

---

## ☠️ STARTUPS / BUSINESSES

What claude-watch replaces:

| Tool | Price/mo | Limitation vs claude-watch | Saving |
|------|----------|---------------------------|--------|
| Otter.ai (Pro) | $17 | Transcript only, no visual notes | $204/yr |
| Notta (Pro) | $14 | No scene analysis, no code extraction | $168/yr |
| Glasp (Pro) | $12 | YouTube highlights only, no structured notes | $144/yr |
| NotebookLM | Free | No persistent library, no code blocks | Convenience |
| Manual notes | 2hr/lecture | You do the work | $200 your time |
| **Total saved** | **~$700+/yr** | | |

---

## Star History

[![Star History Chart](https://api.star-history.com/svg?repos=hmzainjamil/claude-watch&type=Date)](https://star-history.com/#hmzainjamil/claude-watch&Date)

---

Built by [HMZ](https://github.com/hmzainjamil)

---

## 📋 NOTES FORMAT SPECIFICATION

Every `notes.md` follows this exact template:

```markdown
# [Video Title]
**Source:** <url>
**Processed:** YYYY-MM-DD HH:MM UTC
**Duration:** MM:SS (focused range: HH:MM:SS – HH:MM:SS)
**Topic focus:** [user-provided topic or "general"]

---

## TLDR
3-4 sentence synthesis of the most important ideas.

---

## Key Concepts
- **[Concept 1]** `[MM:SS]` — explanation
- **[Concept 2]** `[MM:SS]` — explanation
- ...

---

## Notes

### Scene 1 — [MM:SS]
![frame](frames/00m15s.jpg)
**On screen:** [text visible in frame]
**Said:** [relevant transcript excerpt]
**Synthesis:** [Claude's explanation]

### Scene 2 — [MM:SS]
...

---

## Code & Commands
\`\`\`python
# All code blocks extracted from screen
\`\`\`

---

## Diagrams Referenced
- [MM:SS] — [description of diagram]

---

## Open Questions
- [Question 1]
- [Question 2]
```

---

## 📊 FRAME BUDGET ALLOCATION

How 80 frames are distributed for a 60-minute lecture:

| Strategy | Frame Count | When |
|----------|------------|------|
| Scene changes | ~45 | Every visual transition |
| Coverage floor | ~20 | Static sections (every 45s) |
| Reserve | ~15 | User focus-range bonus density |
| **Total** | **80 max** | |

Scene-aware vs uniform comparison for a 60-min lecture with 20 slow slides:

| Method | Frames on slides | Frames on demos | Quality |
|--------|-----------------|-----------------|---------|
| Uniform (every 45s) | 80 (all identical) | 0 | ❌ Wasteful |
| Scene-aware (claude-watch) | 20 (1 per slide) | 60 (all transitions) | ✅ Efficient |

---

## 🔗 COMPATIBLE PLATFORMS

| Platform | Support | Notes |
|----------|---------|-------|
| YouTube | ✅ Full | Captions + download |
| YouTube Shorts | ✅ Full | Max 60s, captions usually available |
| Vimeo | ✅ Full | yt-dlp supported |
| Coursera (public) | ✅ Partial | No captions — Whisper fallback |
| Local MP4/MKV/MOV | ✅ Full | No download step |
| Loom | ✅ Full | Public videos |
| Twitch VODs | ✅ Partial | Large files — use `--start`/`--end` |
| Netflix / private | ❌ No | DRM-protected |
| Zoom recordings | ✅ If you have the file | Pass as local path |








































































