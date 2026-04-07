# 多语言开发环境配置完全指南

## 概述

你的 Neovim 配置支持 **6 种编程语言**：

| 语言 | LSP | 格式化 | Treesitter | 特性 |
|------|-----|--------|------------|------|
| TypeScript/JS | ts_ls | Prettier | ✅ | 全功能 |
| Vue | vue_ls | Prettier | ✅ | 模板+脚本 |
| Python | pyright | Ruff | ✅ | 类型检查 |
| Rust | rust_analyzer | rustfmt | ✅ | 全功能 |
| Java | jdtls | - | ✅ | **Lombok 支持** |
| Lua | lua_ls | StyLua | ✅ | Neovim API |

---

## TypeScript / JavaScript

### 安装依赖

```vim
" Mason 已配置
:MasonInstall typescript-language-server prettier
```

### 项目配置

创建 `tsconfig.json`（TypeScript）：
```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "commonjs",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true
  },
  "include": ["src/**/*"]
}
```

### 验证 LSP

```bash
# 打开 TypeScript 文件
nvim src/main.ts

" 在 Neovim 中测试
gd          " 跳转定义
K           " 悬浮文档
```

### 常见问题

**Q：LSP 不启动？**

```vim
:LspInfo              " 检查 LSP 状态
:Mason                " 检查工具安装
:TSInstall typescript " 检查 Treesitter
```

**Q：补全不准确？**

确保项目根目录有 `tsconfig.json` 或 `package.json`。

---

## Vue

### 安装依赖

```vim
:MasonInstall vue-language-server prettier
```

### TypeScript 支持

你的配置已配置 TypeScript 集成：
```lua
-- lua/configs/lspconfig.lua
local ts_path = vim.fn.stdpath("data") .. "/mason/packages/typescript-language-server"

vim.lsp.config("vue_ls", {
  init_options = {
    typescript = {
      tsdk = ts_path .. "/node_modules/typescript/lib",
    },
  },
})
```

### 项目配置

创建 `tsconfig.json`（Vue 3 + TypeScript）：
```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "ESNext",
    "strict": true,
    "jsx": "preserve",
    "moduleResolution": "node"
  },
  "include": ["src/**/*.ts", "src/**/*.vue"]
}
```

### 验证

```bash
nvim src/App.vue

" 测试
gd          " 跳转组件定义
K           " 查看组件文档
```

---

## Python

### 安装依赖

```vim
:MasonInstall pyright ruff
```

### 项目配置

创建 `pyproject.toml`：
```toml
[tool.pyright]
include = ["src"]
exclude = ["**/node_modules", "**/__pycache__"]
reportMissingImports = true
reportMissingTypeStubs = false

[tool.ruff]
line-length = 88
indent-width = 4
target-version = "py310"

[tool.ruff.format]
quote-style = "double"
indent-style = "space"
```

### 验证

```bash
nvim main.py

" 测试
gd          " 跳转定义
Space+lf    " 格式化（Ruff）
```

### 虚拟环境

```bash
# 创建虚拟环境
python -m venv .venv
source .venv/bin/activate  # Linux/Mac
.venv\Scripts\activate     # Windows

# 安装依赖
pip install -r requirements.txt

# Pyright 会自动检测虚拟环境
```

---

## Rust

### 安装依赖

```bash
# rust-analyzer 和 rustfmt 通过 rustup 安装
rustup component add rust-analyzer rustfmt
```

或 Mason：
```vim
:MasonInstall rust-analyzer
```

### 项目配置

创建 `rustfmt.toml`：
```toml
max_width = 100
hard_tabs = false
tab_spaces = 4
reorder_imports = true
```

### Cargo 项目

```bash
# 创建项目
cargo new my_project
cd my_project

# 打开
nvim src/main.rs
```

### 验证

```vim
" 测试 LSP
gd          " 跳转定义
gr          " 查看引用
Space+lf    " 格式化（rustfmt）
```

### Cargo 集成

```vim
" 打开终端运行
Space+ht    " 打开终端
cargo build
cargo test
cargo run
```

---

## Java（含 Lombok）

### 安装依赖

```vim
:MasonInstall jdtls
```

### Lombok 支持

你的配置已配置 Lombok：
```lua
-- lua/plugins/init.lua
{
  "mfussenegger/nvim-jdtls",
  ft = "java",
  config = function()
    local jdtls = require("jdtls")
    local jdtls_path = vim.fn.stdpath("data") .. "/mason/packages/jdtls"
    local lombok_path = jdtls_path .. "/lombok.jar"

    local config = {
      cmd = {
        "java",
        -- ... 其他参数
        "-javaagent:" .. lombok_path,  -- Lombok 支持
        "-jar", vim.fn.glob(jdtls_path .. "/plugins/org.eclipse.equinox.launcher_*.jar"),
        "-configuration", jdtls_path .. "/config_mac_arm",
      },
    }
    jdtls.start_or_attach(config)
  end,
}
```

### 验证 Lombok

```java
import lombok.Data;
import lombok.NoArgsConstructor;
import lombok.AllArgsConstructor;

@Data
@NoArgsConstructor
@AllArgsConstructor
public class User {
    private String name;
    private Integer age;
}
```

打开文件后：
```vim
" 检查 LSP
:LspInfo

" 测试 Lombok 生成的方法
gd          " 跳转到 getter/setter
```

### Maven 项目

```bash
# 创建项目
mvn archetype:generate \
  -DgroupId=com.example \
  -DartifactId=my-app \
  -DarchetypeArtifactId=maven-archetype-quickstart

cd my-app
nvim pom.xml
```

### Gradle 项目

```bash
# 创建项目
gradle init --type java-application

cd my-app
nvim build.gradle
```

### Workspace 目录

jdtls 会为每个项目创建 workspace：
```
~/.local/share/nvim/jdtls-workspace/
├── my-project/
├── another-project/
```

---

## Lua

### 安装依赖

```vim
:MasonInstall lua-language-server stylua
```

### Neovim 配置开发

你的配置本身就是 Lua 项目：
```bash
cd ~/.config/nvim
nvim lua/plugins/init.lua
```

### 验证

```lua
-- 测试文件
local M = {}

function M.greet(name)
  return "Hello, " .. name
end

return M
```

```vim
" 测试 LSP
gd          " 跳转定义
Space+lf    " 格式化（StyLua）
```

### StyLua 配置

已配置在 `.stylua.toml`：
```toml
column_width = 120
indent_type = "Spaces"
indent_width = 2
quote_style = "AutoPreferDouble"
```

---

## HTML / CSS

### 安装依赖

```vim
:MasonInstall html-lsp css-lsp prettier
```

### 验证

```bash
nvim index.html
nvim style.css
```

```vim
gd          " 跳转 CSS 类引用
Space+lf    " 格式化（Prettier）
```

---

## 多语言项目

### 全栈项目示例

```
my-fullstack-project/
├── frontend/           # Vue + TypeScript
│   ├── src/
│   │   ├── App.vue
│   │   └── main.ts
│   ├── package.json
│   └── tsconfig.json
│
├── backend/            # Python + FastAPI
│   ├── src/
│   │   └── main.py
│   ├── pyproject.toml
│   └── requirements.txt
│
└── README.md
```

### 工作流

```bash
# 前端
cd frontend
nvim src/App.vue        # Vue LSP + Prettier

# 后端
cd backend
nvim src/main.py        # Pyright + Ruff
```

---

## 语言切换检测

### 查看当前文件类型

```vim
:set ft?
```

### 手动设置文件类型

```vim
:set ft=typescript
:set ft=vue
:set ft=python
:set ft=java
```

### 自动检测

Neovim 根据文件扩展名自动检测：

| 扩展名 | 文件类型 |
|--------|---------|
| `.ts` | typescript |
| `.js` | javascript |
| `.vue` | vue |
| `.py` | python |
| `.java` | java |
| `.rs` | rust |
| `.lua` | lua |
| `.html` | html |
| `.css` | css |

---

## 常见问题

### Q：某个语言 LSP 不工作？

**排查步骤**：

```vim
" 1. 检查文件类型
:set ft?

" 2. 检查 LSP 状态
:LspInfo

" 3. 检查工具安装
:Mason

" 4. 检查 Treesitter
:TSInstallInfo

" 5. 重启 LSP
:LspRestart
```

### Q：Java Lombok 不识别？

**检查**：

```bash
# 确认 lombok.jar 存在
ls ~/.local/share/nvim/mason/packages/jdtls/lombok.jar
```

**确认配置**：
```lua
cmd = {
  -- ...
  "-javaagent:" .. lombok_path,
}
```

### Q：Python 虚拟环境不识别？

**解决**：

```bash
# 激活虚拟环境
source .venv/bin/activate

# 然后在虚拟环境中启动 Neovim
nvim
```

或在 `pyproject.toml` 中配置：
```toml
[tool.pyright]
venvPath = ".venv"
venv = ".venv"
```

### Q：Rust 补全慢？

**优化**：

```lua
-- 限制 rust-analyzer 功能
vim.lsp.config("rust_analyzer", {
  settings = {
    ["rust-analyzer"] = {
      checkOnSave = {
        command = "check",
      },
      procMacro = {
        enable = false,  -- 禁用宏（如果不需要）
      },
    },
  },
})
```

---

## 各语言测试清单

### TypeScript/JS

- [ ] `gd` 跳转定义
- [ ] `K` 悬浮文档
- [ ] `Space+lf` 格式化
- [ ] 补全工作

### Vue

- [ ] 组件跳转
- [ ] 模板补全
- [ ] TypeScript 集成
- [ ] 格式化

### Python

- [ ] 类型检查
- [ ] 跳转定义
- [ ] Ruff 格式化
- [ ] 虚拟环境识别

### Rust

- [ ] 跳转定义
- [ ] 查找引用
- [ ] rustfmt 格式化
- [ ] Cargo 集成

### Java

- [ ] 跳转定义
- [ ] Lombok 支持
- [ ] Maven/Gradle 识别
- [ ] 代码补全

### Lua

- [ ] Neovim API 补全
- [ ] StyLua 格式化
- [ ] 配置开发

---

## 最佳实践

### ✅ 推荐

1. **每个项目配置对应工具** - tsconfig/pyproject 等
2. **使用虚拟环境** - Python/Node.js
3. **定期更新 LSP** - `:MasonUpdate`
4. **测试 LSP 功能** - `gd` / `K` / 补全
5. **配置项目级格式化** - .prettierrc/pyproject.toml

### ❌ 避免

1. ❌ 不配置项目文件（LSP 无法工作）
2. ❌ 混用全局和虚拟环境
3. ❌ 忽略 LSP 错误日志
4. ❌ 不更新工具版本

---

## 速查表

### 安装命令

```vim
:MasonInstall <工具>    安装工具
:TSInstall <语言>      安装语法
```

### 验证命令

```vim
:LspInfo               LSP 状态
:TSInstallInfo         Treesitter 状态
:Mason                 工具列表
```

### 常用操作

```vim
gd                     跳转定义
K                      悬浮文档
Space+lf               格式化
gr                     查找引用
```

---

## 一句话总结

**Mason 安装工具 → 项目配置文件 → 验证 `gd`/`K`/补全 = 多语言开发环境**

为每种语言配置好 LSP 和格式化工具，你就能在 Neovim 中获得 IDE 级别的开发体验。

---

## 相关文档

- [LSP 代码智能](LSP_GUIDE.md) - LSP 功能使用
- [Mason 工具管理](MASON.md) - 安装和管理工具
- [代码格式化](CONFORM_FORMAT.md) - 格式化工具配置
- [Treesitter 语法高亮](TREESITTER.md) - 语法支持