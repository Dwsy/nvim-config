# NvChad 主题与 UI 定制完全指南

## NvChad 主题系统

NvChad 使用 **base46** 主题引擎，特点：

- 🎨 **40+ 内置主题** - 开箱即用
- 🔧 **易于定制** - 覆盖颜色组
- ⚡ **高性能** - 编译主题缓存
- 🌗 **轻松切换** - 一键换肤

---

## 你的当前主题

配置位置：`~/.config/nvim/lua/chadrc.lua`

```lua
M.base46 = {
	theme = "ayu_dark",  -- 当前主题

	-- hl_override = {
	-- 	Comment = { italic = true },
	-- 	["@comment"] = { italic = true },
	-- },
}
```

**当前主题**：`ayu_dark`（深色 Ayu 主题）

---

## 可用主题列表

### 热门主题

| 主题名称 | 风格 | 适合场景 |
|---------|------|---------|
| `ayu_dark` | 暖色深色 | 夜间编程 |
| `ayu_light` | 暖色浅色 | 日间办公 |
| `onedark` | VS Code 风格 | 通用 |
| `tokyonight` | 东京夜景 | 炫酷 |
| `catppuccin` | 咖啡色系 | 舒适 |
| `nord` | 北欧冷色 | 专业 |
| `dracula` | 吸血鬼 | 经典 |
| `gruvbox` | 复古绿棕 | 温暖 |
| `everforest` | 森林绿 | 自然 |
| `kanagawa` | 日本浮世绘 | 艺术 |
| `rose-pine` | 玫瑰松木 | 柔和 |
| `github_dark` | GitHub 深色 | 熟悉 |

### 查看所有可用主题

```vim
:Telescope themes
```

或：
```vim
:lua require("nvconfig").base46.theme = "主题名"
```

---

## 切换主题

### 方法 1：修改配置（永久）

编辑 `~/.config/nvim/lua/chadrc.lua`：

```lua
M.base46 = {
	theme = "tokyonight",  -- 改为 tokyonight
}
```

保存后重启 Neovim。

### 方法 2：Telescope 切换（临时）

```vim
Space + th          " 打开主题选择器
选择主题
Enter
```

**注意**：此方法仅当前会话有效，重启后恢复配置中的主题。

### 方法 3：命令切换

```vim
:colorscheme tokyonight
```

---

## 自定义主题颜色

### 覆盖高亮组

编辑 `~/.config/nvim/lua/chadrc.lua`：

```lua
M.base46 = {
	theme = "ayu_dark",

	hl_override = {
		Comment = { italic = true },           -- 注释斜体
		["@comment"] = { italic = true },      -- Treesitter 注释斜体
		["@keyword"] = { bold = true },        -- 关键字加粗
		["@function"] = { fg = "#66d9ef" },    -- 函数名自定义颜色
	},
}
```

### 常用高亮组

| 高亮组 | 作用 | 示例 |
|--------|------|------|
| `Comment` | 注释 | `italic = true` |
| `["@comment"]` | Treesitter 注释 | `italic = true` |
| `["@keyword"]` | 关键字（if/for） | `bold = true` |
| `["@function"]` | 函数名 | `fg = "#66d9ef"` |
| `["@type"]` | 类型名 | `fg = "#66d9ef"` |
| `["@string"]` | 字符串 | `fg = "#a6e22e"` |
| `["@number"]` | 数字 | `fg = "#ae81ff"` |
| `["@variable"]` | 变量 | `fg = "#f8f8f2"` |

### 查看当前高亮

```vim
" 光标放在代码上
:TSHighlightCapturesUnderCursor

" 或命令
:lua print(vim.inspect(vim.api.nvim_get_hl(0, { name = "Normal" })))
```

---

## UI 组件定制

### Tabufline（顶部标签栏）

你的配置：
```lua
M.ui = {
	tabufline = {
		lazyload = false,    -- 不延迟加载
		enabled = true,      -- 启用
	},
}
```

**关闭 Tabufline**：
```lua
M.ui = {
	tabufline = {
		enabled = false,     -- 关闭
	},
}
```

### Statusline（底部状态栏）

NvChad 默认使用自定义 statusline。

**禁用**：
```lua
M.ui = {
	statusline = {
		theme = "default",   -- 或 "minimal", "vscode_colored"
		separator_style = "default",
	},
}
```

### 终端

```lua
M.ui = {
	term = {
		hidden_termnumbers = false,  -- 显示终端编号
		float = {
			relative = "editor",
			row = 0.3,
			col = 0.3,
			width = 0.4,
			height = 0.4,
			border = "single",
		},
	},
}
```

### 侧边栏（NvimTree）

```lua
M.ui = {
	nvdash = {
		load_on_startup = false,     -- 启动时不加载仪表板
		header = { ... },            -- 自定义 ASCII 艺术
		buttons = { ... },           -- 自定义按钮
	},
}
```

---

## 完整 Chadrc 配置示例

```lua
-- ~/.config/nvim/lua/chadrc.lua

---@type ChadrcConfig
local M = {}

-- 主题配置
M.base46 = {
	theme = "ayu_dark",              -- 主题名称
	transparency = false,            -- 是否透明背景
	theme_toggle = { "ayu_dark", "ayu_light" },  -- 切换的主题对

	hl_override = {                  -- 覆盖高亮组
		Comment = { italic = true },
		["@comment"] = { italic = true },
		["@keyword"] = { bold = true },
	},

	hl_add = {                       -- 添加新高亮组
		["MyCustomGroup"] = {
			fg = "#ff0000",
			bg = "#000000",
		},
	},
}

-- UI 配置
M.ui = {
	-- Tabufline
	tabufline = {
		lazyload = false,
		enabled = true,
		show_mode = true,            -- 显示模式
	},

	-- Statusline
	statusline = {
		theme = "default",
		separator_style = "default",
		order = { ... },             -- 自定义组件顺序
	},

	-- 终端
	term = {
		hidden_termnumbers = false,
		float = {
			relative = "editor",
			row = 0.3,
			col = 0.3,
			width = 0.4,
			height = 0.4,
			border = "single",
		},
	},

	-- 启动仪表板
	nvdash = {
		load_on_startup = false,
	},

	-- 颜色映射
	colors = {                       -- 自定义基础颜色
		black = "#1a1a1a",
		red = "#e06c75",
		green = "#98c379",
		yellow = "#e5c07b",
		blue = "#61afef",
		purple = "#c678dd",
		cyan = "#56b6c2",
		white = "#abb2bf",
	},
}

-- NvChad 选项
M = vim.tbl_deep_extend("force", M, {
	-- 覆盖 NvChad 默认选项
})

return M
```

---

## 主题切换快捷键

### 添加快捷键

编辑 `~/.config/nvim/lua/mappings.lua`：

```lua
local map = vim.keymap.set

-- 主题切换
map("n", "<leader>tt", "<cmd>lua require('base46').toggle_theme()<CR>", { desc = "Toggle theme" })
map("n", "<leader>tc", "<cmd>Telescope themes<CR>", { desc = "Choose theme" })
```

使用：
```vim
Space + tt    " 切换深色/浅色
Space + tc    " 选择主题
```

---

## 创建自定义主题

### 方法 1：基于现有主题修改

```lua
-- ~/.config/nvim/lua/base46/extended_integrations/my_theme.lua

local base_30 = {
  -- 覆盖 30 色
  black = "#1a1a1a",
  white = "#ffffff",
  -- ...
}

local base_16 = {
  -- 覆盖 16 色
  base00 = "#1a1a1a",
  base05 = "#ffffff",
  -- ...
}

return { base_16 = base_16, base_30 = base_30 }
```

然后在 `chadrc.lua` 中使用：
```lua
M.base46 = {
	theme = "my_theme",
}
```

### 方法 2：完全自定义

参考 [NvChad 主题模板](https://github.com/NvChad/base46/tree/master/lua/base46/themes)

---

## 透明度与背景

### 启用透明背景

```lua
M.base46 = {
	transparency = true,  -- 启用透明
}
```

**效果**：终端背景颜色会穿透 Neovim。

### 部分透明

```lua
M.base46 = {
	hl_add = {
		NormalFloat = { bg = "#000000", fg = "#ffffff", force = true },
	},
}
```

---

## 推荐主题组合

### 专业办公

```lua
M.base46 = {
	theme = "nord",              -- 冷色专业感
	theme_toggle = { "nord", "nord" },
}
```

### 夜间编程

```lua
M.base46 = {
	theme = "tokyonight",        -- 东京夜景
	theme_toggle = { "tokyonight", "tokyonight_day" },
}
```

### 舒适开发

```lua
M.base46 = {
	theme = "catppuccin",        -- 咖啡色系
	theme_toggle = { "catppuccin", "catppuccin" },
}
```

### 你的当前配置

```lua
M.base46 = {
	theme = "ayu_dark",          -- 暖色深色
}
```

---

## 常见问题排查

### 问题 1：主题切换后颜色异常

**解决**：

```vim
" 清除缓存
:lua require("base46").compile()

" 或
:lua package.loaded.base46 = nil
:lua require("base46").setup()

" 或重启 Neovim
```

### 问题 2：终端透明背景不工作

**检查**：

1. 终端模拟器是否支持透明
2. `transparency = true` 是否设置

**解决**：
```lua
M.base46 = {
	transparency = true,
}
```

### 问题 3：自定义高亮不生效

**检查**：

```vim
" 查看高亮组
:lua print(vim.inspect(vim.api.nvim_get_hl(0, { name = "Comment" })))
```

**解决**：
```lua
hl_override = {
	Comment = { italic = true, force = true },  -- 添加 force
}
```

### 问题 4：主题加载慢

**优化**：

```lua
M.ui = {
	tabufline = {
		lazyload = true,  -- 启用延迟加载
	},
}
```

---

## 主题推荐列表

### 深色主题

| 排名 | 主题 | 特点 |
|------|------|------|
| 1 | `tokyonight` | 炫酷夜景 |
| 2 | `ayu_dark` | 暖色舒适 |
| 3 | `catppuccin` | 咖啡柔和 |
| 4 | `nord` | 北欧冷色 |
| 5 | `dracula` | 经典对比 |
| 6 | `kanagawa` | 日本艺术 |
| 7 | `everforest` | 自然绿色 |
| 8 | `rose-pine` | 玫瑰柔和 |

### 浅色主题

| 排名 | 主题 | 特点 |
|------|------|------|
| 1 | `ayu_light` | 暖色清晰 |
| 2 | `github_light` | GitHub 熟悉 |
| 3 | `nord` | 北欧清晰 |
| 4 | `catppuccin` | 咖啡舒适 |

---

## 最佳实践

### ✅ 推荐

1. **选择对比度高的主题** - 保护眼睛
2. **启用注释斜体** - 区分代码与注释
3. **深色主题用于夜间** - 减少蓝光
4. **浅色主题用于日间** - 阳光下的可读性
5. **定期换主题** - 保持新鲜感

### ❌ 避免

1. ❌ 使用纯黑背景（`#000000`）- 对比度过高
2. ❌ 使用过于花哨的主题 - 分散注意力
3. ❌ 忽略颜色对比度 - 阅读困难
4. ❌ 不自定义高亮 - 默认不一定适合你

---

## 速查表

### 命令

```vim
:colorscheme                查看当前主题
:colorscheme <名称>         切换主题
:Telescope themes           选择主题
```

### 快捷键（建议配置）

```vim
Space+tt        切换深色/浅色
Space+tc        选择主题
```

### 配置

```lua
-- chadrc.lua
M.base46 = {
	theme = "ayu_dark",
	transparency = false,
	hl_override = {
		Comment = { italic = true },
	},
}
```

---

## 一句话总结

**`chadrc.lua` 改主题 + `Space+th` 快速切换 + `hl_override` 自定义 = 完美 UI**

选择一个适合你的主题，微调颜色，让你的 Neovim 独一无二。

---

## 相关文档

- [配置结构详解](CONFIG_STRUCTURE.md) - chadrc.lua 文件位置
- [快捷键自定义](KEYMAPS.md) - 添加主题切换快捷键
- [性能优化](PERFORMANCE.md) - 主题加载优化