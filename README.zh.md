# bilibili-summarizer

**自动总结哔哩哔哩视频并保存为结构化笔记的 Agent Skill。**

> 遵循 [Agent Skills 规范](https://agentskills.io/specification)，支持 Claude Code、Codex CLI 及所有兼容 skills 的 agent。

[English](README.md)

## 功能

1. 通过浏览器自动化打开 B 站视频
2. 提取标题、UP主、简介和章节标记
3. 用 yt-dlp 下载字幕（如有）
4. 总结内容并保存 Markdown 笔记

## 依赖

- [yt-dlp](https://github.com/yt-dlp/yt-dlp) 已安装并在 PATH 中
- Claude in Chrome 浏览器扩展（用于提取页面内容）

## 安装

### Claude Code

```
/plugin marketplace add zeshuochen/bilibili-summarizer
/plugin install bilibili@bilibili-summarizer
```

### 手动安装

克隆此仓库，将 `skills/` 目录复制到你的 agent 的 skills 路径下。

然后编辑 skill 文件中的 `SAVE_PATH`，指向你的笔记保存文件夹。

## 使用

分享 B 站链接，让 agent 总结视频：

```
https://www.bilibili.com/video/BV...   summarize
```

```
总结这个视频：https://www.bilibili.com/video/BV...
```

agent 会总结视频内容并将笔记保存到配置的文件夹。

## 笔记格式

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

## License

MIT © [zeshuochen](https://github.com/zeshuochen)
