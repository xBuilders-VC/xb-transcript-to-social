# Formatting Rules

## Input Types
The skill handles these transcript sources:
- **Raw text**: paste directly or from a .txt file
- **File path**: local .txt, .md, .json, or .vtt file
- **AssemblyAI JSON**: structured transcript with speaker labels and timestamps
- **YouTube URL**: extract chapters + auto-captions via yt-dlp (if available)
- **Summary file**: pre-summarized session notes

## Speaker Handling
1. **Extract speakers** from transcript labels (Speaker A, SPEAKER_00, etc.) or from a provided speakers.json
2. **Map to real names** if context or a roster is provided
3. **Verified handles only**: use @handles from roster/context — never fabricate social handles
4. **Company handles**: include verified company @handles when speaker affiliation is clear
5. **Format**: "@name from @company discussed..." or "Thread on @name's talk:"

## Content Extraction
For each transcript/session, identify:
- **Core thesis**: the single most important idea (goes in tweet 1 / LinkedIn hook)
- **2-3 key insights**: specific, quotable, shareable points
- **Memorable quotes**: direct quotes add authenticity (use quotation marks)
- **Numbers/stats**: specific figures make content more shareable
- **Action items or takeaways**: what should the audience do/know?

## Tweet Thread Structure
```
Tweet 1 (announcement):
[Action verb] + [topic] at [event] by [@speaker] from [@company]
[YouTube link with timestamp if available]
👇

Tweet 2:
[Key insight 1 — specific, opinionated, concrete]
[Hashtag if fits naturally]

Tweet 3:
[Key insight 2 or direct quote]
[Closing hashtag(s)]
```

## LinkedIn Post Structure
```
[Strong hook — the most surprising or valuable insight]

[2-3 sentence context: who spoke, what event, why it matters]

[Key insight 1]

[Key insight 2]

[Key insight 3 or call to action]

[3-5 hashtags]
```

## Quality Checks
- Every tweet must be ≤ 280 chars (count URLs as 23 chars)
- No fabricated handles, stats, or quotes
- Avoid generic phrases: "great talk", "fascinating insights", "deep dive"
- Prefer active voice and concrete specifics
- If unsure about a speaker's handle, omit it rather than guess
