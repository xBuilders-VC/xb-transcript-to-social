# AssemblyAI Transcription

## When to use AssemblyAI
Invoke AssemblyAI when the input file is an audio or video file:
- Audio: `.mp3`, `.mp4`, `.m4a`, `.wav`, `.ogg`, `.flac`, `.aac`, `.opus`, `.webm`
- Video: `.mp4`, `.mov`, `.avi`, `.mkv`, `.webm` (audio track is extracted automatically)

Do NOT use AssemblyAI for text files (.txt, .md, .json, .vtt) — read those directly.

## Setup check
Before transcribing, verify AssemblyAI is available:
```bash
python3 -c "import assemblyai; print('ok')"
```
If missing: `pip install assemblyai`

Check for API key:
```bash
echo $ASSEMBLYAI_API_KEY
```
If empty, ask the user: "Please set your AssemblyAI API key: `export ASSEMBLYAI_API_KEY=your_key_here`"

## Transcription script

Run this Python script to transcribe a file and save the result:

```python
import assemblyai as aai
import json
import sys
import os

api_key = os.environ.get("ASSEMBLYAI_API_KEY")
if not api_key:
    print("ERROR: ASSEMBLYAI_API_KEY not set")
    sys.exit(1)

aai.settings.api_key = api_key
input_file = sys.argv[1]
output_file = sys.argv[2] if len(sys.argv) > 2 else input_file.rsplit(".", 1)[0] + "_transcript.json"

config = aai.TranscriptionConfig(
    speaker_labels=True,          # identify distinct speakers
    auto_chapters=True,           # chapter/topic segmentation
    auto_highlights=True,         # key phrases
    sentiment_analysis=False,     # not needed for social posts
)

print(f"Transcribing: {input_file}")
transcriber = aai.Transcriber()
transcript = transcriber.transcribe(input_file, config=config)

if transcript.status == aai.TranscriptStatus.error:
    print(f"ERROR: {transcript.error}")
    sys.exit(1)

# Save full JSON result
result = {
    "text": transcript.text,
    "utterances": [
        {
            "speaker": u.speaker,
            "start_ms": u.start,
            "end_ms": u.end,
            "text": u.text,
        }
        for u in (transcript.utterances or [])
    ],
    "chapters": [
        {
            "summary": c.summary,
            "headline": c.headline,
            "start_ms": c.start,
            "end_ms": c.end,
        }
        for c in (transcript.chapters or [])
    ],
    "highlights": [h.text for h in (transcript.auto_highlights.results if transcript.auto_highlights else [])],
    "duration_ms": transcript.audio_duration,
}

with open(output_file, "w") as f:
    json.dump(result, f, indent=2)

print(f"Saved to: {output_file}")
print(f"Duration: {transcript.audio_duration}s")
print(f"Words: {len(transcript.text.split())}")
```

Save this as a temp script and run:
```bash
python3 /tmp/transcribe.py "/path/to/audio.mp3" "/path/to/output_transcript.json"
```

## Using the transcript output

The JSON output contains:
- `text` — full transcript text (for content extraction)
- `utterances` — speaker-labeled segments with timestamps
- `chapters` — auto-detected topic sections with summaries and headlines
- `highlights` — key phrases AssemblyAI identified
- `duration_ms` — total audio length

**Use `chapters` first** — they provide ready-made summaries and timestamps for YouTube-style deep links.

**Use `utterances`** to attribute quotes to specific speakers (SPEAKER_A, SPEAKER_B, etc.).

## Timestamp conversion
To convert milliseconds to seconds for YouTube deep links:
```python
seconds = milliseconds // 1000
# e.g. 132000ms → 132s → use &t=132s in YouTube URL
```

## Cost note
AssemblyAI charges per audio hour. Typical conference talk (30-60 min) = $0.30-0.65 at standard tier.
Inform the user of this before transcribing long files if they haven't mentioned it.
