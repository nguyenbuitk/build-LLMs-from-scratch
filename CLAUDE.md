# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository purpose

This is a personal study repository for the book **"Build a Large Language Model (From Scratch)"** by Sebastian Raschka. It is not (yet) a copy of the book's companion code repository (`github.com/rasbt/LLMs-from-scratch`). The two goals of this repo are:

1. Work through the book PDF and write/run the code demos for each chapter.
2. Summarize the author's companion YouTube videos (one per chapter) into Vietnamese-language markdown notes.

## Current contents

- `Build a Large Language Model (From Scratch) MEAP V08.pdf` — the book itself (MEAP early-access build, version 8). Treat as read-only reference material.
- `yt-video-1.md` — Vietnamese-language summary of the first companion video ("Set up your code environment"), covering environment setup with `uv`, virtual environments, and options for running without a local GPU (Google Colab, Lightning AI Studio).

No source code, dependency manifest, or test suite exists yet — this repo is at the very start of the study process.

## Conventions to follow as the repo grows

- **Video notes**: name additional summaries `yt-video-N.md` following the existing `yt-video-1.md` pattern, one per companion video/chapter. Keep them in Vietnamese, matching the existing tone and structure (section headers, timestamps, key takeaways) of `yt-video-1.md`.
- **Code demos**: when adding runnable code for a chapter, prefer following the structure and tooling of the official companion repo referenced in the notes (`github.com/rasbt/LLMs-from-scratch`) — e.g. `uv` for environment/dependency management, chapter-numbered folders, and a `requirements.txt`/`pyproject.toml` — unless the user directs otherwise. Do not assume this structure exists until it's actually created.
- Do not treat the PDF as something to edit or regenerate; it's reference material to work from, not an output.
