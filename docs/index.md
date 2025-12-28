# 🚀 NvChad 完整配置指南

> 从零到英雄 | 最接地气的 Neovim 配置教程

![Neovim](https://img.shields.io/badge/Neovim-0.11.5-brightgreen?logo=neovim)
![License](https://img.shields.io/badge/License-MIT-blue)
![Stars](https://img.shields.io/github/stars/Dwsy/nvim-config)

---

## 📚 文档导航

### 🎯 快速开始
- **[配置指南](README.md)** - NvChad 完整配置（LSP、Treesitter、Mason）
- **[开发工作流](WORKFLOW.md)** - 日常开发完整流程与实战场景
- **[配置日志](CONFIG_LOG.md)** - 完整的实战配置日志（2025-12-28）

### 🧠 学习路线
1. **[Vim 基础快捷键](VIM_KEYS.md)** - 从基础开始掌握 Vim
2. **[高效编辑技巧](EDITING_TIPS.md)** - 文本对象、多行编辑、宏录制
3. **[导航与 Tabs 管理](NAVIGATION.md)** - 跳转历史、Buffer 管理、场景布局
4. **[Tabufline & Session](TABUFLINE_SESSION.md)** - 状态保留、工作流自动化

### 🔌 核心功能
- **[LSP 代码智能](LSP_GUIDE.md)** - 跳转定义、引用、补全、诊断
- **[Telescope 搜索](TELESCOPE.md)** - 模糊搜索、文件查找、全局搜索
- **[Git 集成](GIT_INTEGRATION.md)** - Gitsigns、Blame、Hunk 操作

### 🔧 参考手册
- **[故障排查](TROUBLESHOOTING.md)** - 常见问题解决方案

---

## 💡 核心功能

### ✅ 已实现
- **LSP 支持** - TypeScript / Vue / Python / Rust / Java（含 Lombok）
- **代码高亮** - Treesitter（master 分支兼容）
- **代码格式化** - Conform（prettier / stylua / ruff）
- **文件树** - nvim-tree（Java 包目录合并）
- **Fuzzy 搜索** - Telescope（快速找文件、Buffer、最近打开）
- **Git 集成** - Gitsigns（Blame / Hunk / Diff）
- **代码片段** - LuaSnip + friendly-snippets
- **自动补全** - nvim-cmp
- **Mason** - 自动安装 LSP / Formatter / 工具

### 🎨 UI / UX
- **主题** - 可切换多个主题
- **状态栏** - NvChad 自定义 statusline
- **终端** - 内置终端集成
- **导航** - WhichKey 快捷键提示

---

## 🛠️ 支持的语言

| 语言 | LSP | 格式化 | Treesitter | 特性 |
|------|-----|--------|------------|------|
| TypeScript/JS | ts_ls | prettier | ✓ | - |
| Vue | vue_ls | prettier | ✓ | - |
| Python | pyright | ruff | ✓ | - |
| Rust | rust_analyzer | rustfmt | ✓ | - |
| Java | jdtls | - | ✓ | **Lombok 支持** |
| Lua | lua_ls | stylua | ✓ | - |
| HTML/CSS | html/cssls | prettier | ✓ | - |

---

## 🚀 5 分钟快速开始

### 1. 安装 Neovim v0.11+
```bash
brew install neovim
```

### 2. 克隆本仓库
```bash
git clone https://github.com/Dwsy/nvim-config ~/.config/nvim
```

### 3. 启动并安装
```bash
nvim
:MasonInstallAll    # 安装所有 LSP / 工具
```

### 4. 测试 LSP
```bash
nv your-project/    # 打开项目
gd                  # 试试跳转定义
```

---

## 📖 必读快捷键

### 导航（最常用）
| 快捷键 | 功能 |
|--------|------|
| `gd` | 跳到定义 |
| `Ctrl+o` | 后退 |
| `Ctrl+i` | 前进 |
| `Space+ff` | 查找文件 |
| `Space+fb` | 切换 Buffer |

### 编辑
| 快捷键 | 功能 |
|--------|------|
| `gcc` | 行注释 |
| `Space+lf` | 格式化代码 |
| `Space+ca` | 代码操作 |

### 工作流
| 快捷键 | 功能 |
|--------|------|
| `:SessionSave` | 保存工作区 |
| `:SessionLoad` | 恢复工作区 |
| `gt` / `gT` | 切换 Tab |

---

## 🎓 学习路径

### 第 1 天：基础
- 读 [Vim 基础快捷键](VIM_KEYS.md) - 掌握基本操作
- 在编辑器里练习 `hjkl` / `yy` / `dd`

### 第 2 天：导航
- 读 [导航与 Tabs 管理](NAVIGATION.md)
- 学会 `gd` / `Ctrl+o` / `Space+fb`
- 理解 Buffer vs Tab 的区别

### 第 3 天：工作流
- 读 [Tabufline & Session](TABUFLINE_SESSION.md)
- 配置自己的工作区（前端 / 后端 / 文档）
- 使用 `:SessionSave` 保留状态

### 持续：深入
- [完整配置指南](README.md) - 了解每个配置
- [配置日志](CONFIG_LOG.md) - 看实战过程

---

## 🔥 为什么选择 NvChad？

- ⚡ **极速启动** - 0.07 秒
- 🎨 **开箱即用** - 无需复杂配置
- 🧩 **易于扩展** - 简单的 Lua 配置
- 📚 **社区活跃** - 27.6k GitHub Stars
- 🛠️ **工程级** - 用于生产环境

---

## 💬 常见问题

### Q: 和 VS Code 有什么区别？
A: NvChad 是终端编辑器，启动快、占用少、完全可编程。一旦学会了，效率远高于 VS Code。

### Q: 学习曲线陡不陡？
A: 有点，但本文档从基础开始。按推荐路径走，3 天能上手，1 周就会爱上。

### Q: 可以用鼠标吗？
A: 可以，但不推荐。Vim 的强项就是键盘效率。

### Q: LSP / Treesitter 有什么区别？
A: Treesitter = 语法高亮 + 缩进。LSP = 代码智能（定义 / 引用 / 补全）。都需要。

---

## 📝 这个项目的故事

这份文档源于 **2025-12-28** 的一场完整配置过程：

- 安装 NvChad，遇到 treesitter 兼容性问题（main vs master 分支）
- 配置 LSP（TS / Vue / Python / Rust / Java）
- 解决 Java + Lombok 支持
- 整理导航系统（Jump List / Buffer / Session）
- 编写完整中文文档

👉 **每个坑都踩过，每个答案都是真实的。**

---

## 🌟 相关资源

- [NvChad 官方](https://nvchad.com/)
- [Neovim 官方文档](https://neovim.io/)
- [Vim 中文文档](https://yianwillis.github.io/vimcdoc/)
- [Practical Vim](https://pragprog.com/titles/dnvim2/practical-vim-second-edition/)

---

## 📄 许可证

MIT

---

## 🙋 贡献

欢迎提 Issue / PR！

- 配置有问题？提 Issue
- 想补充内容？提 PR
- 翻译其他语言？告诉我

---

**祝你爱上 Vim！** 🚀

*最后更新：2025-12-28*
