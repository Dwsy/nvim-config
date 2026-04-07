# Conform.nvim 代码格式化完全指南

## 什么是 Conform？

Conform.nvim 是 Neovim 的代码格式化工具，特点：

- ⚡ **极速** - 异步格式化，不阻塞编辑器
- 🔧 **多语言支持** - 统一接口管理多种格式化工具
- 💾 **保存时自动格式化** - 无需手动触发
- 🎯 **精确控制** - 支持全文件/选区/范围格式化

---

## 核心概念

### Conform 工作原理

```
保存文件 (:w)
    ↓
Conform 拦截
    ↓
根据文件类型选择格式化工具
    ↓
异步调用格式化工具
    ↓
应用格式化结果
    ↓
完成（不阻塞）
```

### 你的配置

配置位置：`~/.config/nvim/lua/configs/conform.lua`

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

## 已配置的格式化工具

| 语言 | 格式化工具 | 安装方式 | 配置 |
|------|-----------|---------|------|
| Lua | StyLua | `:MasonInstall stylua` | stylua.toml |
| JavaScript/TypeScript | Prettier | `:MasonInstall prettier` | .prettierrc |
| Vue | Prettier | `:MasonInstall prettier` | .prettierrc |
| HTML/CSS/JSON | Prettier | `:MasonInstall prettier` | .prettierrc |
| Python | Ruff | `:MasonInstall ruff` | pyproject.toml |
| Rust | Rustfmt | 系统安装 | rustfmt.toml |

---

## 格式化操作

### 手动格式化

```vim
Space + lf          " 格式化当前文件
```

或命令：
```vim
:lua require("conform").format()
```

### 格式化选区

```vim
" 可视模式
V           " 选中行
Space + lf  " 格式化选中区域

" 或命令
:'<,'>Conform
```

### 格式化范围

```lua
-- 格式化第 10-20 行
:lua require("conform").format({ range = { [1] = 10, [2] = 20 } })
```

### 保存时自动格式化

已配置（`format_on_save = true`），保存时自动格式化。

**超时设置**：`timeout_ms = 500`（500ms 超时）

**LSP 回退**：`lsp_fallback = true`（如果 Conform 失败，尝试 LSP 格式化）

---

## 检查格式化状态

### 查看格式化信息

```vim
:ConformInfo
```

显示：
- 当前文件类型
- 已配置的格式化工具
- 哪些工具已安装
- 哪些工具可用

### 查看格式化日志

```vim
:ConformLog
```

显示格式化过程中的错误信息。

---

## 配置格式化工具

### 添加新语言

编辑 `~/.config/nvim/lua/configs/conform.lua`：

```lua
formatters_by_ft = {
  -- 现有配置...

  -- 添加 Go
  go = { "goimports" },

  -- 添加 C/C++
  c = { "clang_format" },
  cpp = { "clang_format" },

  -- 添加 SQL
  sql = { "sqlfluff" },
}
```

### 配置格式化工具选项

```lua
formatters = {
  prettier = {
    -- 自定义 Prettier 选项
    args = { "--tab-width", "2", "--single-quote" },
  },
  stylua = {
    -- StyLua 选项
    args = { "--indent-type", "Spaces", "--indent-width", "2" },
  },
}
```

---

## 格式化工作流

### 场景 1：日常开发

```vim
" 1. 编辑代码
i
...编写代码...
Esc

" 2. 保存（自动格式化）
:w

" 3. 如果不满意，撤销
u

" 4. 手动重新格式化
Space + lf
```

### 场景 2：格式化多个文件

```vim
" 方法 1：逐个打开并保存
:e file1.ts
:w          " 自动格式化

:e file2.ts
:w          " 自动格式化

" 方法 2：命令行批量格式化
" 在终端执行
find src -name "*.ts" -exec nvim --headless -c "lua require('conform').format()" -c "wq" {} \;
```

### 场景 3：格式化后审查

```vim
" 1. 保存（自动格式化）
:w

" 2. 查看 Git 差异
:Gitsigns diffthis

" 3. 如果不满意，撤销
u

" 4. 重新编辑
```

### 场景 4：临时禁用自动格式化

```vim
" 关闭保存时格式化
:lua vim.g.autoformat = false

" 重新开启
:lua vim.g.autoformat = true

" 切换开关
:lua vim.g.autoformat = not vim.g.autoformat
```

---

## 格式化工具配置

### Prettier 配置

创建 `.prettierrc`（项目根目录）：

```json
{
  "semi": true,
  "singleQuote": true,
  "tabWidth": 2,
  "trailingComma": "es5",
  "printWidth": 80
}
```

### StyLua 配置

创建 `stylua.toml`（项目根目录或家目录）：

```toml
column_width = 120
line_endings = "Unix"
indent_type = "Spaces"
indent_width = 2
quote_style = "AutoPreferDouble"
call_parentheses = "None"
```

### Ruff 配置

创建 `pyproject.toml`：

```toml
[tool.ruff.format]
quote-style = "double"
indent-style = "space"
skip-magic-trailing-comma = false
line-ending = "auto"
```

### Rustfmt 配置

创建 `rustfmt.toml`：

```toml
max_width = 100
hard_tabs = false
tab_spaces = 4
reorder_imports = true
```

---

## 常见问题排查

### 问题 1：格式化不工作

**排查步骤**：

```vim
" 1. 查看 Conform 状态
:ConformInfo

" 2. 检查格式化工具是否安装
:Mason

" 3. 手动测试格式化
:lua require("conform").format()

" 4. 查看日志
:ConformLog
```

**常见原因**：
- 格式化工具未安装（`:MasonInstall <工具>`）
- 文件类型不匹配（`:set ft?` 检查）
- 配置文件有语法错误

### 问题 2：格式化结果不符合预期

**排查**：

```vim
" 1. 检查格式化工具配置
" 查看项目根目录的 .prettierrc / stylua.toml 等

" 2. 测试命令行格式化
" 在终端执行
prettier --write file.ts
stylua file.lua

" 3. 如果命令行正常，检查 Conform 配置
```

### 问题 3：格式化太慢

**优化**：

```lua
-- 增加超时时间
format_on_save = {
  timeout_ms = 1000,  -- 从 500ms 增加到 1000ms
  lsp_fallback = true,
}

-- 或禁用保存时格式化
format_on_save = false,
```

### 问题 4：部分文件未被格式化

**检查文件类型**：

```vim
:set ft?
```

**解决**：

```lua
-- 添加文件类型映射
formatters_by_ft = {
  -- TypeScript React
  typescriptreact = { "prettier" },

  -- 或者使用通配符（如果支持）
  ["*"] = { "prettier" },  -- 所有文件
}
```

---

## 多格式化工具共存

### 优先级配置

```lua
formatters_by_ft = {
  -- 多个工具时，按顺序尝试
  javascript = { "prettier", "eslint" },  -- 先 prettier，失败则 eslint
}
```

### LSP 与 Conform 协同

```lua
format_on_save = {
  timeout_ms = 500,
  lsp_fallback = true,  -- Conform 失败时使用 LSP
}
```

---

## 命令行格式化

### 使用 Conform 命令行模式

```bash
# 格式化单个文件
nvim --headless -c "lua require('conform').format({async=false})" -c "wq" file.ts

# 批量格式化
find src -name "*.ts" | xargs -I {} nvim --headless -c "lua require('conform').format({async=false})" -c "wq" {}
```

### Git Hook 集成

创建 `.husky/pre-commit`：

```bash
#!/bin/sh
# 格式化的暂存文件
npx lint-staged
```

创建 `.lintstagedrc`：

```json
{
  "*.{js,ts,vue,css,html,json}": ["prettier --write"],
  "*.lua": ["stylua"],
  "*.py": ["ruff format"]
}
```

---

## 高级配置

### 1. 自定义格式化工具

```lua
formatters = {
  my_custom_formatter = {
    command = "my-formatter",
    args = { "--stdin-filepath", "$FILENAME" },
  },
}

formatters_by_ft = {
  javascript = { "my_custom_formatter" },
}
```

### 2. 条件格式化

```lua
formatters_by_ft = function(bufnr)
  local ft = vim.bo[bufnr].filetype

  -- 根据项目使用不同工具
  if vim.fn.isdirectory(".eslint") then
    return { "eslint" }
  else
    return { "prettier" }
  end
end
```

### 3. 格式化前执行检查

```lua
format_on_save = {
  timeout_ms = 500,
  lsp_fallback = true,
  -- 格式化前询问（可选）
  -- check_exit_code = true,
}
```

---

## 最佳实践

### ✅ 推荐

1. **启用保存时格式化** - 减少手动操作
2. **配置项目级格式化工具** - 团队统一风格
3. **定期更新格式化工具** - `:MasonUpdate`
4. **使用 Git Hook** - 确保提交前格式化
5. **检查 `:ConformInfo`** - 确认工具状态

### ❌ 避免

1. ❌ 禁用自动格式化（容易忘记）
2. ❌ 混用多个格式化工具（风格冲突）
3. ❌ 不配置项目级规则（个人风格不统一）
4. ❌ 忽略格式化错误（日志不查看）

---

## 速查表

### 快捷键

```vim
Space+lf        格式化当前文件
```

### 命令

```vim
:ConformInfo    查看格式化状态
:ConformLog     查看日志
:Conform        手动格式化
```

### 开关

```lua
vim.g.autoformat = false    -- 禁用自动格式化
vim.g.autoformat = true     -- 启用
```

---

## 一句话总结

**`Space+lf` 手动格式化 + `format_on_save` 自动格式化 + `:ConformInfo` 排查 = 代码格式化全流程**

配置好格式化工具，保存时自动执行，让你的代码始终保持统一风格。

---

## 相关文档

- [Mason 工具管理](MASON.md) - 安装格式化工具
- [LSP 代码智能](LSP_GUIDE.md) - LSP 格式化回退
- [Lazy 插件管理](LAZY_NVM.md) - Conform 插件配置