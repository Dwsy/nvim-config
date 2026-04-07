# NvChad 配置指南 — 文档网站项目

## 项目概述

这是一个基于 **MkDocs Material** 构建的中文 Neovim 配置教程文档网站。它为 NvChad（Neovim 配置框架）提供了一套从零到完整的中文学习指南，涵盖安装配置、核心功能、插件管理、多语言开发等主题。

- **仓库地址**: https://github.com/Dwsy/nvim-config
- **当前版本**: v2.0.0
- **文档语言**: 中文（zh）
- **文档数量**: 22 篇
- **总字数**: ~18,000+ 字

## 目录结构

```
.
├── docs/                      # 文档源文件（Markdown）
│   ├── index.md               # 首页（文档导航入口）
│   ├── README.md              # 快速开始指南
│   ├── 404.md                 # 404 页面
│   │
│   ├── CONFIG_STRUCTURE.md    # 配置结构详解
│   ├── WORKFLOW.md            # 开发工作流
│   ├── VIM_KEYS.md            # Vim 基础快捷键
│   ├── EDITING_TIPS.md        # 高效编辑技巧
│   │
│   ├── LSP_GUIDE.md           # LSP 代码智能
│   ├── TREESITTER.md          # Treesitter 语法高亮
│   ├── CONFORM_FORMAT.md      # 代码格式化
│   ├── TELESCOPE.md           # Telescope 搜索
│   ├── GIT_INTEGRATION.md     # Git 集成
│   │
│   ├── LAZY_NVM.md            # Lazy 插件管理
│   ├── MASON.md               # Mason 工具管理
│   │
│   ├── THEME_UI.md            # 主题与 UI 定制
│   ├── KEYMAPS.md             # 快捷键自定义
│   │
│   ├── MULTILANG_SETUP.md     # 多语言开发环境
│   │
│   ├── NAVIGATION.md          # 导航与 Tabs
│   ├── TABUFLINE_SESSION.md   # Tabufline & Session
│   ├── PERFORMANCE.md         # 性能优化
│   │
│   ├── TROUBLESHOOTING.md     # 故障排查
│   └── CONFIG_LOG.md          # 配置日志
│
├── site/                      # MkDocs 构建产物（HTML）
├── .github/workflows/
│   └── deploy.yml             # GitHub Pages 自动部署
├── mkdocs.yml                 # MkDocs 配置文件
├── .gitignore                 # Git 忽略配置
└── QWEN.md                    # 本项目说明
```

## 技术栈

| 组件 | 技术 |
|------|------|
| 文档框架 | MkDocs |
| 主题 | Material for MkDocs |
| Markdown 扩展 | pymdown-extensions（highlight, superfences, tabbed, admonition, details, tasklist） |
| 搜索 | MkDocs search（英文） |
| CI/CD | GitHub Actions（push → build → GitHub Pages） |
| 部署 | GitHub Pages（`gh-pages` 分支） |

## 文档导航结构

```yaml
首页
├── 快速入门（4 篇）
│   ├── 配置结构详解
│   ├── 开发工作流
│   ├── Vim 快捷键
│   └── 高效编辑技巧
├── 核心功能（5 篇）
│   ├── LSP 代码智能
│   ├── Treesitter 语法高亮
│   ├── 代码格式化
│   ├── Telescope 搜索
│   └── Git 集成
├── 插件与工具（2 篇）
│   ├── Lazy 插件管理
│   └── Mason 工具管理
├── 个性化（2 篇）
│   ├── 主题与 UI 定制
│   └── 快捷键自定义
├── 多语言开发（1 篇）
│   └── 多语言环境配置
├── 进阶使用（3 篇）
│   ├── 导航与 Tabs
│   ├── Tabufline & Session
│   └── 性能优化
└── 参考（2 篇）
    ├── 故障排查
    └── 配置日志
```

## 常用命令

### 本地预览

```bash
mkdocs serve
# 访问 http://127.0.0.1:8000
```

### 构建网站

```bash
mkdocs build
# 输出到 site/ 目录
```

### 安装依赖（首次使用）

```bash
pip install mkdocs mkdocs-material pymdown-extensions
```

### Git 操作

```bash
git add docs/*.md mkdocs.yml
git commit -m "docs: 更新文档内容"
git push
git tag -a vX.Y.Z -m "release: 描述"
git push origin vX.Y.Z
```

## CI/CD

项目配置了 **GitHub Actions** 自动部署：

- **触发条件**: push 到 `main` 分支 或 pull_request
- **构建环境**: Ubuntu + Python 3.11
- **部署目标**: GitHub Pages（`gh-pages` 分支）
- **工作流文件**: `.github/workflows/deploy.yml`

## 开发约定

1. **文档语言**: 所有文档使用中文编写
2. **格式规范**:
   - 每个文档包含"一句话总结"
   - 包含速查表（快捷键/命令）
   - 包含常见问题（FAQ）
   - 包含相关文档链接
3. **文档命名**: 全大写 + 下划线（如 `LAZY_NVM.md`）
4. **版本管理**: 重大更新打标签（vX.Y.Z）
5. **构建产物**: `site/` 目录被 `.gitignore` 忽略，由 CI 自动生成

## 关键配置说明

### MkDocs 配置（mkdocs.yml）

- `use_directory_urls: false` — 使用 `.html` 后缀（非目录 URL）
- `theme.language: zh` — 中文界面
- `theme.palette` — 双主题切换（slate 深色 / default 浅色）
- `plugins.search.lang: en` — 搜索使用英文索引（Material 不支持中文搜索）

### 主题特性

- 导航栏标签（`navigation.tabs`）
- 章节展开（`navigation.sections`）
- 目录集成（`toc.integrate`）
- 搜索建议（`search.suggest`）
- 搜索高亮（`search.highlight`）
- 代码复制（`content.code.copy`）

## 注意事项

1. **README.md 冲突**: `mkdocs.yml` 中未包含 README.md（与 index.md 冲突），它作为独立的快速开始页面存在
2. **404.md**: 未包含在导航中，仅作为 404 页面使用
3. **site/ 目录**: 被 `.gitignore` 忽略，不在版本控制中，由 CI 自动构建
4. **中文搜索限制**: MkDocs Material 搜索仅支持英文索引，中文搜索效果有限

## 相关项目

- **Neovim 配置仓库**: https://github.com/Dwsy/nvim-config（实际的 `~/.config/nvim/` 配置）
- **NvChad 官方**: https://nvchad.com/
