# claude-watch

> **Real-time Claude Code session watcher — usage, cost, activity, alerts** — Python-based session watcher that monitors Claude Code sessions, tracks token cost, fires alerts, and produces searchable activity logs

<p align="center"><a href="https://github.com/hmzainjamil/claude-watch">Repository</a> · <a href="https://github.com/hmzainjamil/claude-watch/commits/main">Commits</a> · <a href="https://github.com/hmzainjamil/claude-watch/issues">Issues</a></p>
<p align="center"><img alt="Documentation" src="https://img.shields.io/badge/documentation-deep%20editorial-lightgrey"> <img alt="Lifecycle" src="https://img.shields.io/badge/lifecycle-active-success"></p>

<!-- HMZ DEEP README v1 -->

## At a glance

| Field | Current state |
|---|---|
| Repository | claude-watch |
| Visibility | Public |
| Lifecycle | Active |
| Evidence basis | Current repository documentation and source-visible material |

## Why this exists

**Real-time Claude Code session watcher — usage, cost, activity, alerts** — Python-based session watcher that monitors Claude Code sessions, tracks token cost, fires alerts, and produces searchable activity logs

The README documents the monitoring scope and separates observed system behavior from claims about reliability, uptime, or external production use.

## 🧠 CONCEPTS

Each row maps a concept to a real file. Click `[Source]` to read the actual code.

| # | Concept | Location | Description |
|---|---|---|---|
| 1 | **Plugin manifest** | `.claude-plugin/plugin.json` | Claude Code plugin entry point · [Source](https://github.com/hmzainjamil/claude-watch/blob/main/.claude-plugin/plugin.json) |
| 2 | **Marketplace entry** | `.claude-plugin/marketplace.json` | Install via plugin marketplace · [Source](https://github.com/hmzainjamil/claude-watch/blob/main/.claude-plugin/marketplace.json) |
| 3 | **Codex variant** | `.codex-plugin/skill.json` | Same skill exposed to Codex · [Source](https://github.com/hmzainjamil/claude-watch/blob/main/.codex-plugin/skill.json) |
| 4 | **Skill spec** | `SKILL.md` | Trigger keywords and behavior contract · [Source](https://github.com/hmzainjamil/claude-watch/blob/main/SKILL.md) |
| 5 | **Watch script** | `scripts/watch.py` | Core event-tail loop · [Source](https://github.com/hmzainjamil/claude-watch/blob/main/scripts/watch.py) |
| 6 | **Transcribe script** | `scripts/transcribe.py` | Screen-recording → text · [Source](https://github.com/hmzainjamil/claude-watch/blob/main/scripts/transcribe.py) |
| 7 | **Whisper integration** | `scripts/whisper.py` | Local Whisper for audio transcription · [Source](https://github.com/hmzainjamil/claude-watch/blob/main/scripts/whisper.py) |
| 8 | **Frames extractor** | `scripts/frames.py` | Video → frame samples for OCR · [Source](https://github.com/hmzainjamil/claude-watch/blob/main/scripts/frames.py) |
| 9 | **Session start hook** | `hooks/scripts/session_start.sh` | Fires on every new session · [Source](https://github.com/hmzainjamil/claude-watch/blob/main/hooks/scripts/session_start.sh) |
| 10 | **Release workflow** | `.github/workflows/release.yml` | Automated semantic releases · [Source](https://github.com/hmzainjamil/claude-watch/blob/main/.github/workflows/release.yml) |

## ⚙️ HOW IT WORKS

```
┌─────────────────────────────────────────────────────────────┐
│  Input  →  claude-watch  →  Output                                    │
├─────────────────────────────────────────────────────────────┤
│  1. Prompt / file / event lands at the entry point          │
│  2. Manifest resolves trigger → concrete handler            │
│  3. Handler invokes tools / scripts / sub-agents in order   │
│  4. Output is structured (JSON / Markdown / HTML / file)    │
│  5. Side-effects: logs, alerts, artifacts, commits          │
└─────────────────────────────────────────────────────────────┘
```

The architecture is intentionally narrow: one entry point, one router, deterministic handlers. No hidden global state, no `process.env` surprises, no daemons phoning home.

## 🚀 Install

## 🧩 Usage

Once installed, invoke the primary surface from any Claude Code session:

```text
# example 1 — basic trigger
use claude-watch to ...

# example 2 — explicit skill name
@skill:claude-watch run on <input>

# example 3 — CLI-style invocation
npx claude-watch --help
```

Each concept in the table above is independently usable — you don't have to wire the whole thing up at once.

## ⚙️ Configuration

All configuration is file-based. No web dashboards, no SaaS sign-up, no env-var roulette.

| Setting | Default | Description |
|---|---|---|
| `LOG_LEVEL` | `info` | One of: `debug`, `info`, `warn`, `error` |
| `MODEL_TIER` | `tier0` | Route to free local/cloud models before paid |
| `MAX_TOKENS` | `8192` | Hard cap per invocation |
| `CACHE_TTL` | `3600` | Seconds before refetching upstream data |
| `OUTPUT_DIR` | `~/Downloads` | Where generated artifacts land |
| `DRY_RUN` | `false` | Print plan, skip side-effects |
| `RETRY_COUNT` | `3` | Network/transient failure retries |
| `TIMEOUT_MS` | `30000` | Per-call timeout |
| `TELEMETRY` | `off` | Never on by default |
| `VERBOSE_ERRORS` | `true` | Full stacks in dev, redacted in prod |

## Validation and evidence

No dedicated test or evaluation section was available in the current README.

## 🛰️ Security posture

- Secrets: never committed; use a secrets manager (1Password CLI, doppler, age-encrypted .env).
- Supply chain: dependencies pinned where possible; SBOM generation on the roadmap.
- Sandbox: tools that touch the filesystem default to dry-run preview.
- Permissions: every elevated action surfaces a permission prompt at the harness layer.
- Audit log: every tool call appends to a structured log under `~/.claude/`.

## Limitations

- Monitoring effectiveness depends on the actual signals and runtime environment.
- Reliability claims require time-bounded operational evidence.
- External provider behavior is not controlled by this repository.



## Maintainer

[hmzainjamil](https://github.com/hmzainjamil)