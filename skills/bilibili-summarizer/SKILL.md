---
name: bilibili-summarizer
description: Use when the user shares a Bilibili video link and asks to summarize or save notes about the video.
allowed-tools: Bash, Write, mcp__claude-in-chrome__tabs_context_mcp, mcp__claude-in-chrome__tabs_create_mcp, mcp__claude-in-chrome__navigate, mcp__claude-in-chrome__get_page_text, mcp__claude-in-chrome__read_page
---

# Bilibili Video Summarizer

Fetch a Bilibili video's content via subtitles, Bilibili's AI summary API, or audio transcription — then save a structured note.

## Configuration

Edit before first use:

```
SAVE_PATH=C:\Users\you\Notes          # absolute path to the folder where notes will be saved
COOKIES_PATH=C:\Users\you\bili_cookies.txt  # path to your Bilibili cookies file (Netscape format)
```

## Workflow

### Step 1 — Get metadata

```bash
yt-dlp --dump-json --no-download "<url>"
```

Extract: title, uploader, duration, upload date, description, chapters, BV ID.

CID (needed for Step 2B) can be fetched without authentication:

```bash
python -c "
import urllib.request, json
bvid = '<BV_ID>'
r = urllib.request.urlopen('https://api.bilibili.com/x/web-interface/view?bvid=' + bvid)
d = json.loads(r.read())
print('cid:', d['data']['cid'], 'mid:', d['data']['owner']['mid'])
"
```

---

### Step 2 — Fetch content (try in order)

#### Option A: yt-dlp + cookies file — subtitles (best)

Set `COOKIES_PATH` to wherever you save your Bilibili cookies file (see Configuration above).

Export cookies from your browser using an extension such as **Get cookies.txt LOCALLY** while on any Bilibili page, and save the file to `COOKIES_PATH`.

```bash
yt-dlp --cookies "<COOKIES_PATH>" \
  --skip-download --write-subs --write-auto-subs \
  --sub-lang "zh-Hans,zh-CN,zh,ai-zh,ai-zh-Hans,en" \
  --convert-subs srt \
  -o "/tmp/bili_%(id)s" \
  "<url>"
```

If a `.srt` file is created (e.g. `/tmp/bili_<id>.zh-Hans.srt`), read it and proceed to **Step 3**.

If the cookies file is missing or expired, prompt the user to re-export it.

---

#### Option B: Bilibili AI summary API (if Option A yields no subtitles)

Bilibili provides AI-generated summaries for many videos via an API:

```bash
python -c "
import subprocess, json, urllib.request, http.cookiejar

# Export browser cookies
subprocess.run([
    'yt-dlp', '--cookies-from-browser', 'chrome',
    '--cookies', '/tmp/bili_cookies.txt',
    '--skip-download', '--quiet', '<url>'
], capture_output=True)

# Load cookies
cj = http.cookiejar.MozillaCookieJar('/tmp/bili_cookies.txt')
try:
    cj.load(ignore_discard=True, ignore_expires=True)
except:
    pass
cookie_str = '; '.join(f'{c.name}={c.value}' for c in cj if 'bilibili' in c.domain)

# Call AI summary endpoint
bvid = '<BV_ID>'
cid  = '<CID>'
req = urllib.request.Request(
    f'https://api.bilibili.com/x/web-interface/view/conclusion/get?bvid={bvid}&cid={cid}',
    headers={'Cookie': cookie_str, 'Referer': 'https://www.bilibili.com/', 'User-Agent': 'Mozilla/5.0'}
)
resp = json.loads(urllib.request.urlopen(req).read())
print(json.dumps(resp, ensure_ascii=False, indent=2))
"
```

If `data.model_result` is non-empty, extract `summary` and `outline`, then proceed to **Step 3**.

---

#### Option C: Download audio + Whisper transcription (if A and B both fail)

Inform the user that transcription takes 10–20 minutes, then run in background using a local pipeline (e.g. yt-dlp + Whisper). Proceed to **Step 3** once the transcript is ready.

---

#### Option D: Description + chapters only (last resort)

Use only when all above options fail. Always add this notice to the note:

```
> ⚠️ This summary is based on the video description and chapter titles only — no transcript was available.
```

---

### Step 3 — Summarize

From the content obtained (subtitles / AI summary / transcript):
- 3–5 sentence overview of the main topic
- 5–10 key points as a bullet list
- Detailed notes organised by chapter or theme

---

### Step 4 — Write the note

Save to `<SAVE_PATH>/<video title>.md`:

```markdown
# [Video Title]

> **Uploader**: [name] | **Duration**: [hh:mm:ss] | **Published**: [date]

## Summary

[3–5 sentences]

## Key Points

- [point 1]
- [point 2]

## Detailed Notes

[Expanded notes by chapter or theme — should be substantive when a transcript is available]

## Source

- [Video Title](<Bilibili URL>)
```

---

## Notes

- Strip illegal filename characters: `\ / : * ? " < > |`
- If chapters exist, use them as section headings in Detailed Notes
- Option A covers most users already logged into Bilibili in Chrome
- Option C is slow — always run in background and inform the user
