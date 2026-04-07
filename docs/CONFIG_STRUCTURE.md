# Neovim 配置结构详解

## 配置目录结构

你的 Neovim 配置位于 `~/.config/nvim/`，完整结构：

```
~/.config/nvim/
├── init.lua                    # 入口文件（启动配置）
├── chadrc.lua                  # NvChad 自定义配置
├── .stylua.toml                # StyLua 格式化配置
├── LICENSE                     # 许可证
├── README.md                   # 项目说明
├── lazy-lock.json              # 插件版本锁定
│
├── lua/                        # Lua 配置目录
│   ├── options.lua             # Vim 选项
│   ├── mappings.lua            # 快捷键映射
│   ├── autocmds.lua            # 自动命令
│   ├── chadrc.lua              # NvChad 配置（同根目录的 chadrc.lua）
│   │
│   ├── configs/                # 配置模块
│   │   ├── lazy.lua            # Lazy 插件管理配置
│   │   ├── lspconfig.lua       # LSP 服务器配置
│   │   └── conform.lua         # Conform 格式化配置
│   │
│   └── plugins/                # 插件配置
│       └── init.lua            # 插件列表与配置
│
└── .git/                       # Git 仓库
```

---

## 文件详解

### 1. `init.lua` - 入口文件

**作用**：Neovim 启动时第一个加载的文件

你的配置：
```lua
vim.g.base46_cache = vim.fn.stdpath "data" .. "/base46/"
vim.g.mapleader = " "

-- 1. 引导 Lazy 插件管理器
local lazypath = vim.fn.stdpath "data" .. "/lazy/lazy.nvim"

if not vim.uv.fs_stat(lazypath) then
  local repo = "https://github.com/folke/lazy.nvim.git"
  vim.fn.system { "git", "clone", "--filter=blob:none", repo, "--branch=stable", lazypath }
end

vim.opt.rtp:prepend(lazypath)

local lazy_config = require "configs.lazy"

-- 2. 加载插件
require("Lazy").setup({
  {
    "NvChad/NvChad",
    lazy = false,
    branch = "v2.5",
    import = "nvchad.plugins",
  },

  { import = "plugins" },
}, lazy_config)

-- 3. 加载主题缓存
dofile(vim.g.base46_cache .. "defaults")
dofile(vim.g.base46_cache .. "statusline")

-- 4. 加载配置模块
require "options"
require "autocmds"

vim.schedule(function()
  require "mappings"
end)

-- 5. 启用鼠标
vim.opt.mouse = "a"
```

**加载顺序**：
1. 设置全局变量（`mapleader`, `base46_cache`）
2. 引导 Lazy 插件管理器
3. 加载 NvChad 基础插件
4. 加载用户插件（`plugins/init.lua`）
5. 加载主题缓存
6. 加载 `options.lua`（Vim 选项）
7. 加载 `autocmds.lua`（自动命令）
8. 延迟加载 `mappings.lua`（快捷键）
9. 启用鼠标

---

### 2. `chadrc.lua` - NvChad 自定义配置

**作用**：覆盖 NvChad 默认配置

你的配置：
```lua
---@type ChadrcConfig
local M = {}

M.base46 = {
	theme = "ayu_dark",          -- 主题

	-- hl_override = {
	-- 	Comment = { italic = true },
	-- 	["@comment"] = { italic = true },
	-- },
}

M.ui = {
	tabufline = {
		lazyload = false,
		enabled = true,
	},
}

return M
```

**可配置项**：
- `base46.theme` - 主题名称
- `base46.hl_override` - 覆盖高亮组
- `ui.tabufline` - 顶部标签栏
- `ui.statusline` - 底部状态栏
- `ui.term` - 终端配置
- `ui.nvdash` - 启动仪表板

---

### 3. `lua/options.lua` - Vim 选项

**作用**：配置 Neovim 基础选项

你的配置：
```lua
require "nvchad.options"

-- 在这里添加自定义选项
-- local o = vim.o
-- o.cursorlineopt = 'both'
```

**常用选项示例**：

```lua
local o = vim.o

-- 显示
o.number = true                -- 显示行号
o.relativenumber = true        -- 相对行号
o.cursorline = true            -- 高亮当前行
o.wrap = false                 -- 不自动换行
o.linebreak = true             -- 单词级别换行

-- 缩进
o.tabstop = 2                  -- Tab 宽度
o.shiftwidth = 2               -- 缩进宽度
o.expandtab = true             -- Tab 转空格
o.autoindent = true            -- 自动缩进

-- 搜索
o.ignorecase = true            -- 忽略大小写
o.smartcase = true             -- 智能大小写
o.hlsearch = true              -- 高亮搜索
o.incsearch = true             -- 增量搜索

-- 编辑
o.backup = false               -- 不创建备份
o.writebackup = false          -- 不创建写入备份
o.swapfile = false             -- 不创建交换文件
o.undofile = true              -- 启用持久撤销

-- 性能
o.updatetime = 300             -- 更新间隔（ms）
o.timeoutlen = 300             -- 快捷键超时（ms）

-- 外观
o.termguicolors = true         -- 真彩色
o.signcolumn = "yes"           -- 始终显示符号列
```

---

### 4. `lua/mappings.lua` - 快捷键映射

**作用**：配置快捷键

你的配置：
```lua
require "nvchad.mappings"

local map = vim.keymap.set

map("n", ";", ":", { desc = "CMD enter command mode" })
map("i", "jk", "<ESC>")
```

**快捷键语法**：

```lua
vim.keymap.set(模式, 快捷键, 命令, 选项)

-- 模式：
--   "n"   - 普通模式
--   "i"   - 插入模式
--   "v"   - 可视模式
--   "t"   - 终端模式
--   { "n", "v" } - 多模式

-- 选项：
--   desc      - 描述（WhichKey 显示）
--   silent    - 不显示命令
--   noremap   - 不递归
--   remap     - 允许递归
```

**示例**：

```lua
-- 普通模式
map("n", "<leader>e", ":NvimTreeToggle<CR>", { desc = "Toggle file tree" })

-- 插入模式
map("i", "jk", "<ESC>", { desc = "Exit insert mode" })

-- 可视模式
map("v", "J", ":m '>+1<CR>gv=gv", { desc = "Move line down" })

-- 多模式
map({ "n", "v" }, "<leader>y", '"+y', { desc = "Copy to clipboard" })

-- 静默执行
map("n", "<leader>w", "<cmd>w<CR>", { silent = true, desc = "Save file" })
```

---

### 5. `lua/autocmds.lua` - 自动命令

**作用**：在特定事件时自动执行

你的配置：
```lua
-- 添加你的自动命令

-- local autocmd = vim.api.nvim_create_autocmd
-- autocmd("BufReadPost", {
--   callback = function()
--     -- 代码
--   end,
-- })
```

**常用自动命令示例**：

```lua
local autocmd = vim.api.nvim_create_autocmd
local augroup = vim.api.nvim_create_augroup

-- 创建自动命令组
local mygroup = augroup("MyAutoCmds", { clear = true })

-- 进入文件时自动跳到上次编辑位置
autocmd("BufReadPost", {
  callback = function()
    local mark = vim.api.nvim_buf_get_mark(0, '"')
    local lcount = vim.api.nvim_buf_line_count(0)
    if mark[1] > 0 and mark[1] <= lcount then
      pcall(vim.api.nvim_win_set_cursor, 0, mark)
    end
  end,
  group = mygroup,
})

-- 保存时自动格式化
autocmd("BufWritePre", {
  pattern = { "*.lua", "*.ts", "*.js", "*.vue" },
  callback = function()
    require("conform").format({ lsp_fallback = true })
  end,
  group = mygroup,
})

-- 大文件禁用 Treesitter
autocmd("BufReadPre", {
  callback = function()
    local file_size = vim.fn.getfsize(vim.fn.expand("%"))
    if file_size > 1024 * 1024 then  -- 1MB
      vim.cmd("TSBufDisable highlight")
    end
  end,
  group = mygroup,
})

-- 进入插入模式时自动补全
autocmd("InsertEnter", {
  callback = function()
    -- 代码
  end,
  group = mygroup,
})
```

---

### 6. `lua/configs/lazy.lua` - Lazy 配置

**作用**：Lazy 插件管理器配置

你的配置：
```lua
return {
  defaults = { lazy = true },        -- 默认懒加载
  install = { colorscheme = { "nvchad" } },  -- 安装时加载主题

  ui = {
    icons = {
      ft = "",
      lazy = "󰂠 ",
      loaded = "",
      not_loaded = "",
    },
  },

  performance = {
    rtp = {
      disabled_plugins = {           -- 禁用内置插件
        "2html_plugin",
        "gzip",
        "netrwPlugin",
        -- ... 更多
      },
    },
  },
}
```

---

### 7. `lua/configs/lspconfig.lua` - LSP 配置

**作用**：配置语言服务器

你的配置：
```lua
require("nvchad.configs.lspconfig").defaults()

local servers = {
  "html",
  "cssls",
  "ts_ls",          -- TypeScript/JavaScript
  "pyright",        -- Python
  "rust_analyzer",  -- Rust
  "lua_ls",         -- Lua
}

vim.lsp.enable(servers)

-- Vue LSP with TypeScript support
local ts_path = vim.fn.stdpath("data") .. "/mason/packages/typescript-language-server"

vim.lsp.config("vue_ls", {
  init_options = {
    typescript = {
      tsdk = ts_path .. "/node_modules/typescript/lib",
    },
  },
})
vim.lsp.enable("vue_ls")
```

---

### 8. `lua/configs/conform.lua` - Conform 格式化配置

**作用**：配置代码格式化工具

你的配置：
```lua
local options = {
  formatters_by_ft = {
    lua = { "stylua" },
    javascript = { "prettier" },
    typescript = { "prettier" },
    javascriptreact = { "prettier" },
    typescriptreact = { "prettier" },
    vue = { "prettier" },
    css = { "prettier" },
    scss = { "prettier" },
    less = { "prettier" },
    html = { "prettier" },
    json = { "prettier" },
    jsonc = { "prettier" },
    yaml = { "prettier" },
    markdown = { "prettier" },
    python = { "ruff" },
    rust = { "rustfmt" },
  },

  format_on_save = {
    timeout_ms = 500,
    lsp_fallback = true,
  },
}

return options
```

---

### 9. `lua/plugins/init.lua` - 插件列表

**作用**：声明并配置所有插件

你的配置：
```lua
return {
  -- Conform 格式化
  {
    "stevearc/conform.nvim",
    event = "BufWritePre",
    opts = require "configs.conform",
  },

  -- LSP 配置
  {
    "neovim/nvim-lspconfig",
    config = function()
      require "configs.lspconfig"
    end,
  },

  -- Java LSP
  {
    "mfussenegger/nvim-jdtls",
    ft = "java",
    config = function()
      -- Java 配置（含 Lombok 支持）
    end,
  },

  -- Mason 工具管理
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

  -- Treesitter 语法高亮
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
      highlight = { enable = true },
      indent = { enable = true },
    },
  },

  -- nvim-tree 文件树
  {
    "nvim-tree/nvim-tree.lua",
    opts = {
      renderer = {
        group_empty = true,
      },
    },
  },
}
```

---

### 10. `.stylua.toml` - StyLua 格式化配置

**作用**：配置 StyLua（Lua 代码格式化工具）

你的配置：
```toml
column_width = 120
line_endings = "Unix"
indent_type = "Spaces"
indent_width = 2
quote_style = "AutoPreferDouble"
call_parentheses = "None"
```

---

## 配置加载流程

```
Neovim 启动
    ↓
加载 init.lua
    ↓
设置全局变量（mapleader, base46_cache）
    ↓
引导 Lazy 插件管理器
    ↓
加载 NvChad 插件（v2.5）
    ↓
加载用户插件（plugins/init.lua）
    ↓
加载主题缓存（defaults, statusline）
    ↓
加载 options.lua（Vim 选项）
    ↓
加载 autocmds.lua（自动命令）
    ↓
延迟加载 mappings.lua（快捷键）
    ↓
启用鼠标
    ↓
启动完成
```

---

## 扩展配置

### 添加新插件

编辑 `~/.config/nvim/lua/plugins/init.lua`：

```lua
return {
  -- 现有插件...

  -- 新增插件
  {
    "作者/仓库名",
    event = "VeryStart",         -- 触发时机
    opts = { ... },              -- 配置选项
    config = function()          -- 自定义配置
      require("插件").setup({...})
    end,
  },
}
```

然后：
```vim
:w
:Lazy sync
```

### 修改快捷键

编辑 `~/.config/nvim/lua/mappings.lua`：

```lua
local map = vim.keymap.set

-- 添加新快捷键
map("n", "<leader>x", ":echo 'Hello'<CR>", { desc = "Test" })
```

### 修改选项

编辑 `~/.config/nvim/lua/options.lua`：

```lua
require "nvchad.options"

-- 添加自定义选项
vim.o.number = true
vim.o.relativenumber = true
```

---

## 配置备份与同步

### 备份配置

```bash
# 备份整个配置
cp -r ~/.config/nvim ~/.config/nvim.bak

# 或使用 Git
cd ~/.config/nvim
git add .
git commit -m "backup: 保存配置"
```

### 同步到新机器

```bash
# 推送代码
cd ~/.config/nvim
git push

# 在新机器上拉取
git clone https://github.com/你的用户名/nvim-config ~/.config/nvim
nvim  # 启动时自动安装插件
```

---

## 常见问题

### Q：修改配置后不生效？

**解决**：

```vim
" 方法 1：重启 Neovim
:qa          " 退出所有
nvim         " 重新启动

" 方法 2：重新加载配置
:source ~/.config/nvim/init.lua

" 方法 3：只加载特定文件
:source ~/.config/nvim/lua/mappings.lua
```

### Q：配置报错 `module not found`？

**排查**：

```vim
" 检查文件是否存在
:lua print(vim.fn.glob("~/.config/nvim/lua/configs/conform.lua"))

" 检查拼写错误
:lua print(package.path)
```

### Q：如何查看加载了哪些插件？

```vim
:Lazy           " 查看插件状态
:scriptnames    " 查看加载的脚本
```

---

## 速查表

| 文件 | 作用 |
|------|------|
| `init.lua` | 入口文件 |
| `chadrc.lua` | NvChad 自定义配置 |
| `lua/options.lua` | Vim 选项 |
| `lua/mappings.lua` | 快捷键 |
| `lua/autocmds.lua` | 自动命令 |
| `lua/configs/lazy.lua` | Lazy 配置 |
| `lua/configs/lspconfig.lua` | LSP 配置 |
| `lua/configs/conform.lua` | 格式化配置 |
| `lua/plugins/init.lua` | 插件列表 |
| `.stylua.toml` | StyLua 格式化 |

---

## 一句话总结

**`init.lua` 入口 → `plugins/init.lua` 插件 → `configs/` 模块 → `chadrc.lua` 主题 = 完整配置体系**

理解配置结构，你就能轻松定制 Neovim 的任何功能。

---

## 相关文档

- [Lazy 插件管理](LAZY_NVM.md) - 插件配置详解
- [主题与 UI 定制](THEME_UI.md) - chadrc.lua 完整配置
- [快捷键自定义](KEYMAPS.md) - mappings.lua 详解