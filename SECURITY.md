# Security and data handling

## Data processed

The skill accepts a video URL or local video path. It may download/cache the video, transcript, and scene data and write screenshots and generated notes under the user's local library path. These artifacts can contain private speech, identifying details, workplace information, or licensed content.

## External transfer

When usable captions are missing, the documented fallback can send extracted audio to the configured Groq or OpenAI Whisper API. Obtain the user's explicit consent before that transfer; prior explicit authorization is sufficient. If consent is absent or declined, use the frames-only path with `--no-whisper` when possible. Review the selected provider's current retention and processing terms before handling sensitive recordings. This repository does not certify provider privacy or retention behavior.

## Credentials and local files

- Store provider keys in the documented local config file with restrictive permissions; never commit keys.
- Review the output directory after the first run. Transcripts, frames, downloads, and notes can remain on disk between runs.
- The skill instructions say not to delete the library directory automatically. Users should decide whether to retain or remove generated artifacts.
- Restrict Bash, Read, and Write permissions to the paths and tools needed for the task.

## Verification status

These notes describe checked-in instructions and source-visible behavior. No install, download, provider call, credential validation, cleanup test, or runtime test was performed for this documentation update.

Report security issues through the repository's GitHub Security Advisories if enabled, or contact the repository maintainer privately. Do not publish credentials or private recordings in an issue.
