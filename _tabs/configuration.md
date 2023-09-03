---
layout: post
icon: fas fa-info-circle
order: 2
toc: true
post_style: page
---

## Lazyman Menu Configuration

The `lazyman` menu interface supports a menu-driven configuration of the
`Webdev` Neovim configuration. Many of the options, settings, and plugins
used by `Webdev` can be configured using this menu system.

To view the `Webdev` configuration menu, execute the `lazyman` command
and select the `Webdev Config` option by entering the option number or
`Webdev Config` at the menu prompt.

## Get/Set Configuration Script

The `Webdev` configuration menu includes the capability to get and set
configuration values for the `Webdev` Neovim configuration. This is done
using a new Neovim feature introduced in version 0.9, the ability to invoke
Neovim with a Lua script:

```bash
nvim -l /path/to/some_script.lua
```

The specified Lua script is executed in the Neovim runtime environment
and then Neovim exits.

Lazyman uses the Lua script `~/.config/nvim-Lazyman/scripts/get_conf.lua`
to manage Neovim configuration settings. This script can retrieve the current
value of a setting, set the value of a supported setting, list all supported
setting names, or display the location of the Neovim configuration.

## Lazyman configuration script

The `~/.config/nvim-Lazyman/scripts/lazyman_config.sh` script is used to
display the Lazyman Neovim Configuration menus and invoke the `get_conf.lua`
script to get and set configuration values. The usage message of `lazyman_config.sh`:

```
Usage: lazyman_config [-a] [-d] [-i] [-m menu] [-s name value] [-u]
Where:
    -a lists all configuration names and exits
    -d specifies debug mode
    -i indicates initialize conditional plugin configurations and exit
    -m 'menu' specifies the menu to display (conf, form, lsp, plugins)
    -s 'name value' indicates set the value of configuration 'name' to 'value'
      if 'name' is 'get' then 'value' is the configuration name to get
      if 'name' is a table then 'value' is the table entry to set
      follow 'value' with 'enable' or 'disable'
    -u displays this usage message and exits
Examples:
  Display the 'Formatters' menu
    lazyman_config -m form
  Set the theme to 'kanagawa'
    lazyman_config -s theme kanagawa
  Get the theme setting
    lazyman_config -s get theme
  Disable 'gopls' language server
    lazyman_config -s lsp_servers gopls disable
```

## Get/Set configuration script source

The `get_conf.lua` script is used to get or set configuration values for those
Lazyman Neovim configurations with a `lazyman` menu configuration interface.

The `get_conf.lua` source code:

```lua
-- Invoke with 'nvim -l get_conf.lua conf_name'
-- Where 'conf_name' is:
--   - one of the entries in lua/configuration.lua
--   - the keyword 'config_home' to get configuration location info
--   - the keyword 'list_names' to lists all configuration entry names
--   - an option/variable name to retrieve its value
--
-- For example, to retrieve the Lazyman configuration 'namespace' setting:
--
-- #!/bin/bash
-- NVIM_APPNAME="nvim-Lazyman" \
--   nvim -l ~/.config/nvim-Lazyman/scripts/get_conf.lua namespace
--
-- or, to retrieve the value of the 'mouse' option in the Webdev config:
--
-- #!/bin/bash
-- NVIM_APPNAME="nvim-Webdev" \
--   nvim -l ~/.config/nvim-Lazyman/scripts/get_conf.lua mouse

local config = vim.inspect(_G.arg[1])
local arg = string.gsub(config, '"', "")
local app_name = os.getenv("NVIM_APPNAME") or ""

local function get_var(var_name, default_value)
  local s, v = pcall(function()
    return vim.api.nvim_get_option(var_name)
  end)
  if s then
    return v
  else
    s, v = pcall(function()
      return vim.api.nvim_get_var(var_name)
    end)
    if s then
      return v
    else
      return default_value
    end
  end
end

local function print_var(entry)
  if type(entry) == "string" then
    io.write(entry .. "\n")
  elseif type(entry) == "table" then
    table.sort(entry)
    for _, val in ipairs(entry) do
      io.write(val .. "\n")
    end
  else
    io.write(tostring(entry) .. "\n")
  end
end

if arg == "config_home" then
  local config_home = vim.fn.stdpath("config")
  io.write("Neovim configuration location = " .. vim.fn.expand(config_home) .. "\n")
  io.write("NVIM_APPNAME = " .. app_name .. "\n")
else
  local var_val = ""
  if app_name == "nvim-Lazyman" then
    local settings = require("configuration")
    if arg == "list_names" then
      for k,_ in pairs(settings) do
        io.write(k .. "\n")
      end
    else
      local entry = settings[arg]
      if entry ~= nil then
        print_var(entry)
      else
        var_val = get_var(arg, "")
        print_var(var_val)
      end
    end
  else
    var_val = get_var(arg, "")
    print_var(var_val)
  end
end
```
