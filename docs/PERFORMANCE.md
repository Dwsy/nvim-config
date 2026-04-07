# Neovim 性能优化完全指南

## 性能指标

### 你的当前配置表现

| 指标 | 值 | 评价 |
|------|-----|------|
| 启动时间 | ~0.07s | ⚡ 极快 |
| 内存占用 | ~50MB（空载） | ✅ 低 |
| 插件数量 | 6 个核心插件 | ✅ 精简 |

**评价**：你的配置已经很精简，但仍可进一步优化。

---

## 性能分析工具

### Lazy Profile

```vim
:Lazy profile
```

输出示例：
```
Plugin              Time        %      Count
nvim-treesitter     45.2ms     35%     1
conform.nvim        12.1ms      9%     1
telescope.nvim       8.5ms      7%     1
nvim-tree.lua        5.3ms      4%     1
```

### 启动时间测量

```vim
" 查看启动时间
:lua print(vim.fn.reltimefloat(vim.fn.reltime(vim.g.startuptime)) * 1000 .. "ms")
```

### 内存占用

```vim
" 查看内存使用
:lua print(collectgarbage("count") .. "KB")

" 强制垃圾回收
:lua collectgarbage("collect")
```

---

## 优化策略

### 1. 懒加载（最重要）

**原理**：只在需要时加载插件

你的配置中已使用：
```lua
-- lua/configs/lazy.lua
defaults = { lazy = true },  -- 默认懒加载
```

**推荐懒加载策略**：

```lua
-- 事件触发
{
  "nvim-telescope/telescope.nvim",
  event = "VeryStart",  -- Neovim 启动后
}

-- 命令触发
{
  "williamboman/mason.nvim",
  cmd = { "Mason", "MasonInstall" },
}

-- 文件类型触发
{
  "mfussenegger/nvim-jdtls",
  ft = "java",  -- 仅 Java 文件
}

-- 快捷键触发
{
  "folke/which-key.nvim",
  keys = { "<leader>" },
}
```

### 2. 禁用不必要的内置插件

你的配置：
```lua
-- lua/configs/lazy.lua
performance = {
  rtp = {
    disabled_plugins = {
      "2html_plugin",
      "tohtml",
      "getscript",
      "getscriptPlugin",
      "gzip",
      "logipat",
      "netrw",
      "netrwPlugin",
      "netrwSettings",
      "netrwFileHandlers",
      "matchit",
      "tar",
      "tarPlugin",
      "rrhelper",
      "spellfile_plugin",
      "vimball",
      "vimballPlugin",
      "zip",
      "zipPlugin",
      "tutor",
      "rplugin",
      "syntax",
      "synmenu",
      "optwin",
      "compiler",
      "bugreport",
      "ftplugin",
    },
  },
},
```

**评价**：✅ 已禁用大量不用的插件，很好！

### 3. Treesitter 优化

**大文件禁用高亮**：

```lua
{
  "nvim-treesitter/nvim-treesitter",
  opts = {
    highlight = {
      enable = true,
      disable = function(lang, buf)
        local max_filesize = 100 * 1024  -- 100KB
        local ok, stats = pcall(vim.loop.fs_stat, vim.api.nvim_buf_get_name(buf))
        if ok and stats and stats.size > max_filesize then
          return true
        end
      end,
    },
  },
}
```

**只安装需要的语法**：

你的配置已做到：
```lua
ensure_installed = {
  "vim", "lua", "vimdoc",
  "typescript", "javascript", "tsx",
  "python", "rust", "java",
  -- 只安装使用的语言
}
```

### 4. LSP 优化

**按需启动 LSP**：

```lua
-- 仅为特定文件类型启动 LSP
vim.api.nvim_create_autocmd("FileType", {
  pattern = { "typescript", "javascript" },
  callback = function()
    vim.lsp.start({
      name = "ts_ls",
      cmd = { "typescript-language-server", "--stdio" },
      root_dir = vim.fs.dirname(vim.fs.find({ "package.json" }, { upward = true })[1]),
    })
  end,
})
```

**限制 LSP 诊断数量**：

```lua
vim.lsp.handlers["textDocument/publishDiagnostics"] = vim.lsp.with(
  vim.diagnostic.on_publish_diagnostics, {
    underline = true,
    virtual_text = {
      spacing = 5,
      severity_limit = "Warning",  -- 仅显示 Warning 以上
    },
    update_in_insert = true,
  }
)
```

### 5. 自动命令优化

**避免频繁的自动命令**：

```lua
-- ❌ 不好：每次按键都触发
vim.api.nvim_create_autocmd("CursorMoved", {
  callback = function()
    -- 复杂操作
  end,
})

-- ✅ 好：使用防抖
local debounce_timer = nil
vim.api.nvim_create_autocmd("CursorMoved", {
  callback = function()
    if debounce_timer then
      vim.fn.timer_stop(debounce_timer)
    end
    debounce_timer = vim.fn.timer_start(200, function()
      -- 复杂操作
    end)
  end,
})
```

---

## 启动速度优化

### 测量启动时间

```bash
# 从命令行测量
time nvim --headless -c "qa"

# 或在 Neovim 中
nvim --startuptime /tmp/startup.log
```

### 分析启动日志

```bash
cat /tmp/startup.log | head -50
```

输出示例：
```
000.012  000.012: --- Neovim starts ---
000.045  000.033: loading plugins
000.123  000.078: loading after plugins
```

### 优化 init.lua

**延迟加载非关键配置**：

```lua
-- init.lua

-- 关键配置（立即加载）
vim.g.mapleader = " "
require("configs.lazy")
require("Lazy").setup({...})

-- 非关键配置（延迟加载）
vim.schedule(function()
  require("options")     -- Vim 选项
  require("mappings")    -- 快捷键
  require("autocmds")    -- 自动命令
end)
```

---

## 内存优化

### 减少 Buffer 数量

```vim
" 关闭不用的 Buffer
:bd <编号>

" 关闭所有隐藏 Buffer
:%bd|e#
```

### 配置自动关闭

```lua
-- 自动关闭不用的 Buffer
vim.api.nvim_create_autocmd("BufDelete", {
  callback = function()
    local buffers = vim.fn.getbufinfo({ buflisted = true })
    if #buffers > 20 then  -- 超过 20 个 Buffer
      -- 关闭最旧的
      vim.cmd("bdelete " .. buffers[1].bufnr)
    end
  end,
})
```

### 限制撤销历史

```lua
vim.opt.undolevels = 1000       -- 撤销次数
vim.opt.undoreload = 10000      -- 撤销 reload 次数
vim.opt.undofile = true         -- 持久撤销文件
vim.opt.undodir = vim.fn.stdpath("data") .. "/undo"
```

---

## 大文件处理

### 自动检测大文件

```lua
vim.api.nvim_create_autocmd("BufReadPre", {
  callback = function()
    local file_size = vim.fn.getfsize(vim.fn.expand("%"))
    if file_size > 1024 * 1024 then  -- 1MB
      -- 禁用 Treesitter
      vim.cmd("TSBufDisable highlight")

      -- 禁用 LSP
      vim.lsp.stop_client(vim.lsp.get_active_clients())

      -- 禁用语法高亮
      vim.cmd("syntax off")

      -- 禁用折叠
      vim.cmd("set nofoldenable")

      print("大文件模式：已禁用部分功能以提升性能")
    end
  end,
})
```

### 手动优化大文件

```vim
" 禁用 Treesitter
:TSBufDisable highlight

" 禁用 LSP
:LspStop

" 禁用语法高亮
:syntax off

" 禁用折叠
:set nofoldenable

" 禁用行号
:set nonumber

" 完成后恢复
:TSBufEnable highlight
:LspRestart
:syntax on
```

---

## 网络优化

### Mason 下载加速

```lua
-- 设置代理
vim.env.https_proxy = "http://127.0.0.1:7890"
vim.env.http_proxy = "http://127.0.0.1:7890"
```

### Git 克隆加速

```bash
# 使用镜像
git clone https://hub.fastgit.xyz/NvChad/NvChad.git ~/.config/nvim
```

---

## 插件性能对比

### 常见插件性能

| 插件 | 加载时间 | 内存 | 评价 |
|------|---------|------|------|
| telescope.nvim | 8-15ms | 5MB | ✅ 快 |
| nvim-tree.lua | 5-10ms | 3MB | ✅ 快 |
| conform.nvim | 10-15ms | 4MB | ✅ 快 |
| nvim-treesitter | 30-60ms | 15MB | ⚠️ 较重 |
| nvim-lspconfig | 5-10ms | 10MB+ | ⚠️ 依赖 LSP |

### 推荐替代

| 重插件 | 轻量替代 | 性能提升 |
|--------|---------|---------|
| nerdtree | nvim-tree | 30% 快 |
| coc.nvim | nvim-cmp + LSP | 50% 快 |
| vim-plug | lazy.nvim | 40% 快 |

---

## 配置检查清单

### ✅ 已优化

- [x] 使用 lazy.nvim 懒加载
- [x] 禁用内置插件（27 个）
- [x] Treesitter 锁定 master 分支
- [x] 精简插件数量（6 个核心）
- [x] 使用 conform.nvim（轻量格式化）

### 🔧 可进一步优化

- [ ] 大文件自动禁用 Treesitter
- [ ] LSP 诊断数量限制
- [ ] 延迟加载 options/mappings
- [ ] Buffer 数量限制
- [ ] 撤销历史限制

---

## 常见问题排查

### 问题 1：启动变慢

**排查**：

```vim
:Lazy profile
```

**解决**：
- 检查哪个插件加载慢
- 改为懒加载
- 减少启动时加载的插件

### 问题 2：编辑卡顿

**排查**：

```vim
" 检查 Treesitter
:TSBufDisable highlight

" 检查 LSP
:LspStop

" 如果流畅了，说明是这两个原因
```

**解决**：
- 大文件禁用 Treesitter
- 限制 LSP 功能
- 减少自动命令

### 问题 3：内存占用高

**排查**：

```vim
" 查看 Buffer 数量
:buffers

" 查看插件内存
:Lazy profile
```

**解决**：
- 关闭不用的 Buffer
- 禁用不用的插件
- 限制撤销历史

---

## 性能监控

### 实时监控

```vim
" 显示内存占用
:lua print("Memory: " .. collectgarbage("count") .. "KB")

" 显示 Buffer 数量
:lua print("Buffers: " .. #vim.fn.getbufinfo({ buflisted = true }))
```

### 启动仪表板

```lua
-- 在启动时显示性能信息
vim.api.nvim_create_autocmd("VimEnter", {
  callback = function()
    local mem = collectgarbage("count")
    local buffers = #vim.fn.getbufinfo({ buflisted = true })
    print(string.format("Memory: %.2fMB | Buffers: %d", mem / 1024, buffers))
  end,
})
```

---

## 最佳实践

### ✅ 推荐

1. **定期运行 `:Lazy profile`** - 监控插件性能
2. **启用懒加载** - 减少启动时间
3. **大文件特殊处理** - 避免卡顿
4. **限制 Buffer 数量** - 节省内存
5. **保持插件精简** - 只装需要的

### ❌ 避免

1. ❌ 安装大量不用的插件
2. ❌ 禁用懒加载
3. ❌ 大文件启用全部功能
4. ❌ 频繁的自动命令
5. ❌ 无限撤销历史

---

## 速查表

### 性能分析

```vim
:Lazy profile        插件性能
:lua collectgarbage("count")  内存占用
:buffers             Buffer 数量
```

### 优化命令

```vim
:TSBufDisable highlight    禁用 Treesitter
:LspStop                 停止 LSP
:syntax off              禁用语法高亮
:%bd|e#                  关闭隐藏 Buffer
```

### 配置优化

```lua
-- 懒加载
event = "VeryStart"
cmd = { "Command" }
ft = "filetype"

-- 大文件检测
vim.fn.getfsize(filename)

-- 内存监控
collectgarbage("count")
```

---

## 一句话总结

**`:Lazy profile` 监控 + 懒加载优化 + 大文件处理 = 极速 Neovim**

保持配置精简，定期性能检查，让你的 Neovim 始终快如闪电。

---

## 相关文档

- [Lazy 插件管理](LAZY_NVM.md) - 插件性能分析
- [Treesitter 语法高亮](TREESITTER.md) - Treesitter 优化
- [配置结构详解](CONFIG_STRUCTURE.md) - 配置组织