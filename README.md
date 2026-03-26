# bilibili-summarizer

**Agent Skill for summarizing Bilibili videos and saving structured notes.**

> Follows the [Agent Skills specification](https://agentskills.io/specification) — works with Claude Code, Codex CLI, and any skills-compatible agent.

[中文](README.zh.md)

## What it does

1. Fetches video metadata (title, uploader, duration, chapters) via yt-dlp
2. Downloads subtitles using a Bilibili cookies file — falls back to the AI summary API, then audio transcription if unavailable
3. Summarizes the content and saves a structured Markdown note

## Requirements

- [yt-dlp](https://github.com/yt-dlp/yt-dlp) installed and on PATH
- A Bilibili cookies file (see Installation)

## Installation

### Claude Code

```
/plugin marketplace add zeshuochen/bilibili-summarizer
/plugin install bilibili@bilibili-summarizer
```

### Manual

Clone this repo and copy the `skills/` directory to your agent's skills path.

Then edit the two config values in the skill file:

```
SAVE_PATH=     # absolute path to the folder where notes will be saved
COOKIES_PATH=  # absolute path to your Bilibili cookies file (Netscape format)
```

To get your cookies file: install the **[Get cookies.txt LOCALLY](https://chromewebstore.google.com/detail/get-cookiestxt-locally/cclelndahbckbenkjhflpdbgdldlbecc)** browser extension, open any Bilibili page while logged in, and export — save the file to your `COOKIES_PATH`.

## Usage

After installation, the skill is available as a slash command:

```
/bilibili-summarizer
```

Or share a link directly:

```
https://www.bilibili.com/video/BV...   summarize
```

```
总结这个视频：https://www.bilibili.com/video/BV...
```

The agent will summarize the video and save a note to your configured folder.

## Note format

```markdown
# [视频标题]

> **UP主**: ... | **时长**: ... | **发布时间**: ...

## 核心内容
...

## 主要观点
- ...

## 详细笔记
...

## 来源
- [标题](url)
```
