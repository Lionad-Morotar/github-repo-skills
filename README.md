# github-repo-skills

GitHub 仓库增强技能集合，为 Claude Code 提供便捷的仓库管理功能。

## 技能列表

### repo-tokens-count

为 GitHub 项目添加 token count badge，自动计算代码库的 token 数量并生成可视化 SVG badge。

**功能：**
- 自动计算代码库的 token 数量
- 生成彩色 SVG badge（根据占比自动调整颜色）
- 支持自定义文件匹配模式
- 支持自定义 LLM 上下文窗口大小
- 通过 GitHub Actions 自动更新

**使用：**
```
/ 为当前项目添加 token count badge
```

## 安装

```bash
npx skills add -g https://github.com/Lionad-Morotar/github-repo-skills
```

## 命名规范

本项目遵循以下命名约定，避免出现重复后缀：
- **项目名称**：使用 `github-repo-skills`（单数或复数根据语境）
- **Skill 名称**：使用 `repo-tokens-count`（不含 `-skill` 后缀）
- **项目结构**：`skills/<skill-name>/SKILL.md`

避免使用 `github-repo-skills-skill` 这种重复后缀的形式。

## 开发

项目结构：
```
github-repo-skills/
├── README.md
└── skills/
    └── repo-tokens-counts/
        └── SKILL.md
```

添加新技能：
1. 在 `skills/` 目录下创建新文件夹
2. 编写 `SKILL.md` 文件
3. 提交并推送
