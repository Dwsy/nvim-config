# Neovim 快捷键自定义完全指南

## 快捷键系统概述

Neovim 的快捷键系统非常强大：

- ⌨️ **多模式** - 普通/插入/可视/终端模式
- 🔗 **组合键** - Leader 键 + 其他键
- 📝 **描述支持** - WhichKey 提示
- 🎯 **精确控制** - 模式/上下文/条件

---

## 你的当前快捷键

配置位置：`~/.config/nvim/lua/mappings.lua`

你的配置：
```lua
require "nvchad.mappings"

local map = vim.keymap.set

map("n", ";", ":", { desc = "CMD enter command mode" })
map("i", "jk", "<ESC>")
```

**已配置的快捷键**：
- `;` → `:`（普通模式，快速进入命令模式）
- `jk` → `<ESC>`（插入模式，快速退出）

---

## 快捷键基础语法

### vim.keymap.set

```lua
vim.keymap.set(模式, 快捷键, 命令, 选项)
```

### 模式

| 模式 | 说明 | 使用场景 |
|------|------|---------|
| `"n"` | 普通模式 | 导航、文件操作 |
| `"i"` | 插入模式 | 文本输入 |
| `"v"` | 可视模式 | 选择文本 |
| `"t"` | 终端模式 | 终端操作 |
| `"x"` | 可视模式（排除选择） | 特殊选择 |
| `"s"` | 选择模式 | 替换文本 |
| `{ "n", "v" }` | 多模式 | 通用操作 |

### 快捷键

```lua
-- 普通键
"a"
"<Esc>"
"<CR>"        -- Enter
"<Tab>"
"<Space>"

-- 组合键
"<C-s>"       -- Ctrl+S
"<C-w>"       -- Ctrl+W
"<A-h>"       -- Alt+H（终端可能不支持）
"<M-h>"       -- Meta+H（同 Alt）

-- Leader 键（你的配置中是 Space）
"<leader>f"   -- Space+F
"<leader>ff"  -- Space+F+F

-- 特殊键
"<BS>"        -- Backspace
"<Del>"       -- Delete
"<Home>"
"<End>"
"<PageUp>"
"<PageDown>"
```

### 选项

```lua
{
  desc = "描述",         -- WhichKey 显示
  silent = true,         -- 不显示命令
  noremap = true,        -- 不递归（默认 true）
  remap = false,         -- 允许递归
  nowait = true,         -- 不等待其他快捷键
  buffer = bufnr,        -- 仅当前 buffer
  expr = true,           -- 命令是表达式
}
```

---

## 常用快捷键配置

### 文件操作

```lua
local map = vim.keymap.set

-- 保存
map("n", "<leader>w", "<cmd>w<CR>", { desc = "Save file" })

-- 保存所有
map("n", "<leader>W", "<cmd>wa<CR>", { desc = "Save all files" })

-- 退出
map("n", "<leader>q", "<cmd>q<CR>", { desc = "Quit" })

-- 保存并退出
map("n", "<leader>x", "<cmd>x<CR>", { desc = "Save and quit" })

-- 强制退出
map("n", "<leader>Q", "<cmd>q!<CR>", { desc = "Force quit" })

-- 重新加载文件
map("n", "<leader>r", "<cmd>e!<CR>", { desc = "Reload file" })
```

### 导航

```lua
-- 查找文件
map("n", "<leader>ff", "<cmd>Telescope find_files<CR>", { desc = "Find files" })

-- 全局搜索
map("n", "<leader>fw", "<cmd>Telescope live_grep<CR>", { desc = "Search text" })

-- Buffer 切换
map("n", "<leader>fb", "<cmd>Telescope buffers<CR>", { desc = "Find buffers" })

-- 最近文件
map("n", "<leader>fo", "<cmd>Telescope oldfiles<CR>", { desc = "Recent files" })

-- Git 状态
map("n", "<leader>gs", "<cmd>Telescope git_status<CR>", { desc = "Git status" })

-- 文件树
map("n", "<leader>e", "<cmd>NvimTreeToggle<CR>", { desc = "Toggle file tree" })
```

### 编辑

```lua
-- 格式化代码
map("n", "<leader>lf", "<cmd>lua require('conform').format()<CR>", { desc = "Format code" })

-- 注释
map("n", "<leader>/", "<cmd>lua require('Comment.api').toggle_current_linewise()<CR>", { desc = "Toggle comment" })
map("v", "<leader>/", "<cmd>lua require('Comment.api').toggle_linewise_op(vim.fn.visualmode())<CR>", { desc = "Toggle comment" })

-- 复制到系统剪贴板
map({ "n", "v" }, "<leader>y", '"+y', { desc = "Copy to clipboard" })
map("n", "<leader>Y", '"+Y', { desc = "Copy line to clipboard" })

-- 粘贴
map({ "n", "v" }, "<leader>p", '"+p', { desc = "Paste from clipboard" })
```

### LSP

```lua
-- 跳转定义
map("n", "gd", "<cmd>Telescope lsp_definitions<CR>", { desc = "Go to definition" })

-- 查看引用
map("n", "gr", "<cmd>Telescope lsp_references<CR>", { desc = "Go to references" })

-- 查看实现
map("n", "gi", "<cmd>Telescope lsp_implementations<CR>", { desc = "Go to implementation" })

-- 查看类型定义
map("n", "gD", "<cmd>Telescope lsp_type_definitions<CR>", { desc = "Go to type definition" })

-- 悬浮文档
map("n", "K", "<cmd>lua vim.lsp.buf.hover()<CR>", { desc = "Hover documentation" })

-- 代码操作
map("n", "<leader>ca", "<cmd>lua vim.lsp.buf.code_action()<CR>", { desc = "Code action" })

-- 重命名
map("n", "<leader>rn", "<cmd>lua vim.lsp.buf.rename()<CR>", { desc = "Rename symbol" })

-- 诊断
map("n", "[d", "<cmd>lua vim.diagnostic.goto_prev()<CR>", { desc = "Previous diagnostic" })
map("n", "]d", "<cmd>lua vim.diagnostic.goto_next()<CR>", { desc = "Next diagnostic" })

-- 诊断列表
map("n", "<leader>q", "<cmd>lua vim.diagnostic.setloclist()<CR>", { desc = "Diagnostic list" })
```

### 窗口管理

```lua
-- 分屏
map("n", "<leader>sv", "<C-w>v", { desc = "Split vertical" })
map("n", "<leader>sh", "<C-w>s", { desc = "Split horizontal" })

-- 切换窗口
map("n", "<C-h>", "<C-w>h", { desc = "Move to left window" })
map("n", "<C-j>", "<C-w>j", { desc = "Move to bottom window" })
map("n", "<C-k>", "<C-w>k", { desc = "Move to top window" })
map("n", "<C-l>", "<C-w>l", { desc = "Move to right window" })

-- 调整窗口大小
map("n", "<C-Up>", "<cmd>resize +2<CR>", { desc = "Increase window height" })
map("n", "<C-Down>", "<cmd>resize -2<CR>", { desc = "Decrease window height" })
map("n", "<C-Left>", "<cmd>vertical resize -2<CR>", { desc = "Increase window width" })
map("n", "<C-Right>", "<cmd>vertical resize +2<CR>", { desc = "Decrease window width" })

-- 均分窗口
map("n", "<leader>=", "<C-w>=", { desc = "Equal window sizes" })

-- 关闭当前窗口
map("n", "<leader>sq", "<C-w>q", { desc = "Close window" })
```

### 终端

```lua
-- 打开终端
map("n", "<leader>ht", "<cmd>lua require('nvchad.term').toggle({ pos = 'float' })<CR>", { desc = "Toggle floating terminal" })
map("n", "<leader>hv", "<cmd>lua require('nvchad.term').toggle({ pos = 'sp', id = 'hterm' })<CR>", { desc = "Toggle horizontal terminal" })
map("n", "<leader>hh", "<cmd>lua require('nvchad.term').toggle({ pos = 'vsp', id = 'vterm' })<CR>", { desc = "Toggle vertical terminal" })

-- 终端模式退出
map("t", "<Esc>", "<C-\\><C-n>", { desc = "Exit terminal mode" })
```

### 主题切换

```lua
-- 切换深色/浅色
map("n", "<leader>tt", "<cmd>lua require('base46').toggle_theme()<CR>", { desc = "Toggle theme" })

-- 选择主题
map("n", "<leader>tc", "<cmd>Telescope themes<CR>", { desc = "Choose theme" })
```

### Lazy 插件管理

```lua
-- 打开 Lazy
map("n", "<leader>pl", "<cmd>Lazy<CR>", { desc = "Open Lazy" })

-- 更新插件
map("n", "<leader>pu", "<cmd>Lazy update<CR>", { desc = "Update plugins" })

-- 同步插件
map("n", "<leader>ps", "<cmd>Lazy sync<CR>", { desc = "Sync plugins" })
```

### Mason 工具管理

```lua
-- 打开 Mason
map("n", "<leader>sm", "<cmd>Mason<CR>", { desc = "Open Mason" })

-- 更新工具
map("n", "<leader>su", "<cmd>MasonUpdate<CR>", { desc = "Update Mason tools" })
```

---

## NvChad 默认快捷键

你的配置中 `require "nvchad.mappings"` 加载了 NvChad 默认快捷键：

### 通用

| 快捷键 | 功能 |
|--------|------|
| `Space` | Leader 键 |
| `;` | `:`（命令模式） |

### 文件

| 快捷键 | 功能 |
|--------|------|
| `Space+ff` | 查找文件 |
| `Space+fw` | 全局搜索 |
| `Space+fb` | Buffer 切换 |
| `Space+fo` | 最近文件 |
| `Space+e` | 文件树 |

### 编辑

| 快捷键 | 功能 |
|--------|------|
| `Space+lf` | 格式化 |
| `Space+ca` | 代码操作 |
| `Space+rn` | 重命名 |

### Git

| 快捷键 | 功能 |
|--------|------|
| `Space+gc` | 提交历史 |
| `Space+gs` | Git 状态 |

---

## 条件快捷键

### 仅特定文件类型

```lua
-- 仅为 Lua 文件配置快捷键
vim.api.nvim_create_autocmd("FileType", {
  pattern = "lua",
  callback = function()
    vim.keymap.set("n", "<leader>r", "<cmd>luafile %<CR>", { buffer = true, desc = "Run Lua file" })
  end,
})
```

### 仅特定目录

```lua
-- 检测项目根目录
local function has_file(pattern)
  return #vim.fn.glob(pattern) > 0
end

if has_file("package.json") then
  -- Node.js 项目快捷键
  vim.keymap.set("n", "<leader>nr", "!npm run<CR>", { desc = "NPM run" })
end
```

---

## 快捷键冲突解决

### 查看快捷键映射

```vim
" 查看某个快捷键
:verbose map <leader>f

" 查看所有快捷键
:map

" 查看普通模式
:nmap

" 查看插入模式
:imap
```

### 解除映射

```lua
-- 普通模式
vim.keymap.del("n", "<leader>f")

-- 插入模式
vim.keymap.del("i", "jk")

-- 多模式
vim.keymap.del({ "n", "v" }, "<leader>y")
```

### 覆盖映射

```lua
-- 直接重新配置
vim.keymap.set("n", "<leader>f", "<cmd>Telescope find_files<CR>", { desc = "Find files" })
```

---

## WhichKey 集成

WhichKey 会在按下 `<leader>` 后显示可用快捷键。

### 配置描述

```lua
vim.keymap.set("n", "<leader>ff", "<cmd>Telescope find_files<CR>", {
  desc = "Find files",  -- WhichKey 会显示这个描述
})
```

### 自定义 WhichKey

```lua
-- 添加自定义 WhichKey 分组
require("which-key").add({
  { "<leader>g", group = "Git" },
  { "<leader>p", group = "Plugins" },
  { "<leader>s", group = "Search" },
})
```

---

## 宏录制快捷键

### 录制宏

```vim
" 开始录制到寄存器 q
qq

" 执行操作
...一系列操作...

" 停止录制
q
```

### 绑定宏到快捷键

```lua
-- 执行寄存器 q 中的宏
vim.keymap.set("n", "<leader>m", "@q", { desc = "Run macro q" })

-- 执行 10 次
vim.keymap.set("n", "<leader>M", "10@q", { desc = "Run macro q 10 times" })
```

---

## 最佳实践

### ✅ 推荐

1. **使用 Leader 键** - 避免冲突（你的 Leader 是 Space）
2. **添加 `desc` 描述** - WhichKey 提示
3. **使用 `silent = true`** - 不显示命令
4. **分组组织** - `Space+f*` 文件，`Space+l*` LSP
5. **记录快捷键清单** - 方便查阅

### ❌ 避免

1. ❌ 覆盖重要默认键（如 `h`/`j`/`k`/`l`）
2. ❌ 使用 `Ctrl+字母`（终端可能拦截）
3. ❌ 快捷键太多（记住常用的即可）
4. ❌ 不添加描述（WhichKey 不显示）

---

## 快捷键分组建议

### 文件相关（Space+f*）

```lua
Space+ff    查找文件
Space+fw    搜索文本
Space+fb    Buffer 切换
Space+fo    最近文件
Space+fe    文件树
```

### LSP 相关（Space+l*）

```lua
Space+lf    格式化
Space+la    代码操作
Space+ln    重命名
Space+ld    诊断
```

### Git 相关（Space+g*）

```lua
Space+gs    Git 状态
Space+gc    提交历史
Space+gb    Blame
Space+gd    Diff
```

### 插件管理（Space+p*）

```lua
Space+pl    Lazy
Space+pu    更新插件
Space+ps    同步插件
```

### 工具管理（Space+s*）

```lua
Space+sm    Mason
Space+su    更新工具
```

---

## 速查表

### 语法

```lua
vim.keymap.set(模式, 快捷键, 命令, 选项)
```

### 常用选项

```lua
{
  desc = "描述",
  silent = true,
  noremap = true,
  nowait = true,
  buffer = bufnr,
}
```

### 排查

```vim
:verbose map <键>    查看映射来源
:map                 所有映射
:mapclear            清除所有（慎用）
```

---

## 一句话总结

**`vim.keymap.set` 配置 + `<leader>` 组织 + `desc` 提示 = 高效快捷键系统**

设计好快捷键分组，形成肌肉记忆，你的编辑速度会飞快。

---

## 相关文档

- [Vim 基础快捷键](VIM_KEYS.md) - 默认快捷键列表
- [配置结构详解](CONFIG_STRUCTURE.md) - mappings.lua 文件位置
- [高效编辑技巧](EDITING_TIPS.md) - 编辑操作组合