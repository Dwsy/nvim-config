# 常见问题排查

## 启动问题

### 问题：启动报错 `module 'xxx' not found`

**原因**：插件未正确安装或版本不兼容

**解决**：

```bash
# 1. 清理插件缓存
rm -rf ~/.local/share/nvim/lazy

# 2. 重新启动
nvim

# 3. 等待插件自动安装完成
```

### 问题：启动非常慢

**排查**：

```vim
:Lazy profile
```

查看哪个插件加载慢。

**常见原因**：

1. treesitter 首次编译语法文件
2. Mason 下载 LSP 服务器
3. 网络问题

---

## Treesitter 问题

### 问题：`module 'nvim-treesitter.configs' not found`

**原因**：treesitter main 分支移除了旧 API

**解决**：锁定 master 分支

```lua
-- plugins/init.lua
{
  "nvim-treesitter/nvim-treesitter",
  branch = "master",  -- 关键
}
```

### 问题：语法高亮不工作

**排查**：

```vim
:TSInstallInfo
```

**解决**：

```vim
:TSInstall <语言>
```

例如：`:TSInstall typescript vue python`

---

## LSP 问题

### 问题：`gd` 不工作

**排查步骤**：

```vim
" 1. 检查 LSP 是否启动
:LspInfo

" 2. 检查文件类型
:set ft?

" 3. 检查 LSP 日志
:LspLog
```

**常见原因**：

| 原因 | 解决 |
|------|------|
| LSP 未安装 | `:Mason` 安装对应服务器 |
| 项目无根目录 | 添加 `.git` 或配置文件 |
| 服务器崩溃 | `:LspRestart` |

### 问题：补全不出现

**检查**：

1. 确保在插入模式
2. 输入几个字符触发
3. 手动触发：`Ctrl+Space`

**如果还不行**：

```vim
:checkhealth nvim-cmp
```

### 问题：格式化不工作

**排查**：

```vim
:ConformInfo
```

**解决**：

```vim
" 1. 确认 formatter 已安装
:Mason

" 2. 手动格式化测试
:lua require("conform").format()
```

---

## Java 专项问题

### 问题：Java LSP 不启动

**检查 jdtls 安装**：

```bash
ls ~/.local/share/nvim/mason/packages/jdtls/
```

**手动测试**：

```vim
:lua print(vim.inspect(vim.lsp.get_active_clients()))
```

### 问题：Lombok 注解不识别

**原因**：缺少 `-javaagent:lombok.jar`

**检查 lombok.jar 是否存在**：

```bash
ls ~/.local/share/nvim/mason/packages/jdtls/lombok.jar
```

**确认配置**：

```lua
cmd = {
  -- ... 其他参数
  "-javaagent:" .. jdtls_path .. "/lombok.jar",
}
```

---

## Mason 问题

### 问题：Mason 安装失败

**排查**：

```vim
:MasonLog
```

**常见原因**：

1. 网络问题 → 检查代理设置
2. 磁盘空间 → 清理空间
3. 权限问题 → 检查目录权限

**手动重试**：

```vim
:MasonInstall <工具名>
```

### 问题：找不到已安装的工具

**检查 PATH**：

```vim
:lua print(vim.fn.stdpath("data") .. "/mason/bin")
```

确保这个路径在系统 PATH 中。

---

## 快捷键问题

### 问题：快捷键不生效

**排查**：

```vim
:verbose map <快捷键>
```

例如：`:verbose map gd`

**常见原因**：

1. 快捷键被其他插件覆盖
2. 只在特定模式生效
3. 需要 Leader 键（默认 Space）

### 问题：Leader 键是什么

NvChad 默认 Leader = `Space`

所以 `<leader>ff` = `Space + ff`

---

## 性能问题

### 问题：编辑大文件卡顿

**临时禁用功能**：

```vim
:TSBufDisable highlight
:LspStop
```

**长期方案**：配置大文件自动禁用

```lua
-- 在 autocmd 中检测文件大小
vim.api.nvim_create_autocmd("BufReadPre", {
  callback = function()
    local file_size = vim.fn.getfsize(vim.fn.expand("%"))
    if file_size > 1024 * 1024 then  -- 1MB
      vim.cmd("TSBufDisable highlight")
    end
  end,
})
```

### 问题：内存占用高

**检查 Buffer 数量**：

```vim
:buffers
```

**清理不用的 Buffer**：

```vim
:bd <编号>
" 或关闭所有隐藏 buffer
:%bd|e#
```

---

## 网络问题

### 问题：插件下载失败

**设置代理**：

```bash
# 在 shell 中
export https_proxy=http://127.0.0.1:7890
nvim
```

**或在 Neovim 中**：

```lua
vim.env.https_proxy = "http://127.0.0.1:7890"
```

### 问题：Mason 下载超时

**增加超时时间**：

```lua
require("mason").setup({
  max_concurrent_installers = 2,
})
```

---

## 重置一切

如果问题无法解决，可以完全重置：

```bash
# 1. 备份当前配置
mv ~/.config/nvim ~/.config/nvim.bak

# 2. 清理所有数据
rm -rf ~/.local/share/nvim
rm -rf ~/.local/state/nvim
rm -rf ~/.cache/nvim

# 3. 重新克隆配置
git clone https://github.com/Dwsy/nvim-config ~/.config/nvim

# 4. 重新启动
nvim
```

---

## 获取帮助

### 查看健康状态

```vim
:checkhealth
```

这会检查所有组件状态。

### 查看日志

| 命令 | 内容 |
|------|------|
| `:LspLog` | LSP 日志 |
| `:MasonLog` | Mason 日志 |
| `:messages` | Neovim 消息 |

### 常用调试命令

```vim
:lua print(vim.inspect(xxx))   " 打印变量
:verbose set xxx?              " 查看选项来源
:scriptnames                   " 已加载的脚本
:map                           " 所有映射
```

---

## 速查表

```vim
" 诊断
:checkhealth          全面健康检查
:LspInfo              LSP 状态
:TSInstallInfo        Treesitter 状态
:Mason                工具管理
:Lazy                 插件管理

" 重启
:LspRestart           重启 LSP
:Lazy sync            同步插件
:TSUpdate             更新语法

" 日志
:LspLog               LSP 日志
:MasonLog             Mason 日志
:messages             消息历史

" 清理
:bd                   关闭 Buffer
:%bd|e#               关闭所有隐藏 Buffer
```
