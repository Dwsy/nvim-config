# NvChad 配置文档

## 目录结构

```
~/.config/nvim/
├── init.lua                 # 入口文件
├── lazy-lock.json           # 插件版本锁定
├── lua/
│   ├── autocmds.lua         # 自动命令
│   ├── chadrc.lua           # NvChad 主配置 (主题等)
│   ├── mappings.lua         # 自定义快捷键
│   ├── options.lua          # 编辑器选项
│   ├── configs/
│   │   ├── conform.lua      # 格式化配置
│   │   ├── lazy.lua         # lazy.nvim 配置
│   │   └── lspconfig.lua    # LSP 配置
│   └── plugins/
│       └── init.lua         # 自定义插件
```

## 快捷键

### 通用

| 快捷键 | 功能 |
|--------|------|
| `Space` | Leader 键 |
| `Space + ch` | 快捷键速查表 (Cheatsheet) |
| `Space + th` | 切换主题 |
| `Ctrl + s` | 保存文件 |
| `Ctrl + c` | 复制整行 |

### 文件导航

| 快捷键 | 功能 |
|--------|------|
| `Space + e` | 切换文件树 |
| `Space + ff` | 查找文件 |
| `Space + fw` | 全局搜索内容 |
| `Space + fb` | 查找 Buffer |
| `Space + fo` | 最近打开文件 |
| `Space + fa` | 查找所有文件 (含隐藏) |

### Buffer 操作

| 快捷键 | 功能 |
|--------|------|
| `Tab` | 下一个 Buffer |
| `Shift + Tab` | 上一个 Buffer |
| `Space + x` | 关闭当前 Buffer |
| `Space + b` | 新建 Buffer |

### 窗口操作

| 快捷键 | 功能 |
|--------|------|
| `Ctrl + h/j/k/l` | 窗口间移动 |
| `Space + v` | 垂直分屏 |
| `Space + h` | 水平分屏 |

### LSP (代码智能)

| 快捷键 | 功能 |
|--------|------|
| `gd` | 跳转到定义 |
| `gD` | 跳转到声明 |
| `gr` | 查找引用 |
| `gi` | 跳转到实现 |
| `K` | 悬浮文档 |
| `Space + ca` | 代码操作 |
| `Space + ra` | 重命名符号 |
| `Space + ds` | 文档符号 |
| `[d` / `]d` | 上/下一个诊断 |
| `Space + lf` | 格式化代码 |

### 终端

| 快捷键 | 功能 |
|--------|------|
| `Alt + i` | 浮动终端 |
| `Alt + h` | 水平终端 |
| `Alt + v` | 垂直终端 |

### Git (Gitsigns)

| 快捷键 | 功能 |
|--------|------|
| `]c` / `[c` | 下/上一个 hunk |
| `Space + rh` | 重置 hunk |
| `Space + ph` | 预览 hunk |
| `Space + gb` | Git blame 行 |

### 注释

| 快捷键 | 功能 |
|--------|------|
| `gcc` | 切换行注释 |
| `gbc` | 切换块注释 |
| `gc` (visual) | 注释选中区域 |

## 命令

### 插件管理

```vim
:Lazy              " 打开插件管理器
:Lazy sync         " 同步插件
:Lazy update       " 更新插件
```

### Treesitter

```vim
:TSInstall <lang>     " 安装语法解析器
:TSUpdate             " 更新所有解析器
```

### Mason (LSP/工具管理)

```vim
:Mason                " 打开 Mason UI
:MasonInstallAll      " 安装所有配置的工具
```

### LSP

```vim
:LspInfo              " 查看当前 LSP 状态
:LspRestart           " 重启 LSP
```

## 已安装语言支持

| 语言 | LSP | 格式化 | Treesitter |
|------|-----|--------|------------|
| TypeScript/JS | ts_ls | prettier | ✓ |
| Vue | vue_ls | prettier | ✓ |
| Python | pyright | ruff | ✓ |
| Rust | rust_analyzer | rustfmt | ✓ |
| Java | jdtls | - | ✓ |
| Lua | lua_ls | stylua | ✓ |
| HTML/CSS | html/cssls | prettier | ✓ |

## 配置文件

### plugins/init.lua

```lua
return {
  {
    "stevearc/conform.nvim",
    event = "BufWritePre",
    opts = require "configs.conform",
  },

  {
    "neovim/nvim-lspconfig",
    config = function()
      require "configs.lspconfig"
    end,
  },

  {
    "williamboman/mason.nvim",
    opts = {
      ensure_installed = {
        "typescript-language-server",
        "vue-language-server",
        "pyright",
        "rust-analyzer",
        "jdtls",
        "lua-language-server",
        "html-lsp",
        "css-lsp",
        "prettier",
        "stylua",
        "ruff",
      },
    },
  },

  {
    "nvim-treesitter/nvim-treesitter",
    branch = "master",
    build = ":TSUpdate",
    opts = {
      ensure_installed = {
        "vim", "lua", "vimdoc", "html", "css",
        "typescript", "javascript", "tsx", "json",
        "python", "rust", "java", "toml", "yaml", "markdown",
        "vue",
      },
    },
  },

  {
    "nvim-tree/nvim-tree.lua",
    opts = {
      renderer = {
        group_empty = true,  -- 合并空目录 (Java packages)
      },
    },
  },
}
```

### configs/lspconfig.lua

```lua
require("nvchad.configs.lspconfig").defaults()

local servers = {
  "html",
  "cssls",
  "ts_ls",          -- TypeScript/JavaScript
  "vue_ls",         -- Vue
  "pyright",        -- Python
  "rust_analyzer",  -- Rust
  "jdtls",          -- Java
  "lua_ls",         -- Lua
}

vim.lsp.enable(servers)
```

### configs/conform.lua

```lua
local options = {
  formatters_by_ft = {
    lua = { "stylua" },
    python = { "ruff_format" },
    javascript = { "prettier" },
    typescript = { "prettier" },
    vue = { "prettier" },
    json = { "prettier" },
    html = { "prettier" },
    css = { "prettier" },
  },

  format_on_save = {
    timeout_ms = 500,
    lsp_fallback = true,
  },
}

return options
```

## 添加新语言支持

1. **Mason** - 添加 LSP 服务器到 `ensure_installed`
2. **lspconfig** - 添加服务器名到 `servers` 列表
3. **Treesitter** - 添加语言到 `ensure_installed`
4. **Conform** - 添加格式化工具 (可选)

## 故障排除

```vim
:checkhealth       " 检查健康状态
:Mason             " 确保 LSP 已安装
:LspInfo           " 检查 LSP 状态
:TSInstallInfo     " 检查 Treesitter 状态
```

### 重置配置

```bash
rm -rf ~/.local/share/nvim
rm -rf ~/.local/state/nvim
rm -rf ~/.cache/nvim
nvim  # 重新安装插件
```
