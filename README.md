# claude-watch

A video-to-study-notes skill. It takes a tutorial, lecture, or local recording, extracts a transcript and selected frames, then produces structured Markdown notes with timestamps and screenshots.

> **Status:** README and source tree inspected. Installation, provider calls, output quality, and tests were not run or verified in this update.

The repository's SKILL.md metadata identifies devinilabs/claude-watch as the upstream project. This repository also contains Claude Code plugin metadata and a Codex skill manifest.

## How it works

1. Checks for required local tools and transcription credentials.
2. Downloads a video or reads a local video path.
3. Uses ffmpeg to select frames and captions when available.
4. Uses a configured Whisper API provider as a transcription fallback.
5. Produces section-by-section notes with timestamps and screenshots.

The workflow writes notes under ~/claude-watch/library/<slug>/. Setup stores configuration under ~/.config/claude-watch/.env and documents restrictive file permissions.

## Requirements and start

The documented preflight command is:

    python3 scripts/setup.py --check

If requirements are missing, review the installer before running it. On macOS it may install ffmpeg and yt-dlp with Homebrew and create a local configuration file. A transcription API key is needed for videos without usable captions; --no-whisper skips transcription.

The primary command is:

    python3 scripts/watch.py <video-url-or-path>

This README describes commands from SKILL.md and the scripts. They were not executed here. Follow the instructions in [SKILL.md](SKILL.md) for time ranges, frame limits, and output options.

## Privacy and safety

- Video downloads and screenshots can contain private or confidential material.
- Whisper fallback sends audio to the configured external provider. Review provider policy and obtain approval before using sensitive recordings.
- Keep API keys in the local config file. Do not commit credentials or generated notes/screenshots containing sensitive information.
- Generated notes may omit context or misread frames. Verify important statements against the original video.
- No transcription quality, privacy certification, or production readiness is claimed.

## Repository map

- [Skill instructions](SKILL.md)
- [Executable scripts](scripts/)
- [Tests](tests/)
- [Change history](CHANGELOG.md)
- [License](LICENSE)

## Validation

The repository includes tests, but no tests were run for this README update. A checked-in test file is not evidence of a passing result.