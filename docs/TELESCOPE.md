# Telescope 搜索指南

## 什么是 Telescope？

Telescope 是 Neovim 最强大的模糊搜索插件，可以搜索：

- 文件
- Buffer
- 文本内容
- Git 提交
- LSP 符号
- 命令历史
- ...几乎所有东西

---

## 核心快捷键

### 文件搜索

| 快捷键 | 功能 |
|--------|------|
| `Space + ff` | 查找文件（项目内） |
| `Space + fo` | 最近打开的文件 |
| `Space + fw` | 全局搜索文本（grep） |
| `Space + fb` | 切换已打开的 Buffer |

### 快速记忆

```
ff = Find Files      查找文件
fo = Find Old        最近文件
fw = Find Words      搜索文本
fb = Find Buffers    切换 Buffer
```

---

## Telescope 操作

在 Telescope 窗口内：

| 快捷键 | 功能 |
|--------|------|
| `Enter` | 打开选中项 |
| `Ctrl+j` / `Ctrl+k` | 上下移动 |
| `Ctrl+n` / `Ctrl+p` | 上下移动（同上） |
| `Esc` | 关闭 Telescope |
| `Ctrl+c` | 关闭 Telescope |
| `Ctrl+x` | 水平分屏打开 |
| `Ctrl+v` | 垂直分屏打开 |
| `Ctrl+t` | 新 Tab 打开 |
| `Tab` | 多选 |

---

## 常用场景

### 1. 快速打开文件

```vim
Space + ff
输入文件名片段
Enter
```

**技巧**：输入路径片段更精确

```
src/main     →  src/main.ts
comp/but     →  components/Button.vue
```

### 2. 全局搜索文本

```vim
Space + fw
输入要搜索的文本
Enter 跳转到结果
```

**技巧**：支持正则表达式

```
function.*test    →  搜索 "function" 开头包含 "test" 的行
```

### 3. 切换已打开文件

```vim
Space + fb
输入文件名
Enter
```

比 `:bn` / `:bp` 快得多。

### 4. 最近文件

```vim
Space + fo
选择文件
Enter
```

恢复上次关闭的文件。

---

## 进阶搜索

### Git 相关

| 快捷键 | 功能 |
|--------|------|
| `Space + gc` | Git 提交历史 |
| `Space + gs` | Git 状态 |

### LSP 相关

| 快捷键 | 功能 |
|--------|------|
| `gr` | 查找引用（通过 Telescope） |
| `Space + ds` | 文档符号 |

### 其他

| 快捷键 | 功能 |
|--------|------|
| `Space + fh` | 帮助文档 |
| `Space + th` | 切换主题 |
| `Space + ma` | 书签 |
| `Space + cm` | Git 提交 |

---

## 搜索技巧

### 1. 模糊匹配

不需要完整输入：

```
main.ts     →  输入 "mats" 也能匹配
Button.vue  →  输入 "buvue" 也能匹配
```

### 2. 路径过滤

用 `/` 分隔路径：

```
src/comp/but    →  src/components/Button.vue
test/unit       →  tests/unit/ 下的文件
```

### 3. 排除模式

在搜索框输入 `!` 排除：

```
.vue !test     →  Vue 文件但排除 test
```

### 4. 精确匹配

用单引号 `'` 精确匹配：

```
'main.ts       →  只匹配包含 "main.ts" 的
```

---

## 实战工作流

### 场景 1：找一个不知道在哪的文件

```vim
Space + ff
输入你记得的任何片段
```

### 场景 2：找调用某函数的地方

```vim
" 方法 1：LSP 引用
gr

" 方法 2：全局搜索
Space + fw
输入函数名
```

### 场景 3：回到刚才的文件

```vim
Space + fo
最近的文件在最前面
Enter
```

### 场景 4：在多个文件间快速切换

```vim
Space + fb
输入文件名片段
Enter
```

---

## 自定义 Telescope

### 修改默认行为

编辑 `~/.config/nvim/lua/configs/telescope.lua`：

```lua
require("telescope").setup({
  defaults = {
    file_ignore_patterns = {
      "node_modules",
      ".git",
      "dist",
      "build",
    },
  },
})
```

### 常用忽略配置

```lua
file_ignore_patterns = {
  "node_modules/",
  ".git/",
  "dist/",
  "build/",
  "target/",        -- Rust
  "__pycache__/",   -- Python
  "*.class",        -- Java
}
```

---

## 速查表

```vim
" 文件
Space+ff        查找文件
Space+fo        最近文件
Space+fb        切换 Buffer
Space+fw        全局搜索

" Git
Space+gc        提交历史
Space+gs        Git 状态

" 窗口内操作
Enter           打开
Ctrl+x          水平分屏
Ctrl+v          垂直分屏
Ctrl+t          新 Tab
Esc             关闭
```

---

## 常见问题

### Q: 搜索太慢？

1. 添加 `file_ignore_patterns` 排除大目录
2. 确保安装了 `ripgrep`：`brew install ripgrep`
3. 确保安装了 `fd`：`brew install fd`

### Q: 找不到某些文件？

1. 检查 `.gitignore` 是否排除了
2. 检查 `file_ignore_patterns` 配置
3. 尝试用 `:e 完整路径` 直接打开

### Q: 中文搜索不工作？

Telescope 默认支持中文，如果有问题检查终端编码设置。

---

## 一句话总结

**`Space+ff` 找文件 + `Space+fw` 搜内容 + `Space+fb` 切 Buffer = 导航三件套**

熟练使用这三个，你永远不会在项目里迷路。
