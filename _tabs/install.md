---
layout: post
icon: fas fa-arrow-circle-down
order: 1
toc: true
post_style: page
---

# Webdev Neovim Configuration Installation

## Install Lazyman

[Lazyman](https://lazyman.dev) provides tools to manage multiple Neovim configurations
and a shell function to fuzzy search and select among them.

```bash
git clone https://github.com/doctorfree/nvim-lazyman $HOME/.config/lazyman/Lazyman
$HOME/.config/lazyman/Lazyman/lazyman.sh
```

## Install the Webdev Neovim configuration

```bash
lazyman -L Webdev
```

Alternately, use the `lazyman` command menu interface to install `Webdev`.

## Configure the shell environment

```bash
export NVIM_APPNAME="lazyman/Webdev"
alias nvim="NVIM_APPNAME=lazyman/Webdev nvim"
```

Alternately, use the `nvims` Lazyman shell function and select `Webdev`.
