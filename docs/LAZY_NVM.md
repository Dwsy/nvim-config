# Lazy.nvim 插件管理完全指南

## 什么是 Lazy.nvim？

Lazy.nvim 是现代 Neovim 最流行的插件管理器，特点：

- ⚡ **极速启动** - 懒加载，只在使用时加载
- 🎨 **美观 UI** - 内置插件管理界面
- 🔧 **配置简单** - Lua 表格配置
- 📦 **依赖管理** - 自动处理插件依赖

---

## 核心概念

### 插件配置结构

你的配置在 `~/.config/nvim/lua/plugins/init.lua`：

```lua
return {
  -- 示例：简单插件
  {
    "作者/仓库名",              -- 必需
    event = "BufReadPre",      -- 触发时机（懒加载）
    opts = { ... },            -- 配置选项
    config = function()        -- 自定义配置函数
      require("插件").setup({...})
    end,
    dependencies = { ... },    -- 依赖的其他插件
  },
}
```

### 加载时机

| 触发器 | 含义 | 例子 |
|--------|------|------|
| `event` | 事件触发 | `BufReadPre`, `VeryStart` |
| `cmd` | 命令触发 | `Telescope`, `Mason` |
| `ft` | 文件类型 | `java`, `python` |
| `keys` | 快捷键 | `<leader>ff` |
| `lazy = false` | 启动时立即加载 | 核心插件 |

---

## Lazy 界面使用

### 打开 Lazy 管理界面

```vim
:Lazy
```

或（如果配置了快捷键）：
```vim
Space + l
```

### Lazy 界面操作

| 快捷键 | 功能 |
|--------|------|
| `i` | 安装插件 |
| `u` | 更新所有插件 |
| `U` | 更新单个插件（光标处） |
| `x` | 删除插件 |
| `H` | 查看插件主页 |
| `D` | 查看插件文档 |
| `L` | 查看日志 |
| `P` | 性能分析（profile） |
| `C` | 清理缓存 |
| `q` | 关闭 |

---

## 插件管理操作

### 查看所有插件状态

```vim
:Lazy
```

显示：
- ✅ 已加载（绿色）
- 🔄 未加载（灰色）
- ⚠️ 有问题（黄色）
- ❌ 错误（红色）

### 更新插件

```vim
" 更新所有
:Lazy update

" 更新指定插件
:Lazy update conform.nvim
```

### 安装插件

```vim
" 安装所有（通常在启动时自动）
:Lazy install

" 安装单个
:Lazy install telescope.nvim
```

### 删除插件

```vim
" 方法 1：在 plugins/init.lua 中删除对应配置
" 方法 2：在 Lazy 界面光标放在插件上按 x
:Lazy
x

" 清理未使用的插件
:Lazy clean
```

### 同步插件（安装+更新+清理）

```vim
:Lazy sync
```

---

## 性能分析

### 查看插件加载时间

```vim
:Lazy profile
```

输出示例：
```
Plugin              Time        %
nvim-treesitter     45.2ms     35%
conform.nvim        12.1ms      9%
telescope.nvim       8.5ms      7%
```

**优化建议**：
- 超过 50ms 的插件考虑懒加载
- 使用 `event` / `cmd` / `ft` 延迟加载
- 减少启动时加载的插件数量

### 查看插件加载原因

```vim
:Lazy debug
```

显示为什么某个插件被加载。

---

## 常见插件配置示例

### 1. 简单配置（opts 自动调用 setup）

```lua
{
  "nvim-tree/nvim-tree.lua",
  opts = {
    renderer = {
      group_empty = true,
    },
  },
}
```

### 2. 自定义配置函数

```lua
{
  "neovim/nvim-lspconfig",
  config = function()
    require "configs.lspconfig"
  end,
}
```

### 3. 懒加载（事件触发）

```lua
{
  "nvim-telescope/telescope.nvim",
  event = "VeryStart",
  dependencies = { "nvim-lua/plenary.nvim" },
  config = function()
    require("telescope").setup({...})
  end,
}
```

### 4. 按需加载（命令触发）

```lua
{
  "williamboman/mason.nvim",
  cmd = { "Mason", "MasonInstall", "MasonInstallAll" },
  opts = {
    ensure_installed = { ... },
  },
}
```

### 5. 文件类型触发

```lua
{
  "mfussenegger/nvim-jdtls",
  ft = "java",  -- 只在打开 Java 文件时加载
  config = function()
    -- Java LSP 配置
  end,
}
```

### 6. 快捷键触发

```lua
{
  "folke/which-key.nvim",
  keys = { "<leader>" },
  config = function()
    require("which-key").setup({...})
  end,
}
```

---

## 添加新插件（完整流程）

### 步骤 1：编辑插件配置

```bash
nvim ~/.config/nvim/lua/plugins/init.lua
```

### 步骤 2：添加插件配置

```lua
return {
  -- 现有插件...

  -- 新增：GitHub 集成
  {
    "pwntester/octo.nvim",
    cmd = "Octo",
    opts = {
      enable_builtin = true,
    },
  },
}
```

### 步骤 3：保存并安装

```vim
:w          " 保存文件
:Lazy sync  " 同步插件
```

### 步骤 4：验证安装

```vim
:Octo         " 测试命令是否可用
:Lazy         " 查看插件状态（应为绿色 ✅）
```

---

## 删除插件（完整流程）

### 方法 1：删除配置（推荐）

```lua
-- 在 plugins/init.lua 中删除或注释掉
-- {
--   "作者/不需要的插件",
--   ...
-- },
```

然后：
```vim
:w
:Lazy clean   " 清理未使用的插件
```

### 方法 2：Lazy 界面删除

```vim
:Lazy
光标移到目标插件
x             " 删除
y             " 确认
```

---

## 更新策略

### 自动更新（推荐）

```lua
-- lua/configs/lazy.lua
return {
  defaults = { lazy = true },
  install = { colorscheme = { "nvchad" } },
  -- ...
}
```

### 手动更新

```vim
:Lazy update
```

### 锁定版本（防止更新破坏）

```lua
{
  "nvim-treesitter/nvim-treesitter",
  branch = "master",     -- 锁定分支
  commit = "abc123",     -- 或锁定具体版本
}
```

### 查看更新日志

```vim
:Lazy log
```

显示每个插件的 git 提交历史。

---

## 插件依赖管理

### 自动处理依赖

```lua
{
  "nvim-telescope/telescope.nvim",
  dependencies = {
    "nvim-lua/plenary.nvim",        -- 自动安装
    "nvim-tree/nvim-web-devicons",  -- 自动安装
  },
}
```

### 查看依赖树

```vim
:Lazy
D             " 查看依赖关系
```

---

## 常见问题排查

### 问题 1：插件未加载

**排查**：

```vim
:Lazy           " 查看状态（颜色含义）
:Lazy debug     " 查看加载原因
```

**常见原因**：
- `event` / `cmd` / `ft` 条件未满足
- 插件有错误（查看 `:messages`）
- 依赖未安装

### 问题 2：插件加载太慢

**排查**：

```vim
:Lazy profile
```

**优化**：
- 改用 `event` 延迟加载
- 检查是否有不必要的 `lazy = false`
- 减少 `config` 函数的复杂度

### 问题 3：更新后插件破坏

**解决**：

```vim
" 方法 1：回退到上次版本
:Lazy restore

" 方法 2：锁定到已知可用的 commit
{
  "作者/插件",
  commit = "abc123",
}

" 方法 3：清理并重新安装
:Lazy clean
:Lazy sync
```

### 问题 4：依赖冲突

**排查**：

```vim
:Lazy
H             " 查看插件依赖
```

**解决**：
- 在父插件中声明 `dependencies`
- 删除重复的依赖声明

---

## 高级配置

### 1. 自定义源（镜像加速）

```lua
-- lua/configs/lazy.lua
return {
  git = {
    url_format = "https://hub.fastgit.xyz/%s.git",  -- 国内镜像
  },
  -- ...
}
```

### 2. 并行安装

```lua
{
  install = {
    missing = true,
    colorscheme = { "nvchad" },
  },
  performance = {
    concurrency = 4,  -- 并行下载数
  },
}
```

### 3. 禁用插件

```lua
{
  "some/plugin.nvim",
  enabled = false,  -- 临时禁用
}
```

### 4. 覆盖 NvChad 默认插件

```lua
{
  "NvChad/ui",
  override = {
    ["nvim-tree/nvim-tree.lua"] = {
      -- 你的覆盖配置
    },
  },
}
```

---

## 插件开发入门

### 创建自己的插件

```lua
-- lua/plugins/my-plugin.lua
return {
  "你的名字/my-plugin.nvim",
  config = function()
    require("my-plugin").setup({
      -- 你的配置
    })
  end,
}
```

### 测试本地插件

```lua
{
  dir = "/path/to/local/plugin",  -- 使用本地路径
  config = function()
    require("local-plugin").setup({...})
  end,
}
```

---

## 速查表

### 命令

```vim
:Lazy               打开管理界面
:Lazy update        更新所有
:Lazy install       安装所有
:Lazy clean         清理未使用
:Lazy sync          同步（安装+更新+清理）
:Lazy restore       回退到锁定版本
:Lazy profile       性能分析
:Lazy log           查看更新日志
:Lazy debug         查看加载原因
```

### 快捷键（在 Lazy 界面内）

```vim
i       安装
u       更新所有
U       更新单个
x       删除
H       主页
D       文档
L       日志
P       性能分析
C       清理缓存
q       关闭
```

---

## 最佳实践

### ✅ 推荐

1. **使用懒加载** - 用 `event` / `cmd` / `ft` 延迟
2. **定期更新** - 每周 `:Lazy update`
3. **锁定关键插件** - 用 `branch` 或 `commit` 锁定
4. **监控性能** - 定期 `:Lazy profile`
5. **保持简洁** - 只安装需要的的插件

### ❌ 避免

1. ❌ 所有插件都 `lazy = false`
2. ❌ 安装大量不用的插件
3. ❌ 不更新（积累太多更新）
4. ❌ 不看日志就盲目修改

---

## 一句话总结

**`:Lazy` 打开界面 → `u` 更新 → `P` 看性能 → `profile` 找瓶颈 = 插件管理全流程**

掌握 Lazy 的懒加载机制，你的 Neovim 启动速度会快如闪电。

---

## 相关文档

- [配置结构详解](CONFIG_STRUCTURE.md) - 了解配置组织方式
- [性能优化](PERFORMANCE.md) - 优化启动速度
- [Mason 工具管理](MASON.md) - LSP 和格式化工具管理