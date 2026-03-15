---
name: repo-tokens-count
description: 为 GitHub 项目添加 token count badge，显示代码库规模及其占 LLM 上下文窗口的比例。当用户需要展示代码规模、添加 token 统计 badge 时触发。
---

# Repo Tokens Count Skill

为 GitHub 项目添加自动化的 token 统计 badge，显示代码库规模及其相对于 LLM 上下文窗口的占比。

## 功能

- 自动计算代码库的 token 数量
- 生成彩色 SVG badge（绿色/黄绿/黄色/红色根据占比自动调整）
- 支持自定义统计的文件范围和排除规则
- 支持自定义 LLM 上下文窗口大小
- 通过 GitHub Actions 自动更新

## 使用方式

用户使用示例：

```
为当前项目添加 token count badge
```

或

```
给 https://github.com/user/repo 添加 token badge，统计 src 目录下的 ts 文件
```

## 工作流程

### 1. 分析项目结构

- 检查项目类型和主要语言
- 识别源代码目录结构
- 确认 README.md 位置

### 2. 创建 GitHub Actions Workflow

在 `.github/workflows/tokens.yml` 创建 workflow：

```yaml
name: Token Count Badge

on:
  push:
    branches: [main]
  workflow_dispatch:

permissions:
  contents: write

jobs:
  update-badge:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: qwibitai/nanoclaw/repo-tokens@main
        with:
          include: '<glob-pattern>'      # 例如: 'src/**/*.ts'
          exclude: '<exclude-pattern>'   # 可选，例如: 'src/**/*.test.ts'
          context-window: 200000         # LLM 上下文窗口大小
          badge-path: 'badge.svg'        # SVG 输出路径

      - name: Commit changes
        run: |
          git config user.name "github-actions[bot]"
          git config user.email "41898282+github-actions[bot]@users.noreply.github.com"
          git add README.md badge.svg
          git diff --cached --quiet || git commit -m "docs: update token count badge"
          git push
```

### 3. 修改 README.md

在 README 中添加以下内容（注意：图片 badge 放在标记之外，避免被 action 覆盖）：

```markdown
# Project Name

<a href="https://github.com/qwibitai/nanoclaw/tree/main/repo-tokens"><img src="badge.svg" alt="Token count badge"></a>

<!-- token-count --><!-- /token-count -->

项目描述...
```

### 4. 提交并推送

```bash
git add -A
git commit -m "feat: add token count badge"
git push
```

## 参数说明

| 参数 | 必填 | 默认值 | 说明 |
|------|------|--------|------|
| `include` | 是 | - | Glob 模式，指定统计的文件 |
| `exclude` | 否 | `''` | 排除的文件模式 |
| `context-window` | 否 | `200000` | LLM 上下文窗口大小（Claude Opus 为 200k）|
| `badge-path` | 否 | `''` | SVG badge 输出路径，为空时只更新文字 |
| `readme` | 否 | `README.md` | README 文件路径 |
| `encoding` | 否 | `cl100k_base` | Tiktoken 编码方式 |

## Badge 颜色含义

- 🟢 **绿色** (< 30%)：代码库很小，轻松放入上下文
- 🟡 **黄绿** (30-50%)：适中，可以放入上下文
- 🟠 **黄色** (50-70%)：较大，需要注意
- 🔴 **红色** (70%+)：很大，可能需要分段处理

## 常见项目配置示例

### TypeScript 项目
```yaml
include: 'src/**/*.ts'
exclude: 'src/**/*.test.ts,src/**/*.spec.ts'
```

### Python 项目
```yaml
include: 'src/**/*.py'
exclude: '**/test_*.py,**/tests/**/*.py'
```

### 多语言项目
```yaml
include: 'src/**/*.{ts,js,py}'
exclude: '**/node_modules/**,**/__pycache__/**'
```

## 故障排除

### Badge 不显示图片只显示文字
确保：
1. 设置了 `badge-path: 'badge.svg'`
2. README 中 `<img src="badge.svg">` 放在 `<!-- token-count -->` 标记**之外**
3. Workflow 中 `git add` 包含 `badge.svg`

### Action 运行失败
检查：
1. 仓库是否有 `main` 分支（或修改 workflow 中的分支名）
2. `permissions` 是否包含 `contents: write`
3. 如果是空仓库，先创建初始提交

## 参考

- 原始 Action: https://github.com/qwibitai/nanoclaw/tree/main/repo-tokens
- Badge 示例: https://raw.githubusercontent.com/qwibitai/NanoClaw/main/repo-tokens/badge.svg
