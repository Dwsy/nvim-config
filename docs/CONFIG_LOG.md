# NvChad 配置日志

## 2025-12-28 13:11

### 1. 初始安装

选择 **NvChad** 作为 Neovim 配置框架（GitHub Stars: 27.6k）。

```bash
git clone https://github.com/NvChad/starter ~/.config/nvim
rm -rf ~/.config/nvim/.git
```

### 2. 解决 nvim-treesitter 兼容性问题

**问题**：`module 'nvim-treesitter.configs' not found`

**原因**：nvim-treesitter 从 `master` 迁移到 `main` 分支，移除了 `nvim-treesitter.configs` 模块，NvChad v2.5 尚未适配。

**解决方案**：
1. 预先克隆 treesitter master 分支
2. 在 plugins/init.lua 中锁定 `branch = "master"`
3. 创建 lazy-lock.json 锁定 commit

```bash
git clone --branch master https://github.com/nvim-treesitter/nvim-treesitter.git \
  ~/.local/share/nvim/lazy/nvim-treesitter
```

### 3. 配置 Shell Alias

添加 `nv = nvim` 到三个 shell：

```bash
echo 'alias nv="nvim"' >> ~/.bashrc
echo 'alias nv="nvim"' >> ~/.zshrc
echo 'alias nv="nvim"' >> ~/.config/fish/config.fish
```

### 4. 配置 nvim-tree 合并空目录

**问题**：Java 包目录层级过深（com/jp/med/pms）

**解决**：启用 `group_empty` 选项

```lua
{
  "nvim-tree/nvim-tree.lua",
  opts = {
    renderer = {
      group_empty = true,
    },
  },
},
```

### 5. 配置 LSP 支持

#### Mason 自动安装

```lua
{
  "williamboman/mason.nvim",
  opts = {
    ensure_installed = {
      "typescript-language-server",
      "vue-language-server",
      "pyright",
      "rust-analyzer",
      "jdtls",
      "lua-language-server",
      "html-lsp",
      "css-lsp",
      "prettier",
      "stylua",
      "ruff",
    },
  },
},
```

#### LSP 服务器配置

`~/.config/nvim/lua/configs/lspconfig.lua`:

```lua
require("nvchad.configs.lspconfig").defaults()

local servers = {
  "html",
  "cssls",
  "ts_ls",          -- TypeScript/JavaScript
  "pyright",        -- Python
  "rust_analyzer",  -- Rust
  "lua_ls",         -- Lua
}

vim.lsp.enable(servers)

-- Vue LSP with TypeScript support
local ts_path = vim.fn.stdpath("data") .. "/mason/packages/typescript-language-server"

vim.lsp.config("vue_ls", {
  init_options = {
    typescript = {
      tsdk = ts_path .. "/node_modules/typescript/lib",
    },
  },
})
vim.lsp.enable("vue_ls")
```

### 6. 配置 Java LSP (nvim-jdtls)

**问题**：
1. 标准 lspconfig 的 jdtls 不支持 `gd` (textDocument/definition)
2. 需要 Lombok 支持

**解决**：使用 nvim-jdtls 插件 + Lombok agent

```lua
{
  "mfussenegger/nvim-jdtls",
  ft = "java",
  config = function()
    local jdtls = require("jdtls")
    local jdtls_path = vim.fn.stdpath("data") .. "/mason/packages/jdtls"
    local lombok_path = jdtls_path .. "/lombok.jar"

    local project_name = vim.fn.fnamemodify(vim.fn.getcwd(), ":p:h:t")
    local workspace_dir = vim.fn.stdpath("data") .. "/jdtls-workspace/" .. project_name

    local config = {
      cmd = {
        "java",
        "-Declipse.application=org.eclipse.jdt.ls.core.id1",
        "-Dosgi.bundles.defaultStartLevel=4",
        "-Declipse.product=org.eclipse.jdt.ls.core.product",
        "-Xmx1g",
        "--add-modules=ALL-SYSTEM",
        "--add-opens", "java.base/java.util=ALL-UNNAMED",
        "--add-opens", "java.base/java.lang=ALL-UNNAMED",
        "-javaagent:" .. lombok_path,
        "-jar", vim.fn.glob(jdtls_path .. "/plugins/org.eclipse.equinox.launcher_*.jar"),
        "-configuration", jdtls_path .. "/config_mac_arm",
        "-data", workspace_dir,
      },
      root_dir = vim.fs.dirname(vim.fs.find({ ".git", "mvnw", "gradlew", "pom.xml", "build.gradle" }, { upward = true })[1]),
      settings = {
        java = {
          signatureHelp = { enabled = true },
          contentProvider = { preferred = "fernflower" },
        },
      },
    }
    jdtls.start_or_attach(config)
  end,
},
```

### 7. Vue LSP 配置

**问题**：`volar is deprecated, use vue_ls instead`

**解决**：改用 `vue_ls` 并配置 TypeScript SDK 路径

### 8. 遇到的坑

| 问题 | 原因 | 解决方案 |
|------|------|----------|
| treesitter configs not found | main 分支移除了旧 API | 锁定 master 分支 |
| NvChad v3.0 not found | 不存在该分支 | 使用 v2.5 |
| mason_registry nil | 加载时机问题 | 使用硬编码路径 |
| jdtls gd 不工作 | 需要专用插件 | 使用 nvim-jdtls |
| Lombok 不识别 | 缺少 javaagent | 添加 `-javaagent:lombok.jar` |

---

## 最终配置文件

### ~/.config/nvim/lua/plugins/init.lua

```lua
return {
  {
    "stevearc/conform.nvim",
    event = "BufWritePre",
    opts = require "configs.conform",
  },

  {
    "neovim/nvim-lspconfig",
    config = function()
      require "configs.lspconfig"
    end,
  },

  -- Java LSP with Lombok
  {
    "mfussenegger/nvim-jdtls",
    ft = "java",
    config = function()
      -- ... (见上方完整配置)
    end,
  },

  -- Mason
  {
    "williamboman/mason.nvim",
    opts = {
      ensure_installed = {
        "typescript-language-server",
        "vue-language-server",
        "pyright",
        "rust-analyzer",
        "jdtls",
        "lua-language-server",
        "html-lsp",
        "css-lsp",
        "prettier",
        "stylua",
        "ruff",
      },
    },
  },

  -- Treesitter (locked to master)
  {
    "nvim-treesitter/nvim-treesitter",
    branch = "master",
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
  },

  -- nvim-tree
  {
    "nvim-tree/nvim-tree.lua",
    opts = {
      renderer = {
        group_empty = true,
      },
    },
  },
}
```

---

## 支持的语言

| 语言 | LSP | 格式化 | 特性 |
|------|-----|--------|------|
| TypeScript/JS | ts_ls | prettier | ✓ |
| Vue | vue_ls | prettier | ✓ |
| Python | pyright | ruff | ✓ |
| Rust | rust_analyzer | rustfmt | ✓ |
| Java | jdtls | - | Lombok 支持 |
| Lua | lua_ls | stylua | ✓ |
| HTML/CSS | html/cssls | prettier | ✓ |
