# sino-scrub
[![Gitee](https://img.shields.io/badge/Gitee-mirror-c71d23?logo=gitee)](https://gitee.com/perrylink/sino-scrub)

> Millisecond-level cleaning of cultural taboos and sensitive words in AI-generated text

[![Python Version](https://img.shields.io/badge/python-3.8+-blue.svg)](https://www.python.org/downloads/)
[![License](https://img.shields.io/badge/license-Apache%202.0-green.svg)](LICENSE)
[![Code style: black](https://img.shields.io/badge/code%20style-black-000000.svg)](https://github.com/psf/black)

---

> 毫秒级清洗 AI 文本中的文化禁忌与敏感词

[![Python 版本](https://img.shields.io/badge/python-3.8+-blue.svg)](https://www.python.org/downloads/)
[![许可证](https://img.shields.io/badge/license-Apache%202.0-green.svg)](LICENSE)
[![代码风格: black](https://img.shields.io/badge/code%20style-black-000000.svg)](https://github.com/psf/black)

## Features / 核心特性

**English:**
- ⚡ **Lightning Fast**: O(N) complexity based on FlashText algorithm
- 🎯 **Precise Replacement**: Supports three categories - translation fixes, cultural taboos, and political sensitivities
- 🔍 **Debug Mode**: Not only replaces, but explains **why**
- 🎨 **Beautiful Terminal**: Rich library powered elegant diff display
- 🔧 **Highly Customizable**: Supports custom dictionaries and category filtering

**中文:**
- ⚡ **极速处理**: 基于 FlashText 算法，O(N) 复杂度
- 🎯 **精准替换**: 支持翻译修正、文化禁忌、政治敏感词三大类别
- 🔍 **Debug Mode**: 不仅替换，还解释**为什么**
- 🎨 **终端美化**: Rich 库支持的精美对比显示
- 🔧 **高度可定制**: 支持自定义词库和类别过滤

## The Problem / 核心痛点

**English:**

Even the most powerful LLMs can generate problematic translations like "execute program" being translated as "处死程序" (execute/kill program), or use inauspicious homophones in Chinese New Year copy. Manual proofreading is too slow, but direct publishing risks PR crises.

**sino-scrub** provides millisecond-level processing of large-scale text with O(N) complexity based on the FlashText algorithm.

**中文:**

即使是最强的 LLM，也会生成像"execute program"被翻译成"处死程序"，或者在春节文案里用到不吉利的谐音字。人工校对太慢，直接发布又怕公关危机。

**sino-scrub** 基于 FlashText 算法，提供 O(N) 复杂度的关键词替换，毫秒级处理大规模文本。

## Quick Start / 快速开始

### Installation / 安装

```bash
pip install "git+https://github.com/PerryLink/Sino-Scrub.git"
# （PyPI 未发布，源码直装）
```

Or using Poetry / 或使用 Poetry:

```bash
poetry add git+https://github.com/PerryLink/Sino-Scrub.git
# （PyPI 未发布，源码直装）
```

### Basic Usage / 基础用法

**Command Line / 命令行:**

```bash
# Basic usage / 基础用法
scrub "系统正在处死进程"
# Output / 输出: 系统正在终止进程

# Show diff / 显示对比差异
scrub "祝您全家升天" --diff

# Debug Mode (show replacement reasons) / Debug 模式（显示替换原因）
scrub "送您一顶绿帽子" --debug

# Process file / 处理文件
scrub input.txt -o output.txt

# Use custom dictionary / 使用自定义词库
scrub text.txt -d custom.json

# Load specific categories only / 只加载特定类别
scrub text.txt -c translation -c cultural
```

**Python API:**

```python
from sino_scrub import TextScrubber

# Initialize scrubber / 初始化清洗器
scrubber = TextScrubber()

# Scrub text / 清洗文本
text = "系统正在处死进程"
result, _ = scrubber.scrub(text)
print(result)  # Output / 输出: 系统正在终止进程

# Track changes (Debug Mode) / 追踪变更（Debug 模式）
result, changes = scrubber.scrub(text, track_changes=True)
for change in changes:
    print(f"{change['keyword']} → {change['replacement']}")
    print(f"Reason / 原因: {change['reason']}")
```

## Dictionary Categories / 词库类别

### 1. translation (Translation Fixes / 翻译修正)
Fixes common errors in technical translations / 修正技术翻译中的常见错误:
- "处死" → "终止" (execute → terminate)
- "杀死进程" → "终止进程" (kill process → terminate process)
- "致命错误" → "严重错误" (fatal error → critical error)

### 2. cultural (Cultural Taboos / 文化禁忌)
Avoids culturally taboo words / 避免文化禁忌词汇:
- "升天" → "幸福" (ascend to heaven → happiness, euphemism for death)
- "绿帽子" → "精美帽子" (green hat → nice hat, implies infidelity)
- "送钟" → "送礼物" (give clock → give gift, sounds like "attend funeral")

### 3. political (Political Sensitivities / 政治敏感)
Handles politically sensitive words (use with caution) / 处理政治敏感词汇（谨慎使用）

## Custom Dictionary / 自定义词库

Create a JSON format dictionary file / 创建 JSON 格式的词库文件:

```json
{
  "敏感词": {
    "replacement": "替换词",
    "reason": "替换原因说明",
    "category": "custom"
  }
}
```

Simplified format is also supported / 简化格式也支持:

```json
{
  "敏感词": "替换词"
}
```

## Debug Mode Example / Debug Mode 示例

```bash
$ scrub "祝您全家升天,送您一顶绿帽子作为礼物" --debug
```

Output / 输出:

```
┏━━━━━━━━━━━━━━━━━━━━━━━━━━┳━━━━━━━━━━━━━━━━━━━━━━━━━━┓
┃ 原文                     ┃ 清洗后                   ┃
┡━━━━━━━━━━━━━━━━━━━━━━━━━━╇━━━━━━━━━━━━━━━━━━━━━━━━━━┩
│ 祝您全家升天,送您一顶绿  │ 祝您全家幸福,送您一顶精  │
│ 帽子作为礼物             │ 美帽子作为礼物           │
└──────────────────────────┴──────────────────────────┘

Debug Information:

  1. '升天' → '幸福'
     Category: [cultural]
     Reason: 文化禁忌：'升天'字面意思是'升入天堂'（死亡的委婉说法），
             在祝福语中不合适。

  2. '绿帽子' → '精美帽子'
     Category: [cultural]
     Reason: 文化禁忌：'绿帽子'在中文文化中暗示配偶不忠，
             在礼物语境中应避免。
```

## Project Structure / 项目结构

```
sino-scrub/
├── src/sino_scrub/
│   ├── __init__.py              # Public API exports / 公共 API 导出
│   ├── __main__.py              # CLI entry point / CLI 入口点
│   ├── cli.py                   # Typer CLI commands / Typer CLI 命令
│   ├── processor.py             # FlashText core engine / FlashText 核心引擎
│   ├── visualizer.py            # Rich terminal visualization / Rich 终端可视化
│   ├── utils.py                 # Utility functions / 工具函数
│   └── data/
│       ├── translation_fix.json      # Translation fixes / 翻译修正词库
│       ├── cultural_taboo.json       # Cultural taboos / 文化禁忌词库
│       └── political_sensitive.json  # Political sensitivities / 政治敏感词库
├── tests/
│   ├── test_processor.py        # Core logic tests / 核心逻辑测试
│   └── test_cli.py              # CLI integration tests / CLI 集成测试
├── pyproject.toml               # Poetry configuration / Poetry 配置
├── README.md                    # Project documentation / 项目文档
└── .gitignore                   # Git ignore configuration / Git 忽略配置
```

## Tech Stack / 技术栈

- **[FlashText](https://github.com/vi3k6i5/flashtext)**: Efficient keyword replacement algorithm / 高效的关键词替换算法
- **[Rich](https://github.com/Textualize/rich)**: Beautiful terminal output / 精美的终端输出
- **[Typer](https://github.com/tiangolo/typer)**: Modern CLI framework / 现代化的 CLI 框架
- **[Poetry](https://python-poetry.org/)**: Dependency management / 依赖管理

## Performance / 性能

- Load 1000 keywords < 100ms / 加载 1000 个关键词 < 100ms
- Process 10KB text < 10ms / 处理 10KB 文本 < 10ms
- Process 100KB text < 100ms / 处理 100KB 文本 < 100ms

## Target Users / 目标用户

**English:**
- AIGC application developers
- Content operations for overseas-to-domestic market
- Enterprise compliance departments
- Automated workflows requiring text review

**中文:**
- AIGC 应用开发者
- 出海转内销的内容运营
- 企业合规部门
- 需要文本审核的自动化流程

## Development / 开发

```bash
# Clone repository / 克隆仓库
git clone https://github.com/PerryLink/sino-scrub.git
cd sino-scrub

# Install dependencies / 安装依赖
poetry install

# Run tests / 运行测试
poetry run pytest

# Run CLI / 运行 CLI
poetry run scrub "测试文本"
```

## License / 许可证

Apache License 2.0 - see [LICENSE](LICENSE) file for details

Copyright 2026 Chance Dean (novelnexusai@outlook.com)

## Contributing / 贡献

Contributions are welcome! Please see [CONTRIBUTING.md](CONTRIBUTING.md) for details.

欢迎贡献！详情请参阅 [CONTRIBUTING.md](CONTRIBUTING.md)。

## Acknowledgments / 致谢

- [FlashText](https://github.com/vi3k6i5/flashtext) - Efficient keyword replacement algorithm / 高效的关键词替换算法
- [Rich](https://github.com/Textualize/rich) - Beautiful terminal output / 精美的终端输出
- [Typer](https://github.com/tiangolo/typer) - Modern CLI framework / 现代化的 CLI 框架
