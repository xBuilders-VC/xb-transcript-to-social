# transcript-to-social

A Claude Code plugin that converts transcripts, session notes, and talk summaries into ready-to-post social media content.

## What it does

Point it at an audio file, video file, transcript, YouTube URL, or pasted text and get back:

- **Twitter/X threads** — multi-tweet threads with character counts, verified @handles, hashtags
- **LinkedIn posts** — hook + insights + hashtags, up to 3000 chars
- **Bluesky threads** — same structure as Twitter, 300-char limit

The full pipeline: **audio/video → AssemblyAI transcription → social posts**. Works end-to-end from raw `.mp3`/`.mp4` files.

Built from real-world use: originally created to process 23 ETHDenver 2026 conference sessions from AssemblyAI transcripts into tweet threads.

## Install

In Claude Code, run these two commands:

```
/plugin marketplace add srwillinger/transcript-to-social
/plugin install transcript-to-social@transcript-to-social
```

## Requirements

For audio/video transcription:
```bash
pip install assemblyai
export ASSEMBLYAI_API_KEY=your_key_here
```
Get a free API key at [assemblyai.com](https://www.assemblyai.com). The first 100 hours are free.

For YouTube support (optional):
```bash
brew install yt-dlp   # macOS
# or: pip install yt-dlp
```

## Usage

### As a slash command

```
/transcript-to-social [input] [options]
```

**Arguments:**
- `[input]` — audio/video file, transcript file, YouTube URL, or omit to paste inline
- `--platform twitter|linkedin|bluesky` — generate one platform only (default: all)
- `--speakers speakers.json` — path to a JSON file with verified speaker handles

**Examples:**
```
/transcript-to-social ~/talks/session1.mp4
/transcript-to-social ~/talks/keynote.mp3 --platform twitter
/transcript-to-social ~/talks/session.mp3 --speakers ~/speakers.json
/transcript-to-social ~/talks/session1.txt --platform linkedin
/transcript-to-social https://youtube.com/watch?v=abc123 --platform twitter
```

### As a skill (auto-invoked)

Claude will automatically apply this skill when you say things like:
- "Convert this transcript to tweets"
- "Make a LinkedIn post from this talk"
- "Turn this session into a Twitter thread"
- "Here's a transcript, create social content"

## Speakers JSON format

To get verified @handles in your posts, provide a speakers file:

```json
[
  {
    "name": "Jane Smith",
    "twitter": "@janesmith",
    "bluesky": "@janesmith.bsky.social",
    "company": "Acme Corp",
    "company_twitter": "@acmecorp"
  }
]
```

If no speakers file is provided, the skill will ask you to confirm handles or omit them.

## Features

- **Full audio/video pipeline** — point at `.mp3`, `.mp4`, `.mov`, `.wav`, `.m4a` and get posts
- **AssemblyAI transcription** — speaker labels, auto chapters, key highlights
- Enforces character limits (280 for Twitter, 300 for Bluesky, 3000 for LinkedIn)
- Never fabricates @handles or statistics
- Supports YouTube chapter timestamps as deep links (e.g. `&t=1320s`)
- Handles AssemblyAI speaker labels (SPEAKER_00, SPEAKER_01, etc.)
- Works with raw .txt, .md, .json, and .vtt transcript files

## Plugin structure

```
transcript-to-social/
├── .claude-plugin/
│   └── plugin.json
├── commands/
│   └── transcript-to-social.md      # /transcript-to-social slash command
├── skills/
│   └── transcript-to-social/
│       ├── SKILL.md                  # Auto-invoked skill
│       └── references/
│           ├── assemblyai.md         # AssemblyAI transcription workflow + script
│           ├── platforms.md          # Platform char limits and formats
│           └── formatting-rules.md   # Content extraction and structure rules
└── README.md
```

## License

MIT
