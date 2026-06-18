---
name: markitdown
description: "Use when converting files (PDF, Word, Excel, PowerPoint, images, audio, HTML, EPUB, ZIP, YouTube URLs) to Markdown. Handles document-to-text extraction for LLM pipelines, indexing, and analysis."
version: 1.1.0
author: Hermes Agent
license: MIT
metadata:
  hermes:
    tags: [document-conversion, markdown, pdf, office, ocr, productivity]
    related_skills: [ocr-and-documents, nano-pdf]
---

# MarkItDown — 文件转 Markdown 工具

## Overview

[MarkItDown](https://github.com/microsoft/markitdown) 是微软开源的轻量级 Python 工具（v0.1.6），用于将各种文件格式转换为 Markdown。专为 LLM 管线和文本分析设计，保留文档结构（标题、列表、表格、链接等）。

**安装位置：** Hermes venv（`/root/.hermes/hermes-agent/.venv`），全局可用
**CLI 路径：** `markitdown`（venv 已在 PATH 中，直接调用）
**Python API：** `from markitdown import MarkItDown`（execute_code 中可直接 import）

> **安全提示：** MarkItDown 以当前进程权限执行 I/O，与 `open()` / `requests.get()` 行为一致。处理不受信任来源的文件时，应对输入进行清洗。处理 YouTube URL 需要外部网络访问。处理本地文件需要对目标文件所在目录有读取权限。

## 支持的文件格式

| 格式 | 扩展名 | 说明 |
|------|--------|------|
| PDF | `.pdf` | 文本提取 |
| Word | `.docx` | 文档结构保留 |
| PowerPoint | `.pptx` | 幻灯片内容提取 |
| Excel | `.xlsx`, `.xls` | 表格转 Markdown 表格 |
| 图片 | `.jpg/.png/.gif` 等 | EXIF 元数据 + OCR |
| 音频 | `.wav`, `.mp3` | EXIF 元数据 + 语音转录（需 `flac`） |
| HTML | `.html` | 网页转 Markdown |
| 文本格式 | `.csv`, `.json`, `.xml` | 原生支持 |
| EPUB | `.epub` | 电子书提取 |
| ZIP | `.zip` | 遍历内部文件 |
| YouTube | URL | 视频字幕转录（需网络） |

## 安装与配置

### Step 1: 安装 Python 包

MarkItDown 要求 Python ≥ 3.10，安装在当前活跃的 Python 环境中：

```bash
# 完整安装（含 PDF、Office、OCR、音频转录、YouTube 转录等全部可选依赖）
pip install 'markitdown[all]' -i https://pypi.org/simple/

# 或仅安装常用格式（PDF + Office 三件套）
pip install 'markitdown[pdf,docx,pptx,xlsx]' -i https://pypi.org/simple/
```

**可选依赖速查：**

| 安装选项 | 包含能力 |
|---------|---------|
| `[all]` | 全部（推荐） |
| `[pdf]` | PDF 文本提取 |
| `[docx]` | Word 文档 |
| `[pptx]` | PowerPoint |
| `[xlsx]` | Excel (.xlsx) |
| `[xls]` | 旧版 Excel (.xls) |
| `[audio-transcription]` | 音频语音转录 |
| `[youtube-transcription]` | YouTube 字幕提取 |
| `[outlook]` | Outlook 邮件 (.msg) |

### Step 2: 验证安装

```bash
markitdown --version
# 期望输出: markitdown 0.1.6

python -c "from markitdown import MarkItDown; print(MarkItDown())"
# 期望输出: <markitdown._markitdown.MarkItDown object at ...>
```

如果 `markitdown` 命令找不到，确认安装的 Python 环境已加入 PATH，或直接用完整路径：

```bash
/path/to/venv/bin/markitdown --version
```

### Step 3: 系统级依赖（可选）

`markitdown[all]` 通过 PyPI 安装后，以下格式开箱即用：PDF、Word、Excel、PPT、HTML、CSV、JSON、XML、EPUB、ZIP、图片（EXIF + OCR）。

以下格式需要额外的系统级库：

| 依赖 | 用途 | 安装方式 |
|------|------|----------|
| `flac` | 音频转录（wav/mp3 → 文字） | `apt install flac` |
| `ffmpeg` | 音视频处理增强 | `apt install ffmpeg` |

### Step 4: Azure AI 服务（可选，高级功能）

如需使用 Azure Document Intelligence 或 Content Understanding 进行高质量文档提取：

```bash
# 安装 Azure 依赖（已包含在 [all] 中）
pip install 'markitdown[az-doc-intel]' -i https://pypi.org/simple/
pip install 'markitdown[az-content-understanding]' -i https://pypi.org/simple/
```

使用时需设置环境变量或通过 CLI 参数传入 endpoint：

```bash
export AZURE_DOCUMENT_INTELLIGENCE_ENDPOINT="https://your-resource.cognitiveservices.azure.com"
export AZURE_DOCUMENT_INTELLIGENCE_KEY="your-key"

markitdown document.pdf -d -e "$AZURE_DOCUMENT_INTELLIGENCE_ENDPOINT"
```

### Step 5: 第三方插件（可选）

MarkItDown 支持社区插件增强功能（如 `markitdown-ocr` 用 LLM 做 OCR）：

```bash
# 查看已安装插件
markitdown --list-plugins

# 安装 OCR 插件（需要 LLM API key）
pip install markitdown-ocr -i https://pypi.org/simple/

# 使用插件转换
markitdown scanned-doc.pdf -p
```

## When to Use

- 需要将 PDF/Word/Excel/PPT 转为可读文本给 LLM 处理
- 提取文档中的结构化内容（表格、列表、标题）
- 从图片中 OCR 提取文字
- 从 YouTube URL 获取字幕
- 将 HTML 网页转为 Markdown
- 将 ZIP 中多个文件批量转 Markdown

**不要用于：** 高保真文档格式转换（保留精确排版）；此时应使用专门的格式转换工具。

## CLI 用法

### 基本转换（输出到 stdout）

```bash
markitdown path-to-file.pdf
```

### 输出到文件

```bash
markitdown path-to-file.pdf -o output.md
```

### 管道输入

```bash
cat path-to-file.pdf | markitdown
```

### 指定文件类型提示（stdin 场景）

```bash
cat data.bin | markitdown -x pdf
```

### 指定 MIME 类型和编码

```bash
markitdown data.bin -m application/pdf -c UTF-8
```

### 保留 data URI（base64 图片）

```bash
markitdown file.pdf --keep-data-uris
```

### 完整参数列表

| 参数 | 说明 |
|------|------|
| `filename` | 输入文件路径（可选，缺省读 stdin） |
| `-o, --output` | 输出文件名 |
| `-x, --extension` | 文件扩展名提示（stdin 场景） |
| `-m, --mime-type` | MIME 类型提示 |
| `-c, --charset` | 字符编码提示 |
| `-d, --use-docintel` | 使用 Azure Document Intelligence（需配置 endpoint） |
| `-e, --endpoint` | Document Intelligence Endpoint |
| `--use-cu` | 使用 Azure Content Understanding（需配置 endpoint） |
| `--cu-endpoint` | Content Understanding Endpoint |
| `--cu-analyzer` | Content Understanding analyzer ID |
| `-p, --use-plugins` | 启用第三方插件 |
| `--list-plugins` | 列出已安装插件 |
| `--keep-data-uris` | 保留 data URI（默认截断） |

## Python API

```python
from markitdown import MarkItDown

md = MarkItDown()

# 本地文件
result = md.convert("document.pdf")
print(result.text_content)

# URL（自动下载并转换）
result = md.convert_url("https://example.com/document.pdf")
print(result.text_content)

# 二进制流
with open("file.docx", "rb") as f:
    result = md.convert_stream(f)
    print(result.text_content)
```

所有 `convert*` 方法返回 `DocumentConverterResult`，核心属性：
- `.text_content` — 转换后的 Markdown 文本

### 在 execute_code 中使用

execute_code 运行在 Hermes venv 中，markitdown 已可直接 import：

```python
from markitdown import MarkItDown
md = MarkItDown()
result = md.convert("/path/to/file.pdf")
print(result.text_content)
```

## 常用 One-Shot Recipes

### 1. 快速查看 PDF 内容

```bash
markitdown document.pdf | head -100
```

### 2. Word 文档转 Markdown 文件

```bash
markitdown report.docx -o report.md
```

### 3. Excel 转 Markdown 表格

```bash
markitdown data.xlsx -o data.md
```

### 4. PPT 提取内容

```bash
markitdown presentation.pptx -o slides.md
```

### 5. YouTube 视频转字幕文本

```bash
markitdown "https://www.youtube.com/watch?v=VIDEO_ID"
```

### 6. HTML 网页转 Markdown

```bash
markitdown page.html -o page.md
```

### 7. ZIP 文件批量转换

```bash
markitdown archive.zip
```

### 8. 图片 OCR 提取文字

```bash
markitdown screenshot.png
```

### 9. 远程 URL 文件直接转换

```python
from markitdown import MarkItDown
md = MarkItDown()
result = md.convert_url("https://example.com/report.pdf")
print(result.text_content)
```

### 10. 管道处理 + head 限制大文件输出

```bash
markitdown large-report.pdf 2>/dev/null | head -200
```

## 大文件处理策略

大文件（如 100+ 页 PDF、大型 Excel）可能转换较慢或输出巨大。推荐策略：

1. **先测试前几行输出**：`markitdown file.pdf 2>/dev/null | head -50` 判断格式是否正确
2. **输出到文件再分段读取**：`markitdown file.pdf -o /tmp/out.md`，然后用 `read_file` 分段读取
3. **Excel 指定 sheet**：用 Python API 精细控制，或先转出再按 sheet 分段处理
4. **stderr 忽略**：转换过程中的进度/警告信息在 stderr，用 `2>/dev/null` 过滤可保持 stdout 纯净

## 错误排查

| 错误现象 | 可能原因 | 解决方案 |
|---------|---------|---------|
| `ModuleNotFoundError: No module named 'markitdown'` | 未安装或安装在了其他 Python 环境 | 确认 `which python` 和 `pip install` 在同一环境 |
| `markitdown: command not found` | CLI 不在 PATH 中 | 用 `python -m markitdown` 替代，或用完整路径 `/path/to/venv/bin/markitdown` |
| `pip install` 报 `externally-managed-environment` | 系统 Python 受 PEP 668 保护 | 使用 venv：`python -m venv .venv && source .venv/bin/activate && pip install ...` |
| PyPI 镜像源不可用（DNS 解析失败） | 国内镜像偶尔抽风 | 加 `-i https://pypi.org/simple/` 指定官方源 |
| `python3.11` 版本过低 | markitdown 要求 Python ≥ 3.10 | 升级 Python 到 3.10+ |
| 音频转写无输出/报错 | 缺少 `flac` 系统命令 | `apt install flac` |
| YouTube 转录失败 | 网络无法访问 YouTube | 需配置代理或使用其他方式获取字幕 |
| 图片 OCR 输出空 | 图片质量差或无文字 | 安装 `markitdown-ocr` 插件（需额外配置 LLM） |
| 中文乱码 | 编码不匹配 | 用 `-c UTF-8` 或 `-c GBK` 指定编码 |
| `Magika` 模型下载超时 | 首次运行需下载 ONNX 模型 (~30MB) | 重试或设置代理；模型缓存后会持久化 |
| PDF 提取为空 | 扫描件 PDF（纯图片，无文字层） | 用 OCR 插件或 `ocr-and-documents` skill |

## Common Pitfalls

1. **腾讯云镜像源不可用时**，安装需要指定 PyPI 官方源：
   ```bash
   python -m pip install 'markitdown[all]' -i https://pypi.org/simple/
   ```

2. **首次运行较慢**。`magika`（文件类型检测）首次使用会下载 ~30MB ONNX 模型到缓存，后续运行秒启动。

3. **`--keep-data-uris` 慎用**。保留 base64 编码的图片数据会使输出体积暴增（一张图几 MB），通常不需要。

4. **PDF 扫描件 vs 文字版**。markitdown 的 PDF 转换基于文本层提取，纯图片扫描件会输出空。需要 OCR 的场景请用 `markitdown-ocr` 插件或 `ocr-and-documents` skill。

5. **不要用 stdout 管道处理二进制内容**。markitdown 输出的是纯文本 Markdown，不要用于非文本格式的二次编码。

## 验证清单

- [ ] `markitdown --version` 输出 `0.1.6`
- [ ] `markitdown test.csv` 能正常输出 Markdown 表格
- [ ] Python `from markitdown import MarkItDown` 可正常导入
- [ ] `flac` 已安装（`which flac` 非空），用于音频转录
