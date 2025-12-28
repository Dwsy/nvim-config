# Git 集成指南

## 已集成的 Git 功能

NvChad 通过 **Gitsigns** 插件提供 Git 集成：

| 功能 | 说明 |
|------|------|
| 行内标记 | 左侧显示修改/新增/删除标记 |
| Blame | 查看每行的提交者信息 |
| Hunk 操作 | 暂存/撤销单个代码块 |
| Diff 查看 | 对比当前修改 |

---

## 行内 Git 标记

编辑文件时，左侧会显示 Git 状态：

| 标记 | 含义 |
|------|------|
| `│` 绿色 | 新增的行 |
| `│` 蓝色 | 修改的行 |
| `_` 红色 | 删除的行（在下方显示） |

---

## Hunk 操作

### 什么是 Hunk？

Hunk = 一块连续的修改。比如连续修改了 3 行，这 3 行就是一个 Hunk。

### 导航 Hunk

```vim
]c          " 跳到下一个 Hunk
[c          " 跳到上一个 Hunk
```

### Hunk 操作

```vim
" 暂存当前 Hunk
:Gitsigns stage_hunk

" 撤销当前 Hunk（恢复原状）
:Gitsigns reset_hunk

" 预览当前 Hunk 的修改
:Gitsigns preview_hunk
```

### 实战流程

```vim
" 1. 编辑代码
i
...修改代码...
Esc

" 2. 查看这块改了什么
:Gitsigns preview_hunk

" 3. 确认无误，暂存
:Gitsigns stage_hunk

" 4. 跳到下一处修改
]c

" 5. 如果这处不想要，撤销
:Gitsigns reset_hunk
```

---

## Git Blame

### 查看当前行的提交信息

```vim
:Gitsigns blame_line
```

显示格式：
```
作者 (时间) 提交信息
```

### 开启行内 Blame（持续显示）

```vim
:Gitsigns toggle_current_line_blame
```

每行末尾会显示提交者信息。

---

## Diff 查看

### 查看当前文件的修改

```vim
:Gitsigns diffthis
```

会在分屏中显示与 HEAD 的对比。

### 与特定提交对比

```vim
:Gitsigns diffthis ~2
```

与 HEAD~2 对比。

### 关闭 Diff 视图

```vim
:q          " 关闭 diff 窗口
```

---

## 整体操作

### 暂存整个文件

```vim
:Gitsigns stage_buffer
```

### 撤销整个文件修改

```vim
:Gitsigns reset_buffer
```

### 撤销暂存

```vim
:Gitsigns reset_buffer_index
```

---

## 使用终端进行 Git 操作

### 打开终端

```vim
Space + h   " 水平终端
Space + v   " 垂直终端
```

### 常用 Git 命令

```bash
# 状态查看
git status
git log --oneline -10

# 暂存
git add .
git add <file>

# 提交
git commit -m "feat: 添加新功能"

# 推送拉取
git push
git pull

# 分支
git branch
git checkout -b feature/new
git merge main
```

---

## Telescope Git 集成

### 查看提交历史

```vim
Space + gc      " Git commits
```

在列表中选择提交可以查看详情。

### 查看 Git 状态

```vim
Space + gs      " Git status
```

显示所有修改的文件，可以快速跳转。

### 查看分支

```vim
:Telescope git_branches
```

可以切换分支。

---

## 完整工作流示例

### 场景：完成一个功能并提交

```vim
" 1. 编写代码
...编辑...
:w

" 2. 查看所有改动
Space + gs              " 查看修改了哪些文件

" 3. 逐个 Hunk 审查
]c                      " 下一个修改
:Gitsigns preview_hunk  " 预览
:Gitsigns stage_hunk    " 暂存

" 4. 打开终端提交
Space + h
git commit -m "feat: 完成用户登录功能"
exit

" 5. 推送
Space + h
git push
exit
```

### 场景：撤销某处修改

```vim
" 1. 找到要撤销的位置
]c          " 或 [c 导航

" 2. 查看这里改了什么
:Gitsigns preview_hunk

" 3. 撤销这个修改
:Gitsigns reset_hunk
```

### 场景：查看某行是谁写的

```vim
" 1. 光标移到目标行
" 2. 查看 Blame
:Gitsigns blame_line

" 显示：
" John (2025-01-15) feat: 添加登录验证
```

---

## 常用快捷键速查

### Hunk 导航

```vim
]c          下一个 Hunk
[c          上一个 Hunk
```

### Gitsigns 命令

```vim
:Gitsigns stage_hunk           暂存当前 Hunk
:Gitsigns reset_hunk           撤销当前 Hunk
:Gitsigns preview_hunk         预览当前 Hunk
:Gitsigns stage_buffer         暂存整个文件
:Gitsigns reset_buffer         撤销整个文件
:Gitsigns blame_line           查看当前行 Blame
:Gitsigns toggle_current_line_blame   开关行内 Blame
:Gitsigns diffthis             查看 Diff
```

### Telescope Git

```vim
Space+gc    提交历史
Space+gs    Git 状态
```

---

## 配置自定义快捷键（可选）

编辑 `~/.config/nvim/lua/mappings.lua`：

```lua
local map = vim.keymap.set

-- Git 相关快捷键
map("n", "<leader>gs", ":Gitsigns stage_hunk<CR>", { desc = "Stage hunk" })
map("n", "<leader>gr", ":Gitsigns reset_hunk<CR>", { desc = "Reset hunk" })
map("n", "<leader>gp", ":Gitsigns preview_hunk<CR>", { desc = "Preview hunk" })
map("n", "<leader>gb", ":Gitsigns blame_line<CR>", { desc = "Blame line" })
map("n", "<leader>gd", ":Gitsigns diffthis<CR>", { desc = "Diff this" })
```

这样就可以用 `Space + gs` 暂存 Hunk 等。

---

## 与命令行 Git 配合

Neovim 内的 Git 操作主要用于：
- 查看修改（Diff / Blame）
- 精细控制（Hunk 级别暂存/撤销）

复杂操作仍建议用终端：
- 合并冲突解决
- 交互式 rebase
- 复杂的分支操作

```vim
Space + h       " 打开终端
git rebase -i HEAD~3
exit
```

---

## 一句话总结

**`]c` 导航 Hunk + `:Gitsigns stage_hunk` 暂存 + `:Gitsigns preview_hunk` 预览 = 精细版本控制**

比命令行 `git add -p` 更直观，是代码审查和精细提交的利器。
