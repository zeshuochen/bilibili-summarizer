---
name: bilibili-summarizer
description: Use when the user shares a Bilibili video link and asks to summarize, take notes on, or save notes about the video.
allowed-tools: Bash, Write, mcp__claude-in-chrome__tabs_context_mcp, mcp__claude-in-chrome__tabs_create_mcp, mcp__claude-in-chrome__navigate, mcp__claude-in-chrome__get_page_text, mcp__claude-in-chrome__read_page
---

# Bilibili Video Summarizer

Fetch a Bilibili video, extract its transcript (if available), summarize the content, and save a structured note.

## Configuration

Edit this value before first use:

```
SAVE_PATH=C:\Users\you\Notes     # absolute path to the folder where notes will be saved
```

## Workflow

### Step 1 — Get video metadata (browser)

Use `mcp__claude-in-chrome__tabs_context_mcp` to check current tabs, create a new tab (`mcp__claude-in-chrome__tabs_create_mcp`), navigate to the URL (`mcp__claude-in-chrome__navigate`), then call `mcp__claude-in-chrome__get_page_text`.

Extract from the page:
- Video title (use as filename)
- UP主 (uploader name)
- Publish date
- Description / intro text
- Chapter markers (if any)

### Step 2 — Download subtitles (yt-dlp)

Run the following and capture stdout/stderr to check what subtitle tracks are available:

```bash
yt-dlp --skip-download --write-subs --write-auto-subs \
  --sub-lang "zh-Hans,zh-CN,zh,ai-zh,ai-zh-Hans,en" \
  --convert-subs srt \
  -o "C:/tmp/bili_%(id)s" \
  "<url>"
```

Then read the `.srt` file if created (e.g. `C:/tmp/bili_<id>.zh-Hans.srt`).

**If no subtitles are found**, summarize from the description + chapter headings gathered in Step 1.
Note in the saved file that the summary is based on metadata only.

### Step 3 — Summarize

Produce a concise summary in the **same language as the video** (Chinese for most Bilibili content):

- 3–5 sentence overview of the main topic
- Key points as a bullet list (5–10 items)
- Any notable quotes, data, or conclusions worth preserving

### Step 4 — Write the note

Use the `Write` tool to save `<SAVE_PATH>/<video title>.md`:

```markdown
# [视频标题]

> **UP主**：[UP主] | **时长**：[时长] | **发布时间**：[日期]

## 核心内容

[3-5句话总结视频主旨]

## 主要观点

- [观点 1]
- [观点 2]
- [观点 3]

## 详细笔记

[从字幕或描述中整理的详细要点，按主题分段]

## 来源

- [视频标题](<Bilibili链接>)
```

## Notes

- Always use the video title (sanitized for filesystem) as the filename
- If chapters exist, use them as section headings in the detailed notes
- If only metadata is available (no subtitles), add `> ⚠️ 本摘要基于视频描述，未获取字幕` after the header block
- Strip illegal filename characters: `\ / : * ? " < > |`
