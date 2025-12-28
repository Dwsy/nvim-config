# Nvim 导航与 Tabs 管理完全指南

## 第一部分：跳转历史 (Jump History)

### 核心概念：3 套历史系统

| 系统 | 快捷键 | 用途 |
|------|--------|------|
| Jump List | `Ctrl+o` / `Ctrl+i` | 光标位置跳转 |
| Change List | `g;` / `g,` | 编辑修改位置 |
| File History | `Space+fo` | 最近打开文件 |

---

## 一、最重要的两个：前进 / 后退

### ⬅️ 后退（回到上一个位置）
```vim
Ctrl + o
```

### ➡️ 前进（回到刚才的位置）
```vim
Ctrl + i
```

**记忆法：**
- `o` = older（更老的位置）
- `i` = newer（更新的位置）
- 和浏览器的 ⬅️➡️ 完全一样

---

## 二、能回退什么？

所有"光标大移动"都能用 `Ctrl+o` / `Ctrl+i` 回退：

| 操作 | 快捷键 |
|------|--------|
| 跳转定义 | `gd` |
| 查看引用 | `gr` |
| 搜索 | `/xxx` |
| 文件开头/末尾 | `gg` / `G` |
| 文件间跳转 | `:e file.js` |

---

## 三、查看跳转历史

### 方法 1️⃣：查看 Jump List（最重要）

```vim
:jumps
```

输出示例：
```
 jump line  col file/text
   1   86   10  src/Main.java
   2   34    2  src/Service.java
   3   12    5  src/Util.java
```

`Ctrl+o` / `Ctrl+i` 就是在这个列表里移动。

### 方法 2️⃣：查看修改历史（Change List）

```vim
:changes
```

对应快捷键：
```vim
g;    上一个修改点
g,    下一个修改点
```

---

## 四、文件与 Buffer 历史

### 最近打开的文件
```vim
:oldfiles
```

或（NvChad 推荐）：
```vim
Space + fo
```

### 已打开文件切换（Buffer）
```vim
Space + bb
```

在已打开文件之间来回切。

---

## 五、LSP + 导航的黄金组合

看代码 → 跳 → 回来

```vim
gd          # 跳到定义
Ctrl + o    # 回来
Ctrl + i    # 再过去
```

👉 这三个是 Vim 用户的肌肉记忆。

---

## 六、必须记住的 6 个快捷键

| 快捷键 | 功能 |
|--------|------|
| `gd` | 跳到定义 |
| `Ctrl+o` | 后退 |
| `Ctrl+i` | 前进 |
| `:jumps` | 查看跳转历史 |
| `g;` | 上一个修改点 |
| `Space+fo` | 最近文件 |

---

## 常见疑问

### Q：`Ctrl+i` 和 `Tab` 冲突？

在终端里它们是同一个键，但 Vim 内部仍然区分 jump list。放心用就行。

### Q：有没有"时间轴式撤销"？

有：`u` / `Ctrl+r`，那是内容历史，不是位置历史。

---

---

## 第二部分：Tabs 管理（3 个层级）

### 概念澄清：90% 新手会混

| 层级 | 说明 | 例子 |
|------|------|------|
| **Buffer** ✅ 最重要 | 一个文件 = 一个 buffer | README.md / Main.java |
| **Window** | 屏幕里的分屏 | `:sp` / `:vsp` |
| **Tab** | 一组 window/buffer 的工作区 | 顶部标签页列表 |

👉 你平时其实主要在"切 buffer"，不是 tab。

---

## 一、基础 Tab 操作

### 新建 Tab
```vim
:tabnew
```

打开文件到新 tab：
```vim
:tabedit README.md
```

### 关闭当前 Tab
```vim
:tabclose
:tabc          " 缩写
```

### 在 Tabs 之间切换（最常用）
```vim
gt     下一个 tab
gT     上一个 tab
```

### 跳到指定 tab（非常快）
```vim
1gt    跳到第 1 个 tab
2gt    跳到第 2 个 tab
```

### 查看所有 Tabs
```vim
:tabs
```

---

## 二、NvChad 推荐方式（更高效）

NvChad 默认鼓励用 **buffer**，而不是 tab。

| 操作 | 快捷键 |
|------|--------|
| Buffer 切换列表 | `Space + bb` |
| 关闭当前 buffer | `Space + bd` |

👉 这比 tabs 高效很多。

---

## 三、正确的使用姿势（重点）

### ❌ 不推荐：Tab 思维（VS Code）
```
一个文件一个 tab
tab 数量无限增长
```

### ✅ 推荐：Buffer 思维（Vim）
```
Tab = 场景 / 工作区
Buffer = 文件

Tab 1：前端（Vue / TS）
  - App.vue
  - main.ts
  - config.ts

Tab 2：后端（Java）
  - Main.java
  - Service.java
  - Util.java

Tab 3：文档
  - README.md
  - CHANGELOG.md
```

**在每个 Tab 里：**
- 用 `Space + bb` 切文件
- 用 `Ctrl+w` 管分屏

---

## 四、实操方案（基于你当前的截图）

### 第一步：简化 Tab 数量

只保留 2-3 个 tab：

```vim
:tabc     " 关闭不需要的 tab
```

### 第二步：在一个 Tab 里切文件

```vim
Space + bb    " 打开 buffer 列表

选择：
  FillContext.java
  FuzzyMatcher.js
  README.md
```

### 第三步：快速左右切 Tab

```vim
gt        " 下一个 tab
gT        " 上一个 tab
```

---

## 五、速查表（收藏级）

### Tabs 操作
| 命令 | 功能 |
|------|------|
| `:tabnew` | 新 tab |
| `:tabedit 文件` | 打开到新 tab |
| `:tabc` | 关 tab |
| `gt` / `gT` | 下一个 / 上一个 |
| `:number gt` | 跳转到第 N 个 tab |
| `:tabs` | 查看所有 tab |

### Buffers（更重要）
| 快捷键 | 功能 |
|--------|------|
| `Space + bb` | 切 buffer |
| `Space + bd` | 关 buffer |
| `:buffers` | 列出所有 buffer |
| `:bn` / `:bp` | 下一个 / 上一个 buffer |

### Window 分屏
| 快捷键 | 功能 |
|--------|------|
| `Ctrl+w s` | 水平分屏 |
| `Ctrl+w v` | 垂直分屏 |
| `Ctrl+w h/j/k/l` | 切换窗口 |
| `Ctrl+w =` | 均分窗口 |

---

## 六、推荐布局方案

### 场景 1：全栈开发

**Tab 1：前端**
```
┌─────────────────┐
│ App.vue      │ main.ts  │
└─────────────────┘
```
快捷键：`Space+bb` 切换文件

**Tab 2：后端**
```
┌──────────────────────┐
│ Main.java   │ Service.java │
└──────────────────────┘
```
快捷键：`Space+bb` 切换文件

**Tab 3：文档 / 配置**
```
┌──────────────────┐
│ README.md │ pom.xml │
└──────────────────┘
```

### 场景 2：专注单 Tab（高手模式）

只用 1 个 tab + buffer 列表：

```vim
Space + bb    " 列出所有打开的文件
```

优点：
- 更少的上下文切换
- 专注当前任务
- Buffer 之间快速跳转

---

## 七、一句话总结

🔥 **在 nvim 里：**

- **Tabs** = 工作区 / 场景（2-3 个即可）
- **Buffers** = 文件（用 `Space+bb` 切换）
- **Windows** = 分屏（用 `Ctrl+w` 管理）

**90% 情况下你只需要 buffer，不需要多个 tab。**

---

## 八、从现在开始的工作流建议

### 快速启动
```bash
nv ~/project
```

### 打开新文件（到当前 Tab）
```vim
:e src/Main.java
Space + bb      " 看看打开了哪些文件
```

### 不同场景切换
```vim
gt              " 下一个 tab
gT              " 上一个 tab
Space + bb      " 在 tab 内切文件
```

### 查看全局位置
```vim
:jumps          " 你跳过哪些地方
Space + fo      " 最近打开的文件
```

---

## 九、进阶技巧（选看）

### 快速定位 Tab

```vim
" 获取当前 tab 编号
:echom tabpagenr()

" 快速跳转
5gt    " 跳到第 5 个 tab
```

### 自动创建工作布局

创建 `~/.config/nvim/session.vim`:

```vim
" Session 1: Frontend
tabedit src/App.vue
tabedit src/main.ts

" Session 2: Backend
tabnew
tabedit src/Main.java
tabedit src/Service.java

" 回到第一个 tab
1gt
```

启动时加载：
```bash
nv -S session.vim
```

---

## 总结速查

**今天必学的 5 个快捷键：**

```vim
Ctrl+o        后退到上一个位置
Ctrl+i        前进到下一个位置
Space+bb      切换 buffer
gt            下一个 tab
gT            上一个 tab
```

**3 个命令一定记得：**

```vim
:jumps        查看跳转历史
:tabnew       新建 tab
:tabc         关闭 tab
```

从现在开始，你已经掌握了 Vim 的"导航系统"，可以像真正的 Vim 用户一样高效工作了！
