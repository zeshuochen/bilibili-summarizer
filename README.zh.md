# bilibili-summarizer

**自动总结哔哩哔哩视频并保存为结构化笔记的 Agent Skill。**

> 遵循 [Agent Skills 规范](https://agentskills.io/specification)，支持 Claude Code、Codex CLI 及所有兼容 skills 的 agent。

[English](README.md)

## 功能

1. 通过 yt-dlp 获取视频元数据（标题、UP主、时长、章节）
2. 用 B站 cookies 文件下载字幕——若无字幕，依次回退到 AI 总结 API、音频转录
3. 总结内容并保存结构化 Markdown 笔记

## 依赖

- [yt-dlp](https://github.com/yt-dlp/yt-dlp) 已安装并在 PATH 中
- B站 cookies 文件（见安装步骤）

## 安装

### Claude Code

```
/plugin marketplace add zeshuochen/bilibili-summarizer
/plugin install bilibili@bilibili-summarizer
```

### 手动安装

克隆此仓库，将 `skills/` 目录复制到你的 agent 的 skills 路径下。

然后编辑 skill 文件中的两个配置项：

```
SAVE_PATH=     # 笔记保存文件夹的绝对路径
COOKIES_PATH=  # B站 cookies 文件的绝对路径（Netscape 格式）
```

获取 cookies 文件：安装 **[Get cookies.txt LOCALLY](https://chromewebstore.google.com/detail/get-cookiestxt-locally/cclelndahbckbenkjhflpdbgdldlbecc)** 浏览器扩展，在登录状态下打开任意 B站页面导出，保存到你的 `COOKIES_PATH`。

## 使用

安装后，skill 可作为 slash command 直接调用：

```
/bilibili-summarizer
```

或直接分享链接：

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
