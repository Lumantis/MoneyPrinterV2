# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

MoneyPrinterV2 (MPV2) is a Python 3.12 CLI tool that automates four online money-making workflows:

1. **YouTube Shorts** — full pipeline: LLM script → TTS voiceover → AI image generation → MoviePy composite → subtitle rendering → Selenium upload to YouTube Studio
2. **Twitter/X Bot** — LLM-generated tweet content → automated posting via Selenium with CRON scheduling
3. **Affiliate Marketing** — Amazon product scraping → LLM pitch generation → Twitter posting
4. **Local Business Outreach** — Google Maps scraping (Go binary) → email extraction → SMTP cold outreach with HTML templates

There is no web UI, no REST API, no test suite, no CI, and no linting config.

## Running the Application

```bash
# First-time setup
cp config.example.json config.json   # fill in all required values
python -m venv venv && source venv/bin/activate
pip install -r requirements.txt

# macOS quick setup (auto-configures Ollama, ImageMagick, Firefox profile)
bash scripts/setup_local.sh

# Preflight check (validates all services are reachable before running)
python scripts/preflight_local.py

# Run the interactive menu
python src/main.py
```

The app **must** be run from the project root. `python src/main.py` adds `src/` to `sys.path`, so all imports use bare module names (e.g., `from config import *`, not `from src.config import *`).

## Architecture

### Entry Points

- `src/main.py` — interactive CLI menu loop (primary entry point)
- `src/cron.py` — headless runner for scheduled tasks: `python src/cron.py <platform> <account_uuid>`

### Provider Pattern

Two service categories use a string-based dispatch pattern configured in `config.json`:

| Category | Config key | Options |
|---|---|---|
| LLM | `ollama_model` | Ollama (via `ollama` Python SDK). If empty, user picks interactively at startup. |
| Image gen | `nanobanana2_*` | Nano Banana 2 (wraps Gemini image generation API) |
| STT | `stt_provider` | `local_whisper` or `third_party_assemblyai` |

- LLM always uses the local Ollama server (no cloud LLM calls).
- Image generation always uses Nano Banana 2 / Gemini API.
- Speech-to-text is used for subtitle generation; local Whisper avoids external API costs.

### Key Modules

| File | Role |
|---|---|
| `src/main.py` | CLI menu loop, account management, task dispatch |
| `src/cron.py` | Headless runner spawned by the scheduler |
| `src/llm_provider.py` | `generate_text(prompt)` — unified LLM interface via Ollama SDK |
| `src/config.py` | 30+ getter functions; re-reads `config.json` on every call (no caching). `ROOT_DIR` = project root via `os.path.dirname(sys.path[0])` |
| `src/cache.py` | JSON file persistence in `.mp/` (accounts, videos, posts, products) |
| `src/constants.py` | Menu strings and Selenium CSS/XPath selectors for YouTube Studio, X.com, Amazon |
| `src/classes/YouTube.py` | Most complex class: full short-video generation and upload pipeline |
| `src/classes/Twitter.py` | Selenium automation against x.com |
| `src/classes/AFM.py` | Amazon product scraping + LLM pitch generation |
| `src/classes/Outreach.py` | Google Maps scraper invocation (Go binary) + yagmail SMTP sender |
| `src/classes/Tts.py` | KittenTTS wrapper for voiceover generation |

### Data Storage

All persistent state lives in `.mp/` at the project root as JSON files:
- `youtube.json` — YouTube account configs and video history
- `twitter.json` — Twitter account configs and post history
- `afm.json` — affiliate product and pitch history

The `.mp/` directory also serves as scratch space for temporary WAV, PNG, SRT, and MP4 files. Non-JSON files are cleaned on each run by `rem_temp_files()`.

### Browser Automation

Selenium uses **pre-authenticated Firefox profiles** — it never handles login flows. The Firefox profile path is stored per-account in the cache JSON and also in `config.json` as a global default. Set `headless: true` in config to run the browser invisibly.

### CRON Scheduling

Uses Python's `schedule` library (in-process scheduler, not OS cron). Each scheduled job spawns: `subprocess.run(["python", "src/cron.py", platform, account_id])`.

## Configuration

All config lives in `config.json` at the project root. Copy `config.example.json` as a starting point. See `docs/Configuration.md` for the full reference.

Key external dependencies to configure:

| Dependency | Config key | Notes |
|---|---|---|
| ImageMagick | `imagemagick_path` | Required for MoviePy subtitle rendering. Path to `magick.exe` (Windows) or `/usr/bin/convert` (Linux/macOS). |
| Firefox profile | `firefox_profile` | Must be pre-logged-in to target platforms (YouTube, X.com, Amazon). |
| Ollama | `ollama_base_url`, `ollama_model` | Local LLM server. Default URL: `http://127.0.0.1:11434`. |
| Gemini API | `nanobanana2_api_key` | For AI image generation. Falls back to `GEMINI_API_KEY` env var. |
| Go runtime | — | Only needed for the Outreach Google Maps scraper binary. |
| AssemblyAI | `assembly_ai_api_key` | Only needed if `stt_provider` is `third_party_assemblyai`. |

## Common Patterns and Pitfalls

- **No caching in config.py** — every `get_*()` call reads from disk. Do not assume values are cached between calls.
- **Import paths** — always use bare names (`from config import *`), never `from src.config import *`.
- **Temp file cleanup** — `.mp/` non-JSON files are deleted at startup; do not store anything important there between runs.
- **Selenium selectors** — all CSS/XPath selectors live in `src/constants.py`. Update them there when platform UIs change.
- **Go binary** — the Google Maps scraper is downloaded and compiled at runtime by `Outreach.py`; Go must be installed on the system.

## Contributing

PRs go against `main`. One feature/fix per PR. Open an issue first for non-trivial changes. Use the `WIP` label for in-progress PRs. See `docs/Roadmap.md` for planned features.
