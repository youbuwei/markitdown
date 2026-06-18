# markitdown-skill

[![SkillHub](https://img.shields.io/badge/SkillHub-markitdown-blue)](https://skillhub.cn/skills/markitdown)
[![Version](https://img.shields.io/badge/version-1.1.0-green)]()

Hermes Agent Skill for [MarkItDown](https://github.com/microsoft/markitdown) — Microsoft 的轻量级 Python 工具，用于将各种文件格式（PDF、Word、Excel、PowerPoint、图片、音频、HTML、EPUB、ZIP、YouTube URL）转换为 Markdown。

## 安装

```bash
# 在 Hermes venv 中安装 markitdown（含全部格式支持）
pip install 'markitdown[all]' -i https://pypi.org/simple/
```

## 使用方式

1. 将 `SKILL.md` 放置到 Hermes skills 目录：`~/.hermes/skills/productivity/markitdown/SKILL.md`
2. Agent 会自动加载并识别该 skill
3. 当需要文件转 Markdown 时，Agent 会自动调用

## 功能

- **文档转换**：PDF、Word (.docx)、PowerPoint (.pptx)、Excel (.xlsx/.xls) → Markdown
- **图片 OCR**：从图片中提取文字
- **音频转录**：wav/mp3 语音转文字（需 `flac`）
- **YouTube 字幕**：从 YouTube URL 提取字幕文本
- **网页转换**：HTML → Markdown
- **批量处理**：ZIP 文件遍历内部文件转换
- **电子书**：EPUB → Markdown

## 技巧

安装音频转录支持：

```bash
apt install flac
```

## 许可证

MIT License
