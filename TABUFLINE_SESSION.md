# Nvim Tabufline 与 Session 管理指南

## 第一部分：Tabufline 多行显示问题

### 核心结论

❌ **NvChad 的 tabufline 目前不支持多行显示**

这不是你没配对，不是隐藏选项，而是 **设计上就是单行**。

---

## 为什么 Tabufline 不能多行？

### 1️⃣ 技术原因（Neovim 限制）

tabufline 本质是 statusline / winbar 的变体

**Neovim 原生 UI 不支持多行 bufferline**

多行会严重影响：
- 窗口高度计算
- 滚动
- 分屏一致性

### 2️⃣ NvChad 的设计取向

**NvChad 的理念：**

```
"tabufline 只是提示，不是主要导航工具"
```

**Buffer 多了的官方态度：**
- 不鼓励你一直堆 buffer
- 推荐用 Telescope 快速切换
- 推荐用场景化 Tab 分组

---

## 官方推荐方案（按优先级）

### 🥇 方案一：用 Telescope 管 Buffer（强烈推荐）

```vim
Space + fb    " 列出所有已打开 buffer
```

**功能：**
- 列出所有已打开 buffer
- 模糊搜索
- `Enter` 跳转
- `dd` 可关闭

👉 这比多行 tabufline 高效得多。

### 🥈 方案二：限制 Tabufline 数量（更 Vim）

快速关掉不用的 buffer：

```vim
Space + x     " 关闭当前 buffer
```

或：

```vim
:bd           " 关闭 buffer
```

### 🥉 方案三：用 Tab 做"分组"（推荐）

**核心思想：**

| 层级 | 用途 |
|------|------|
| Tab | 场景 / 工作区 |
| Buffer | 文件 |

**例子：**

```
Tab 1：前端
  └─ App.vue, main.ts, config.ts

Tab 2：后端
  └─ Main.java, Service.java, Util.java

Tab 3：文档
  └─ README.md, CHANGELOG.md
```

这样每个 tab 里 buffer 数量自然少，tabufline 就不会挤。

---

## 不得已的替代方案（不推荐给新手）

如果你强烈想要多行，可以换插件：

### 替代插件

```lua
-- bufferline.nvim
-- barbar.nvim
```

⚠️ **代价：**
- 和 NvChad 风格冲突
- 维护成本高
- 容易被升级打断

### 或者直接关闭 Tabufline

```lua
-- lua/chadrc.lua
M.ui = {
  tabufline = {
    enabled = false,
  },
}
```

然后全部用：
- `Space + ff` 查找文件
- `Space + fb` 切换 buffer
- `Telescope` 做所有导航

👉 很多老 Vim 用户就是这样用。

---

## 给你的"老手建议"

**不要追求"看得见全部"**

👉 Vim 的强项是 **"跳得快"**

你已经有了：

| 工具 | 作用 |
|------|------|
| `gd` | 跳到定义 |
| `Ctrl+o` / `Ctrl+i` | 前进后退 |
| `Telescope` | 模糊搜索 |
| `LSP` | 代码智能 |

这些组合起来，**远比多行 tab 强**。

---

---

## 第二部分：保留状态（Session 管理）

### 核心问题

> 我文件太多，我不想每次重新打开

**答案：使用 Neovim 的 Session（会话）**

---

## 你想要的"保留状态"分 3 个层级

| 层级 | 你想保留什么 | 对应方案 |
|------|-------------|---------|
| 文件 | 上次打开过哪些文件 | Buffer / oldfiles |
| 位置 | 光标在哪一行 | viminfo（自动） |
| 布局 | Tab / Window / Buffer 结构 | **Session**（重点） |

---

## 🥇 最推荐方案：使用 NvChad 自带 Session

### 1️⃣ 保存当前状态

在 nvim 里执行：

```vim
:SessionSave
```

**它会记住：**
- 所有打开的 buffer
- Tab / window 布局
- 当前目录

### 2️⃣ 下次恢复状态

在同一目录下：

```bash
nvim
```

然后：

```vim
:SessionLoad
```

🎉 所有文件、tab、布局原样回来。

### 3️⃣ 查看已有 Session

```vim
:SessionList
```

---

## 使用建议（非常重要）

### 正确姿势：一个项目 = 一个 session

```bash
cd my-project
nvim
```

打开你要的文件，布好 tab / buffer，然后：

```vim
:SessionSave
```

**下次：**

```bash
cd my-project
nvim
:SessionLoad
```

✅ 完全恢复上次的工作状态。

---

## 快速速查表

| 命令 | 功能 |
|------|------|
| `:SessionSave` | 保存当前状态 |
| `:SessionLoad` | 恢复状态 |
| `:SessionList` | 查看已有 session |
| `Space + fo` | 最近文件 |

---

## 补充：文件级别的"记忆"（已自动）

### 🔹 最近打开过的文件

```vim
:oldfiles     " 列出最近打开的文件
```

或：

```vim
Space + fo    " NvChad 快捷键
```

### 🔹 光标位置自动恢复

Neovim 的默认行为：

**重新打开文件 → 光标自动回到上次位置**

这是内置功能，不用配，自动工作。

---

## ❌ 不推荐的方式

❌ **一直开一堆 buffer 不关**

问题：
- 内存占用
- Tabufline 挤
- 心智负担大

👉 **Session 才是正解**

---

## 老 Vim 用户的真实工作流

每天只做 3 件事：

```bash
1. cd my-project && nvim
2. :SessionLoad              # 恢复上次状态
3. 开始干活
```

**下班前：**

```vim
:SessionSave
```

---

## 一句话总结（非常重要）

🔥 **不要试图"每次都重新打开文件"**

👉 **Neovim 是为"长期会话"设计的**

👉 **Session 就是答案**

---

## 进阶：自动保存 Session（可选）

如果你想完全自动化，可以在启动时自动加载上次的 session。

编辑 `~/.config/nvim/init.lua`，添加：

```lua
-- 自动加载上次 session（可选）
if vim.fn.argc() == 0 then
  vim.cmd("SessionLoad")
end
```

这样每次启动都自动恢复上次的状态。

---

## 完整工作流总结

| 场景 | 快捷键 / 命令 |
|------|---------------|
| 开始工作 | `cd project && nvim` → `:SessionLoad` |
| 快速切 buffer | `Space + fb` |
| 快速找文件 | `Space + ff` |
| 跳到定义 | `gd` |
| 前进后退 | `Ctrl+i` / `Ctrl+o` |
| 保存工作区 | `:SessionSave` |
| 看最近文件 | `Space + fo` |

---

## 快速对比：多个方案的取舍

| 方案 | 优点 | 缺点 | 推荐度 |
|------|------|------|--------|
| **Telescope + Session** | 快速、自动、清爽 | 需要记几个快捷键 | ⭐⭐⭐⭐⭐ |
| **多行 Tabufline** | 视觉清晰 | 不可行（不支持） | ❌ |
| **Tab 分组** | 场景清晰、结构好 | 需要规划 | ⭐⭐⭐⭐ |
| **一堆 Buffer** | 什么都不用改 | 乱、慢、占内存 | ❌ |

---

## 你现在掌握的完整导航系统

✅ **跳转系统**
- `gd` / `gr` 快速导航
- `Ctrl+o` / `Ctrl+i` 前进后退

✅ **文件管理**
- `Space + fb` 快速切 buffer
- `Space + ff` 搜索打开文件
- `Space + fo` 最近文件

✅ **状态保留**
- `:SessionSave` / `:SessionLoad` 保存工作区
- viminfo 自动记光标位置

✅ **场景分组**
- Tab 分"场景"
- 每个 Tab 用 buffer 管文件

👉 **你已经拥有比 VS Code 强得多的文件管理能力了！**
