# Treesitter 语法高亮完全指南

## 什么是 Treesitter？

Treesitter 是 Neovim 的现代语法分析引擎，相比传统正则高亮：

- 🎨 **更准确** - 基于 AST（抽象语法树），理解代码结构
- ⚡ **更快** - 增量解析，性能优秀
- 🧩 **更智能** - 支持代码折叠、文本对象、缩进
- 🔌 **可扩展** - 社区驱动，支持 100+ 语言

---

## Treesitter vs 传统高亮

| 特性 | 正则高亮 | Treesitter |
|------|---------|-----------|
| 原理 | 正则表达式匹配 | AST 语法树解析 |
| 准确性 | 易出错（注释/字符串误判） | 极高（理解代码结构） |
| 性能 | 大文件慢 | 增量解析，快速 |
| 功能 | 仅高亮 | 高亮+缩进+文本对象+折叠 |
| 配置 | 简单 | 需要编译语法 |

**结论**：Treesitter 是未来，强烈推荐启用。

---

## 你的配置

配置位置：`~/.config/nvim/lua/plugins/init.lua`

```lua
{
  "nvim-treesitter/nvim-treesitter",
  branch = "master",  -- 关键：锁定 master 分支
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
}
```

### ⚠️ 重要：分支锁定

**为什么用 `master` 而不是 `main`？**

- Treesitter 从 `main` 分支移除了旧 API（`nvim-treesitter.configs`）
- NvChad v2.5 仍使用旧 API
- 锁定 `master` 分支避免兼容性问题

---

## 已安装的语法

你的配置支持 **16 种语言**：

| 语言 | 用途 | 状态 |
|------|------|------|
| vim | Vim 脚本 | ✅ |
| lua | Lua 配置 | ✅ |
| vimdoc | Vim 帮助文档 | ✅ |
| html | HTML 模板 | ✅ |
| css | CSS 样式 | ✅ |
| typescript | TypeScript | ✅ |
| javascript | JavaScript | ✅ |
| tsx | React JSX | ✅ |
| json | JSON 数据 | ✅ |
| python | Python | ✅ |
| rust | Rust | ✅ |
| java | Java | ✅ |
| toml | TOML 配置 | ✅ |
| yaml | YAML 配置 | ✅ |
| markdown | Markdown 文档 | ✅ |
| vue | Vue 单文件组件 | ✅ |

---

## 查看语法状态

### 查看已安装的语法

```vim
:TSInstallInfo
```

输出示例：
```
vim              [✓] installed
lua              [✓] installed
typescript       [✓] installed
python           [✗] not installed
```

### 查看当前文件的语法

```vim
:echo &filetype
:TSHighlightCapturesUnderCursor
```

显示光标处的语法捕获组。

---

## 安装与管理语法

### 安装语法

```vim
" 安装单个
:TSInstall typescript

" 安装多个
:TSInstall typescript javascript vue

" 安装所有（在配置中声明的）
:TSInstallAll
```

### 更新语法

```vim
" 更新所有
:TSUpdate

" 更新单个
:TSUpdate typescript
```

### 卸载语法

```vim
:TSUninstall typescript
```

### 查看语法状态

```vim
:TSInstallInfo
```

---

## Treesitter 功能

### 1. 语法高亮

已启用：
```lua
highlight = { enable = true }
```

**效果**：打开代码文件时，自动使用 Treesitter 高亮。

### 2. 智能缩进

已启用：
```lua
indent = { enable = true }
```

**效果**：
- 按 `=` 自动缩进时，使用 Treesitter 理解代码结构
- 比传统缩进更准确

### 3. 文本对象

Treesitter 提供强大的文本对象（配合 `d`/`c`/`y`/`v`）：

| 对象 | 说明 | 示例 |
|------|------|------|
| `@function.outer` | 整个函数 | `da@function.outer` |
| `@function.inner` | 函数体（不含签名） | `di@function.inner` |
| `@class.outer` | 整个类 | `da@class.outer` |
| `@block.outer` | 代码块 | `da@block.outer` |
| `@loop.outer` | 循环语句 | `da@loop.outer` |
| `@conditional.outer` | 条件语句 | `da@conditional.outer` |
| `@parameter.inner` | 函数参数 | `di@parameter.inner` |
| `@comment.outer` | 注释 | `da@comment.outer` |

**示例**：

```javascript
// 光标在函数内
function greet(name) {
  console.log(`Hello, ${name}!`);
}

da@function.outer    " 删除整个函数
di@function.inner    " 删除函数体（保留签名）
va@function.outer    " 选中整个函数
```

### 4. 代码折叠

```vim
" 基于 Treesitter 折叠
:set foldmethod=expr
:set foldexpr=nvim_treesitter#foldexpr()

" 操作
zc    " 折叠
zo    " 展开
za    " 切换
zM    " 折叠所有
zR    " 展开所有
```

### 5. 增量选择

```vim
" 逐步扩大选中范围
<CR>    " 扩大（在可视模式下）
<BS>    " 缩小

" 示例：
viw         " 选中单词
<CR>        " 扩大到句子
<CR>        " 扩大到段落
<CR>        " 扩大到函数
```

---

## 配置 Treesitter

### 完整配置示例

```lua
{
  "nvim-treesitter/nvim-treesitter",
  branch = "master",
  build = ":TSUpdate",
  opts = {
    -- 确保安装的语法
    ensure_installed = {
      "vim", "lua", "vimdoc",
      "html", "css", "json", "yaml", "toml",
      "typescript", "javascript", "tsx",
      "python", "rust", "java",
      "markdown", "vue",
    },

    -- 同步安装（启动时）
    sync_install = false,

    -- 自动识别文件类型
    auto_install = true,

    -- 高亮
    highlight = {
      enable = true,
      -- 大文件禁用高亮
      disable = function(lang, buf)
        local max_filesize = 100 * 1024  -- 100KB
        local ok, stats = pcall(vim.loop.fs_stat, vim.api.nvim_buf_get_name(buf))
        if ok and stats and stats.size > max_filesize then
          return true
        end
      end,
    },

    -- 缩进
    indent = { enable = true },

    -- 文本对象
    incremental_selection = {
      enable = true,
      keymaps = {
        init_selection = "<CR>",
        node_incremental = "<CR>",
        scope_incremental = false,
        node_decremental = "<BS>",
      },
    },

    -- 代码折叠
    fold = { enable = true },
  },
}
```

---

## 常见问题排查

### 问题 1：`module 'nvim-treesitter.configs' not found`

**原因**：使用了 `main` 分支（移除了旧 API）

**解决**：

```lua
{
  "nvim-treesitter/nvim-treesitter",
  branch = "master",  -- 关键：锁定 master
}
```

### 问题 2：语法高亮不工作

**排查**：

```vim
" 1. 检查 Treesitter 是否安装
:TSInstallInfo

" 2. 检查当前文件类型
:set ft?

" 3. 检查语法是否安装
:TSInstallInfo

" 4. 手动安装
:TSInstall <语言>

" 5. 重新编译
:TSUpdate
```

### 问题 3：高亮颜色异常

**解决**：

```vim
" 清除缓存
:TSBufDisable highlight
:TSBufEnable highlight

" 或重启 Neovim
```

### 问题 4：大文件卡顿

**优化**：

```lua
-- 大文件禁用 Treesitter
highlight = {
  enable = true,
  disable = function(lang, buf)
    local max_filesize = 100 * 1024  -- 100KB
    local ok, stats = pcall(vim.loop.fs_stat, vim.api.nvim_buf_get_name(buf))
    if ok and stats and stats.size > max_filesize then
      return true
    end
  end,
}
```

### 问题 5：语法编译失败

**解决**：

```bash
# 清理编译缓存
rm -rf ~/.local/share/nvim/lazy/nvim-treesitter/parser/

# 重新启动 Neovim
nvim

# 重新安装语法
:TSUpdate
```

---

## Treesitter 文本对象详解

### 函数操作

```javascript
function test(a, b) {
  const result = a + b;
  return result;
}
```

```vim
" 光标在函数内
da@function.outer    " 删除整个函数
di@function.inner    " 删除函数体
ya@function.outer    " 复制整个函数
ca@function.outer    " 修改整个函数
```

### 类操作

```python
class MyClass:
    def __init__(self):
        self.value = 0

    def get_value(self):
        return self.value
```

```vim
" 光标在类内
da@class.outer     " 删除整个类
di@class.inner     " 删除类内容
va@class.outer     " 选中整个类
```

### 条件语句操作

```javascript
if (condition) {
  doSomething();
} else {
  doAnotherThing();
}
```

```vim
" 光标在 if 语句内
da@conditional.outer    " 删除整个条件语句
di@conditional.inner    " 删除条件体
```

### 循环语句操作

```python
for i in range(10):
    print(i)
    if i == 5:
        break
```

```vim
" 光标在循环内
da@loop.outer    " 删除整个循环
di@loop.inner    " 删除循环体
```

---

## 性能优化

### 1. 按需启用语法

```lua
highlight = {
  enable = true,
  -- 只为特定语言启用
  disable = { "java" },  -- 禁用 Java 高亮
}
```

### 2. 增量解析

Treesitter 默认启用增量解析（只解析修改的部分），无需额外配置。

### 3. 查询缓存

```vim
" 清除查询缓存
:lua vim.treesitter.query.clear_cache()
```

### 4. 查看性能

```vim
" 查看解析时间
:lua vim.treesitter.inspect()
```

---

## Treesitter 与 LSP 协同

### 区别与配合

| 功能 | Treesitter | LSP |
|------|-----------|-----|
| 高亮 | ✅ 基于 AST | ❌ |
| 补全 | ❌ | ✅ |
| 跳转定义 | ❌ | ✅ |
| 查找引用 | ❌ | ✅ |
| 诊断 | ❌ | ✅ |
| 缩进 | ✅ | ❌ |
| 文本对象 | ✅ | ❌ |
| 折叠 | ✅ | ❌ |

**结论**：两者都需要，互相补充。

---

## 最佳实践

### ✅ 推荐

1. **启用 Treesitter 高亮** - 比正则高亮更准确
2. **启用智能缩进** - 缩进更准确
3. **学习 Treesitter 文本对象** - 编辑效率翻倍
4. **大文件禁用高亮** - 避免性能问题
5. **定期更新语法** - `:TSUpdate`

### ❌ 避免

1. ❌ 使用 `main` 分支（API 不兼容）
2. ❌ 安装不需要的语法（浪费空间）
3. ❌ 大文件启用 Treesitter（卡顿）
4. ❌ 忽略编译错误（高亮异常）

---

## 速查表

### 命令

```vim
:TSInstallInfo         查看语法状态
:TSInstall <语言>      安装语法
:TSUpdate              更新所有语法
:TSUpdate <语言>       更新单个语法
:TSUninstall <语言>    卸载语法
:TSHighlightCapturesUnderCursor  查看光标处语法
```

### 文本对象

```vim
@function.outer     整个函数
@function.inner     函数体
@class.outer        整个类
@block.outer        代码块
@loop.outer         循环
@conditional.outer  条件语句
@comment.outer      注释
@parameter.inner    参数
```

### 开关

```vim
:TSBufDisable highlight    禁用当前 buffer 高亮
:TSBufEnable highlight     启用当前 buffer 高亮
```

---

## 一句话总结

**Treesitter = 准确高亮 + 智能缩进 + 强大文本对象 + 代码折叠**

学会 `@function` / `@class` / `@block` 文本对象，你的编辑效率会提升一个档次。

---

## 相关文档

- [高效编辑技巧](EDITING_TIPS.md) - 文本对象基础
- [LSP 代码智能](LSP_GUIDE.md) - LSP 与 Treesitter 协同
- [性能优化](PERFORMANCE.md) - Treesitter 性能调优