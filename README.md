# bilibili-summarizer

**Agent Skill for summarizing Bilibili videos and saving structured notes.**

> Follows the [Agent Skills specification](https://agentskills.io/specification) — works with Claude Code, Codex CLI, and any skills-compatible agent.

[中文](README.zh.md)

## What it does

1. Opens the Bilibili video in Chrome via browser automation
2. Extracts title, uploader, description, and chapter markers
3. Downloads subtitles with yt-dlp (if available)
4. Summarizes the content and saves a Markdown note

## Requirements

- [yt-dlp](https://github.com/yt-dlp/yt-dlp) installed and on PATH
- Claude in Chrome browser extension (for page text extraction)

## Installation

### Claude Code

```
/plugin marketplace add zeshuochen/bilibili-summarizer
/plugin install bilibili@bilibili-summarizer
```

### Manual

Clone this repo and copy the `skills/` directory to your agent's skills path.

Then edit the `SAVE_PATH` in the skill file to point to your notes folder.

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
