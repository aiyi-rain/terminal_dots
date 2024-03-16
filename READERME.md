内容：
[安装你喜欢的终端工具](#安装你喜欢的终端工具)
[配置你的wezterm](#配置你的wezterm)
[安装shell程序](#安装shell程序)
[安装oh-my-posh](#安装oh-my-posh)
[配置命令别名](#配置命令别名)
[效果展示](#效果展示)
# 安装你喜欢的终端工具
安装自己喜欢的终端工具，如果你习惯了Linux终端工具，不要慌，很多终端工具在Windows下也可以使用。
- alacritty
- kitty
- wezterm
- terminal
- ...etc
这里我们安装的是wezterm，官网下载对应安装包，傻瓜式安装。
# 配置你的wezterm
将终端配置成你喜欢的样式
在用户目录下的.config文件夹下建立wezterm文件夹，这里我使用了GitHub上国外大佬的配置文件并作了简单修改,大家也可以在官方文档指导下自己配置：
- 设置powershell作为shell程序，告别cmd
- 设置窗口透明度
- 设置默认窗口大小
创建wezterm.lua文件
```lua
--- wezterm.lua

--- $ figlet -f small Wezterm

--- __      __      _

--- \ \    / /__ __| |_ ___ _ _ _ __

---  \ \/\/ / -_)_ /  _/ -_) '_| '  \

---   \_/\_/\___/__|\__\___|_| |_|_|_|

---

--- My Wezterm config file

  

local wezterm = require("wezterm")

local act = wezterm.action

  
  

local config = {}

-- Use config builder object if possible

if wezterm.config_builder then

  config = wezterm.config_builder()

end

  

--设置 powershell

config.set_environment_variables={

 COMSPEC="C:\\Program Files\\PowerShell\\7\\pwsh.exe"

}

  

config.color_scheme = "Tokyo Night"

config.font = wezterm.font_with_fallback({

  { family = "ComicShannsMono Nerd Font Mono", scale = 1.2, weight = "Medium" },

  { family = "Symbols Nerd 常规", scale = 2.3 },

})

config.window_background_opacity = 0.6

config.window_decorations = "RESIZE"

config.window_close_confirmation = "AlwaysPrompt"

config.scrollback_lines = 3000

config.default_workspace = "main"

  

-- Dim inactive panes

config.inactive_pane_hsb = {

  saturation = 0.24,

  brightness = 0.5,

}

  

--set the window size

config.initial_cols = 120

config.initial_rows = 30

  

-- Keys

config.leader = { key = "a", mods = "CTRL", timeout_milliseconds = 3000 }

config.keys = {

  -- Send C-a when pressing C-a twice

  { key = "a",          mods = "LEADER|CTRL", action = act.SendKey({ key = "a", mods = "CTRL" }) },

  { key = "c",          mods = "LEADER",      action = act.ActivateCopyMode },

  { key = "phys:Space", mods = "LEADER",      action = act.ActivateCommandPalette },

  

  -- Pane keybindings

  { key = "s",          mods = "LEADER",      action = act.SplitVertical({ domain = "CurrentPaneDomain" }) },

  { key = "v",          mods = "LEADER",      action = act.SplitHorizontal({ domain = "CurrentPaneDomain" }) },

  { key = "h",          mods = "LEADER",      action = act.ActivatePaneDirection("Left") },

  { key = "j",          mods = "LEADER",      action = act.ActivatePaneDirection("Down") },

  { key = "k",          mods = "LEADER",      action = act.ActivatePaneDirection("Up") },

  { key = "l",          mods = "LEADER",      action = act.ActivatePaneDirection("Right") },

  { key = "q",          mods = "LEADER",      action = act.CloseCurrentPane({ confirm = true }) },

  { key = "z",          mods = "LEADER",      action = act.TogglePaneZoomState },

  { key = "o",          mods = "LEADER",      action = act.RotatePanes("Clockwise") },

  -- We can make separate keybindings for resizing panes

  -- But Wezterm offers custom "mode" in the name of "KeyTable"

  {

    key = "r",

    mods = "LEADER",

    action = act.ActivateKeyTable({ name = "resize_pane", one_shot = false }),

  },

  

  -- Tab keybindings

  { key = "t", mods = "LEADER", action = act.SpawnTab("CurrentPaneDomain") },

  { key = "[", mods = "LEADER", action = act.ActivateTabRelative(-1) },

  { key = "]", mods = "LEADER", action = act.ActivateTabRelative(1) },

  { key = "n", mods = "LEADER", action = act.ShowTabNavigator },

  {

    key = "e",

    mods = "LEADER",

    action = act.PromptInputLine({

      description = wezterm.format({

        { Attribute = { Intensity = "Bold" } },

        { Foreground = { AnsiColor = "Fuchsia" } },

        { Text = "Renaming Tab Title...:" },

      }),

      action = wezterm.action_callback(function(window, pane, line)

        if line then

          window:active_tab():set_title(line)

        end

      end),

    }),

  },

  -- Key table for moving tabs around

  { key = "m", mods = "LEADER",       action = act.ActivateKeyTable({ name = "move_tab", one_shot = false }) },

  -- Or shortcuts to move tab w/o move_tab table. SHIFT is for when caps lock is on

  { key = "{", mods = "LEADER|SHIFT", action = act.MoveTabRelative(-1) },

  { key = "}", mods = "LEADER|SHIFT", action = act.MoveTabRelative(1) },

  

  -- Lastly, workspace

  { key = "w", mods = "LEADER",       action = act.ShowLauncherArgs({ flags = "FUZZY|WORKSPACES" }) },

}

-- I can use the tab navigator (LDR t), but I also want to quickly navigate tabs with index

for i = 1, 9 do

  table.insert(config.keys, {

    key = tostring(i),

    mods = "LEADER",

    action = act.ActivateTab(i - 1),

  })

end

  

config.key_tables = {

  resize_pane = {

    { key = "h",      action = act.AdjustPaneSize({ "Left", 1 }) },

    { key = "j",      action = act.AdjustPaneSize({ "Down", 1 }) },

    { key = "k",      action = act.AdjustPaneSize({ "Up", 1 }) },

    { key = "l",      action = act.AdjustPaneSize({ "Right", 1 }) },

    { key = "Escape", action = "PopKeyTable" },

    { key = "Enter",  action = "PopKeyTable" },

  },

  move_tab = {

    { key = "h",      action = act.MoveTabRelative(-1) },

    { key = "j",      action = act.MoveTabRelative(-1) },

    { key = "k",      action = act.MoveTabRelative(1) },

    { key = "l",      action = act.MoveTabRelative(1) },

    { key = "Escape", action = "PopKeyTable" },

    { key = "Enter",  action = "PopKeyTable" },

  },

}

  

-- Tab bar

-- I don't like the look of "fancy" tab bar

config.use_fancy_tab_bar = false

config.status_update_interval = 1000

config.tab_bar_at_bottom = false

wezterm.on("update-status", function(window, pane)

  -- Workspace name

  local stat = window:active_workspace()

  local stat_color = "#f7768e"

  -- It's a little silly to have workspace name all the time

  -- Utilize this to display LDR or current key table name

  if window:active_key_table() then

    stat = window:active_key_table()

    stat_color = "#7dcfff"

  end

  if window:leader_is_active() then

    stat = "LDR"

    stat_color = "#bb9af7"

  end

  

  local basename = function(s)

    -- Nothing a little regex can't fix

    return string.gsub(s, "(.*[/\\])(.*)", "%2")

  end

  

  -- Current working directory

  local cwd = pane:get_current_working_dir()

  if cwd then

    if type(cwd) == "userdata" then

      -- Wezterm introduced the URL object in 20240127-113634-bbcac864

      cwd = basename(cwd.file_path)

    else

      -- 20230712-072601-f4abf8fd or earlier version

      cwd = basename(cwd)

    end

  else

    cwd = ""

  end

  

  -- Current command

  local cmd = pane:get_foreground_process_name()

  -- CWD and CMD could be nil (e.g. viewing log using Ctrl-Alt-l)

  cmd = cmd and basename(cmd) or ""

  

  -- Time

  local time = wezterm.strftime("%H:%M")

  

  -- Left status (left of the tab line)

  window:set_left_status(wezterm.format({

    { Foreground = { Color = stat_color } },

    { Text = "  " },

    { Text = wezterm.nerdfonts.oct_table .. "  " .. stat },

    { Text = " |" },

  }))

  

  -- Right status

  window:set_right_status(wezterm.format({

    -- Wezterm has a built-in nerd fonts

    -- https://wezfurlong.org/wezterm/config/lua/wezterm/nerdfonts.html

    { Text = wezterm.nerdfonts.md_folder .. "  " .. cwd },

    { Text = " | " },

    { Foreground = { Color = "#e0af68" } },

    { Text = wezterm.nerdfonts.fa_code .. "  " .. cmd },

    "ResetAttributes",

    { Text = " | " },

    { Text = wezterm.nerdfonts.md_clock .. "  " .. time },

    { Text = "  " },

  }))

end)

  

--[[ Appearance setting for when I need to take pretty screenshots

config.enable_tab_bar = false

config.window_padding = {

  left = '0.5cell',

  right = '0.5cell',

  top = '0.5cell',

  bottom = '0cell',

  

}

--]]

  

return config
```
# 安装shell程序
安装powershell7.x
官网下载安装程序，傻瓜式安装。
将wezterm默认shell程序更换成powershell7.x，找到程序，复制位置，来到wezterm配置文件中。
# 安装oh-my-posh
GitHub下载安装程序，傻瓜式安装。
使用：安装完成后，将安装目录添加到环境变量中，如果在安装时自动添加了就不用手动添加了。
输入命令：`Get-PoshThemes`
可以查看主题，将主题下载到theme文件夹后
这里我下载了一种主题，大家可以在GitHub下载自己喜欢的主题
编辑powershell配置文件：`notepad $PROFILE`
添加如下行：即可令主题生效
```
oh-my-posh init pwsh --config "C:\Program Files\oh-my-posh\themes\space.omp.json" | Invoke-Expression
```
添加如下行，安装文件图标：
```
Import-Module -Name Terminal-Icons
```
修改文件后，要执行：`. $PROFILE`
才会生效。
# 配置命令别名
对于需要在终端使用的命令，我们都可以使用别名。
同样在powershell配置文件中添加：
```
Set-Alias -Name code -Value path
```
这样就可以在终端中使用相关程序了。
# 效果展示
