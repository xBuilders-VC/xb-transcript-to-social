---
description: Convert an audio file, video file, transcript, or talk summary into Twitter/X threads, LinkedIn posts, and Bluesky threads. Supports AssemblyAI transcription for audio/video files.
argument-hint: [file-or-url] [--platform twitter|linkedin|bluesky] [--speakers speakers.json]
allowed-tools: Read, Glob, Bash(python3 *), Bash(yt-dlp *), Bash(pip install assemblyai), Bash(echo $ASSEMBLYAI_API_KEY)
---

Convert the following into social media content: $ARGUMENTS

## Instructions

### 1. Parse the input from `$ARGUMENTS`

Determine the input type by file extension or URL pattern:

**Audio/video file** (`.mp3`, `.mp4`, `.m4a`, `.wav`, `.mov`, `.avi`, `.mkv`, `.ogg`, `.flac`, `.webm`):
- Check AssemblyAI is ready: `python3 -c "import assemblyai; print('ok')"` — if missing, run `pip install assemblyai`
- Check API key: `echo $ASSEMBLYAI_API_KEY` — if empty, tell the user: "Set your key with: `export ASSEMBLYAI_API_KEY=your_key`"
- Warn the user about transcription cost (~$0.30-0.65/hour of audio) and ask to confirm
- Write and run the transcription script from `references/assemblyai.md` to produce a `_transcript.json` file
- Proceed with the JSON output

**Text/transcript file** (`.txt`, `.md`, `.vtt`, `.srt`, or `.json` with transcript fields):
- Use the Read tool to load the file

**YouTube URL** (`youtube.com` or `youtu.be`):
- Run `yt-dlp --write-auto-sub --skip-download --print-json "[URL]"` to get chapters and metadata

**No argument / inline text**:
- Ask: "Please paste the transcript text, or provide a file path or YouTube URL."

### 2. Check for a speakers file
If `--speakers path/to/file.json` is present, read that file for verified @handles and names. Never fabricate handles.

### 3. Check for platform flag
If `--platform twitter|linkedin|bluesky` is specified, generate only that platform. Otherwise generate all three.

### 4. Extract content and generate posts
Follow the transcript-to-social skill workflow:
- Use AssemblyAI chapter summaries when available (faster and cleaner than raw text)
- Extract: core thesis, 2-3 key insights, memorable quotes, stats/numbers
- Map speaker labels (SPEAKER_A, SPEAKER_00) to real names if context is available

### 5. Output posts with character counts
Label each section clearly. Show char count for every tweet/post.

### 6. After output
Ask: "Would you like to edit any posts, adjust the tone, or export to a file?"

## Examples

```
/transcript-to-social ~/talks/session1.mp4
/transcript-to-social ~/talks/keynote.mp3 --platform twitter
/transcript-to-social ~/talks/session.mp3 --speakers ~/speakers.json
/transcript-to-social ~/talks/session1.txt --platform linkedin
/transcript-to-social https://youtube.com/watch?v=abc123 --platform twitter
```
