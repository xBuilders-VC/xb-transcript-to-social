---
name: transcript-to-social
description: Use this skill when the user wants to convert audio files, video files, transcripts, session notes, YouTube videos, or talk summaries into social media posts. Activates when the user mentions "transcript", "talk", "session", "conference", "speaker", "convert to tweets", "make a thread", "LinkedIn post from", "tweet this", points at an .mp3/.mp4/.wav/.mov/.m4a file, or asks to create social content from audio/video content.
version: 1.1.0
---

# Transcript → Social Media

This skill converts audio files, video files, transcripts, and talk summaries into platform-ready social media content. It handles the full pipeline: **audio/video → AssemblyAI transcription → social posts**.

## Supported Platforms
- **Twitter/X** — tweet threads (≤280 chars each)
- **LinkedIn** — long-form post with hook + insights + hashtags
- **Bluesky** — thread format (≤300 chars each)

## Supported Input Formats

| Type | Extensions | How it's handled |
|------|-----------|-----------------|
| Audio file | `.mp3` `.m4a` `.wav` `.ogg` `.flac` `.aac` `.opus` | AssemblyAI transcription |
| Video file | `.mp4` `.mov` `.avi` `.mkv` `.webm` | AssemblyAI (audio track extracted) |
| AssemblyAI JSON | `.json` with `utterances`/`chapters` fields | Read directly |
| Subtitle/caption | `.vtt` `.srt` | Read directly |
| Plain text | `.txt` `.md` | Read directly |
| YouTube URL | `youtube.com` or `youtu.be` | yt-dlp for chapters + captions |
| Inline paste | Raw text in conversation | Use directly |

## Your Workflow

### Step 1 — Detect input type

Check the file extension or URL:
- **Audio/video file** → go to Step 1a (transcribe with AssemblyAI)
- **Text/JSON/VTT file** → go to Step 1b (read directly)
- **YouTube URL** → go to Step 1c (yt-dlp)
- **No input / inline text** → go to Step 1d (use as-is)

#### Step 1a — Transcribe audio/video with AssemblyAI
Follow the full transcription workflow in [references/assemblyai.md](references/assemblyai.md):
1. Check AssemblyAI is installed and `ASSEMBLYAI_API_KEY` is set
2. Run the transcription script — saves a `_transcript.json` alongside the source file
3. Use the resulting JSON (chapters for structure, utterances for speaker attribution)

#### Step 1b — Read text/transcript files
Use the Read tool to load the file. For AssemblyAI JSON, use `chapters` and `utterances` fields.

#### Step 1c — YouTube URL
Run `yt-dlp --write-auto-sub --skip-download --print-json "[URL]"` to get chapters and metadata.

#### Step 1d — Inline text
Proceed directly to content extraction.

### Step 2 — Understand the content
- Identify speaker(s), event/conference name, topic
- Check if a speakers roster or handles file was provided (`--speakers`)
- Note any timestamps or chapter markers for YouTube deep links

### Step 3 — Extract content
1. Identify: core thesis, 2-3 key insights, memorable quotes, any stats/numbers
2. Map speaker labels (SPEAKER_A, SPEAKER_00) to real names/handles if context available
3. Use chapter summaries from AssemblyAI when available — they save extraction time

### Step 4 — Generate posts

**Default: produce all three formats unless the user specifies a platform.**

Follow the platform specs and formatting rules:
- [Platform specifications](references/platforms.md)
- [Formatting rules and templates](references/formatting-rules.md)

### Step 5 — Validate
- Count every tweet — flag any over 280 chars (URLs = 23 chars)
- Confirm all @handles came from provided context (never fabricate)
- Remove vague filler phrases
- Re-read tweet 1 — it must stand alone as the announcement

## Output Format

```
## Twitter/X Thread

**Tweet 1** (N chars)
[text]

**Tweet 2** (N chars)
[text]

**Tweet 3** (N chars)
[text]

---

## LinkedIn Post

[full post text]

---

## Bluesky Thread

**Post 1** (N chars)
[text]

...
```

## Tips
- If the transcript is very long (>10,000 words), use AssemblyAI chapter summaries instead of the full text
- For multi-session events, process one audio/video file at a time
- When timestamps are available, link tweet 1 to `[youtube-url]&t=[seconds]s`
- Inform the user of AssemblyAI cost (~$0.30-0.65 per hour of audio) before transcribing
- Speaker's name in tweet 1 should include @handle if verified; omit if uncertain
- Ask the user to confirm speaker handles before final output if not provided
