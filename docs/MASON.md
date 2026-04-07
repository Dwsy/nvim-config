# Mason 工具管理完全指南

## 什么是 Mason？

Mason 是 Neovim 的 LSP、DAP、Linter、Formatter 包管理器：

- 📦 **一键安装** - 自动下载和配置工具
- 🎯 **统一管理** - 所有工具在一个界面
- ⚡ **开箱即用** - 与 LSP/Conform 无缝集成
- 🔄 **自动更新** - 轻松保持工具最新

---

## Mason vs 系统安装

| 方式 | 优点 | 缺点 |
|------|------|------|
| **Mason** | 自动配置 PATH，与 Neovim 集成 | 仅 Neovim 使用 |
| **系统安装** | 全局可用 | 手动配置，版本管理复杂 |

**推荐**：Neovim 相关工具用 Mason，其他用系统包管理器。

---

## 你的配置

配置位置：`~/.config/nvim/lua/plugins/init.lua`

```lua
{
  "williamboman/mason.nvim",
  opts = {
    ensure_installed = {
      "typescript-language-server",    -- TypeScript LSP
      "vue-language-server",           -- Vue LSP
      "pyright",                       -- Python LSP
      "rust-analyzer",                 -- Rust LSP
      "jdtls",                         -- Java LSP
      "lua-language-server",           -- Lua LSP
      "html-lsp",                      -- HTML LSP
      "css-lsp",                       -- CSS LSP
      "prettier",                      -- 格式化
      "stylua",                        -- Lua 格式化
      "ruff",                          -- Python 格式化/Lint
    },
  },
}
```

---

## Mason 界面使用

### 打开 Mason

```vim
:Mason
```

或（如果配置了快捷键）：
```vim
Space + sm
```

### Mason 界面操作

| 快捷键 | 功能 |
|--------|------|
| `i` | 安装光标处的工具 |
| `U` | 更新所有已安装工具 |
| `X` | 删除光标处的工具 |
| `C` | 查看安装日志 |
| `L` | 查看日志 |
| `H` | 查看工具主页 |
| `q` | 关闭 |

### 界面说明

```
Registry packages (可安装)
    ↓ 按 i 安装
Installed packages (已安装)
    ↓ 按 U 更新
```

---

## 工具分类

### LSP 服务器

| 工具名称 | 语言 | 功能 |
|---------|------|------|
| `ts_ls` | TypeScript/JS | 代码智能 |
| `vue-language-server` | Vue | 模板+脚本 |
| `pyright` | Python | 类型检查 |
| `rust-analyzer` | Rust | 全功能 |
| `jdtls` | Java | Lombok 支持 |
| `lua_ls` | Lua | Neovim API |
| `html` | HTML | 标签补全 |
| `cssls` | CSS | 样式补全 |

### 格式化工具

| 工具名称 | 语言 | 功能 |
|---------|------|------|
| `prettier` | JS/TS/Vue/HTML/CSS | 代码格式化 |
| `stylua` | Lua | 代码格式化 |
| `ruff` | Python | 格式化+Lint |
| `rustfmt` | Rust | 代码格式化（系统安装） |

### Linter（未安装）

| 工具名称 | 语言 | 功能 |
|---------|------|------|
| `eslint` | JS/TS | 代码检查 |
| `flake8` | Python | 代码检查 |
| `clippy` | Rust | 代码检查（系统安装） |

---

## 安装与管理工具

### 安装工具

```vim
" 方法 1：Mason 界面
:Mason
光标移到工具上
i           " 安装

" 方法 2：命令安装
:MasonInstall prettier
:MasonInstall stylua ruff

" 方法 3：配置中声明（自动安装）
"Mason 启动时自动安装 ensure_installed 中的工具
```

### 更新工具

```vim
" 更新所有
:MasonUpdate

" 更新单个
:MasonUpdate prettier
```

### 删除工具

```vim
" 方法 1：Mason 界面
:Mason
光标移到工具上
X           " 删除

" 方法 2：命令删除
:MasonUninstall prettier
```

### 查看工具状态

```vim
:Mason
```

显示：
- ✅ 已安装（绿色）
- ⬇️ 可安装（灰色）
- 🔄 更新可用（黄色）
- ❌ 错误（红色）

---

## 查看工具信息

### 查看已安装工具

```vim
:Mason
```

### 查看工具详情

```vim
"Mason 界面中
Enter       " 查看工具信息
```

### 查看工具路径

```vim
" 默认安装位置
~/.local/share/nvim/mason/packages/

" 查看具体工具
ls ~/.local/share/nvim/mason/packages/prettier
```

### 查看工具版本

```vim
" 在终端执行
~/.local/share/nvim/mason/packages/prettier/bin/prettier.cjs --version
```

---

## 工具配置

### LSP 服务器配置

配置位置：`~/.config/nvim/lua/configs/lspconfig.lua`

```lua
local servers = {
  "html",
  "cssls",
  "ts_ls",
  "pyright",
  "rust_analyzer",
  "lua_ls",
}

vim.lsp.enable(servers)
```

### 格式化工具配置

配置位置：`~/.config/nvim/lua/configs/conform.lua`

```lua
formatters_by_ft = {
  lua = { "stylua" },
  javascript = { "prettier" },
  typescript = { "prettier" },
  python = { "ruff" },
}
```

---

## 安装问题排查

### 问题 1：工具安装失败

**排查**：

```vim
"Mason 日志
:MasonLog
```

**常见原因**：
- 网络问题（设置代理）
- 磁盘空间不足
- 权限问题

**解决**：

```vim
" 重试安装
:MasonInstall prettier

" 或清理缓存后重新安装
:MasonCache clean
```

### 问题 2：工具安装后找不到

**检查 PATH**：

```vim
" Mason bin 目录
:lua print(vim.fn.stdpath("data") .. "/mason/bin")

" 检查工具是否存在
ls ~/.local/share/nvim/mason/packages/
```

**解决**：

```lua
" 确保 Mason 在 lspconfig 之前加载
" plugins/init.lua 中顺序：
{
  "williamboman/mason.nvim",
  -- ...
},
{
  "neovim/nvim-lspconfig",
  config = function()
    require "configs.lspconfig"
  end,
},
```

### 问题 3：工具版本过旧

**更新**：

```vim
:MasonUpdate

" 或强制重新安装
:MasonUninstall prettier
:MasonInstall prettier
```

---

## 网络加速

### 设置代理

```bash
# 在 shell 中
export https_proxy=http://127.0.0.1:7890
nvim
```

### 在 Neovim 中设置

```lua
" init.lua 或 options.lua
vim.env.https_proxy = "http://127.0.0.1:7890"
vim.env.http_proxy = "http://127.0.0.1:7890"
```

### 使用国内镜像

```lua
" Mason 配置
require("mason").setup({
  registries = {
    "github:nvim/mason-registry",
  },
})
```

---

## 自动安装

### 启动时自动安装

配置中声明：
```lua
{
  "williamboman/mason.nvim",
  opts = {
    ensure_installed = {
      "prettier",
      "stylua",
      -- ...
    },
    -- 自动安装缺失工具
    auto_install = true,
  },
}
```

### 手动触发安装

```vim
" 安装所有声明的工具
:MasonInstallAll
```

---

## 工具列表（完整）

### 你的配置中安装的

| 工具 | 类型 | 用途 |
|------|------|------|
| typescript-language-server | LSP | TypeScript/JS |
| vue-language-server | LSP | Vue |
| pyright | LSP | Python |
| rust-analyzer | LSP | Rust |
| jdtls | LSP | Java |
| lua-language-server | LSP | Lua |
| html-lsp | LSP | HTML |
| css-lsp | LSP | CSS |
| prettier | Formatter | JS/TS/Vue/HTML/CSS |
| stylua | Formatter | Lua |
| ruff | Formatter/Linter | Python |

### 推荐额外安装的

| 工具 | 类型 | 用途 |
|------|------|------|
| eslint_lsp | LSP | JS/TS 代码检查 |
| bashls | LSP | Bash/Shell |
| dockerls | LSP | Docker |
| jsonls | LSP | JSON |
| tailwindcss | LSP | Tailwind CSS |
| volar | LSP | Vue 3（已弃用，用 vue_ls） |

---

## Mason 与其他工具集成

### 与 LSP 集成

Mason 安装的 LSP 服务器可被 lspconfig 自动识别：

```lua
" 自动配置 Mason 安装的 LSP
require("mason-lspconfig").setup({
  ensure_installed = {
    "ts_ls",
    "pyright",
    -- ...
  },
  automatic_installation = true,
})
```

### 与 Conform 集成

Mason 安装的格式化工具可被 Conform 自动识别：

```lua
" Conform 会自动查找 Mason 安装的工具
formatters_by_ft = {
  lua = { "stylua" },  -- stylua 来自 Mason
}
```

---

## 命令行使用

### 查看 Mason 安装目录

```bash
ls ~/.local/share/nvim/mason/packages/
```

### 手动执行工具

```bash
# Prettier
~/.local/share/nvim/mason/packages/prettier/bin/prettier.cjs --write file.ts

# StyLua
~/.local/share/nvim/mason/bin/stylua file.lua

# Ruff
~/.local/share/nvim/mason/bin/ruff check file.py
```

---

## 最佳实践

### ✅ 推荐

1. **使用 Mason 管理所有 LSP/Formatter** - 统一版本
2. **定期更新工具** - `:MasonUpdate`
3. **在配置中声明工具** - 自动安装
4. **查看日志排查问题** - `:MasonLog`
5. **设置代理加速下载** - 国内网络

### ❌ 避免

1. ❌ 手动安装 LSP（难以管理版本）
2. ❌ 忽略更新（积累太多）
3. ❌ 不看日志就排查问题
4. ❌ 混用 Mason 和系统工具（冲突）

---

## 速查表

### 命令

```vim
:Mason                  打开管理界面
:MasonInstall <工具>    安装工具
:MasonUninstall <工具>  删除工具
:MasonUpdate            更新所有工具
:MasonLog               查看日志
:MasonCache clean       清理缓存
```

### 快捷键（Mason 界面）

```vim
i       安装
U       更新所有
X       删除
C       查看安装日志
L       查看日志
H       工具主页
q       关闭
```

---

## 一句话总结

**`:Mason` 打开界面 → `i` 安装 → `U` 更新 → `:MasonLog` 排查 = 工具管理全流程**

用 Mason 统一管理所有 LSP 和格式化工具，让你的 Neovim 始终保持最佳状态。

---

## 相关文档

- [LSP 代码智能](LSP_GUIDE.md) - LSP 服务器配置
- [代码格式化](CONFORM_FORMAT.md) - 格式化工具配置
- [Lazy 插件管理](LAZY_NVM.md) - Mason 插件配置