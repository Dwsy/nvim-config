# LSP 代码智能功能指南

## 什么是 LSP？

LSP（Language Server Protocol）是代码智能的核心，提供：

| 功能 | 说明 |
|------|------|
| 跳转定义 | `gd` 跳到函数/类定义处 |
| 查看引用 | `gr` 查看所有引用位置 |
| 智能补全 | 自动补全代码 |
| 悬浮文档 | `K` 查看函数文档 |
| 重命名 | 批量重命名变量 |
| 代码诊断 | 实时显示错误和警告 |

---

## 核心快捷键

### 导航类

| 快捷键 | 功能 |
|--------|------|
| `gd` | 跳转到定义 |
| `gD` | 跳转到声明 |
| `gr` | 查看所有引用 |
| `gi` | 跳转到实现 |
| `Ctrl+o` | 跳回上一个位置 |
| `Ctrl+i` | 跳到下一个位置 |

### 信息类

| 快捷键 | 功能 |
|--------|------|
| `K` | 显示悬浮文档 |
| `Space + lf` | 格式化代码 |
| `Space + ca` | 代码操作（Code Actions） |
| `Space + ra` | 重命名符号 |

### 诊断类

| 快捷键 | 功能 |
|--------|------|
| `[d` | 上一个诊断 |
| `]d` | 下一个诊断 |
| `Space + q` | 诊断列表 |

---

## 常用工作流

### 1. 阅读代码

```vim
gd          " 跳到定义
K           " 看文档
Ctrl+o      " 返回
```

### 2. 查找使用

```vim
gr          " 查看所有引用
            " 在 Telescope 中选择
Enter       " 跳转到引用处
```

### 3. 重构代码

```vim
Space + ra  " 重命名当前符号
            " 输入新名称
Enter       " 全部替换
```

### 4. 修复问题

```vim
]d          " 跳到下一个错误
Space + ca  " 查看可用的修复
Enter       " 应用修复
```

---

## 已配置的 LSP 服务器

| 语言 | 服务器 | 功能 |
|------|--------|------|
| TypeScript/JS | ts_ls | 全功能 |
| Vue | vue_ls | 模板 + 脚本 |
| Python | pyright | 类型检查 |
| Rust | rust_analyzer | 全功能 |
| Java | jdtls | Lombok 支持 |
| Lua | lua_ls | Neovim API |
| HTML | html | 标签补全 |
| CSS | cssls | 样式补全 |

---

## 检查 LSP 状态

### 查看当前 LSP

```vim
:LspInfo
```

输出示例：
```
Attached clients:
  - ts_ls (typescript)
  - vue_ls (vue)
```

### 检查是否正常工作

1. 打开一个代码文件
2. 光标放在函数名上
3. 按 `gd`
4. 如果跳转成功 → LSP 正常

### 手动重启 LSP

```vim
:LspRestart
```

---

## 代码操作（Code Actions）

`Space + ca` 可以触发的操作：

| 场景 | 操作 |
|------|------|
| 未导入的模块 | 自动添加 import |
| 未使用的变量 | 移除或添加下划线 |
| 缺少类型 | 自动推断类型 |
| 简化代码 | 重构建议 |

---

## 格式化

### 手动格式化

```vim
Space + lf
```

### 保存时自动格式化

已配置 conform.nvim，支持：

| 语言 | 格式化工具 |
|------|------------|
| TypeScript/JS/Vue | prettier |
| Python | ruff |
| Lua | stylua |
| Rust | rustfmt |

---

## 常见问题

### Q: `gd` 不工作？

1. 检查 LSP 是否启动：`:LspInfo`
2. 确认文件类型正确：`:set ft?`
3. 重启 LSP：`:LspRestart`
4. 检查 Mason 是否安装了对应服务器：`:Mason`

### Q: 补全不出现？

1. 确保在插入模式
2. 输入几个字符触发
3. 检查 LSP 状态
4. 尝试 `Ctrl+Space` 手动触发

### Q: Java 的 Lombok 不识别？

确认 nvim-jdtls 已配置 `-javaagent:lombok.jar`，见 CONFIG_LOG.md。

### Q: 格式化没反应？

1. 检查 conform.nvim 配置
2. 确认 formatter 已安装：`:Mason`
3. 查看格式化日志：`:ConformInfo`

---

## 速查表

```vim
" 导航
gd              跳转定义
gr              查看引用
K               悬浮文档
Ctrl+o/i        前进后退

" 编辑
Space+ca        代码操作
Space+ra        重命名
Space+lf        格式化

" 诊断
]d / [d         下/上一个诊断
Space+q         诊断列表

" 调试
:LspInfo        查看 LSP 状态
:LspRestart     重启 LSP
:Mason          管理工具
```

---

## 一句话总结

**`gd` 跳定义 + `K` 看文档 + `gr` 找引用 = 代码阅读三件套**

掌握这三个，你就能像 IDE 一样高效阅读代码了。
