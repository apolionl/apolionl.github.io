---
layout: post
title: "Setup LTeX LSP in NVIM"
date: 2023-08-10 1:00:05
categories: regular latex
tags: regular latex nvim
image: /assets/article_images/2022-08-15-julia_environment/night-track.JPG
image2: /assets/article_images/2022-08-15-julia_environment/night-track-mobile.JPG
---

This is a mini blog for a fast setup of the **LTeX Language Server**, a LSP language server for LanguageTool with support for LaTeX, Markdown and others. Moreover, I will set up the **LTeX extra** plugin for NVIM, which provides additional functions for LTeX file handling (off-spec lsp).

<!--toc:start-->
- [NVIM Plugins](#nvim-plugins)
  - [Mason](#mason)
  - [LTeX extra](#ltex-extra)
  - [LTeX setup](#ltex-setup)
- [Comments](#comments)
<!--toc:end-->

## NVIM Plugins

Before setting up anything, we must ensure that certain NVIM plugins are installed. I will let up to you the plugin manager.

### Mason

To install the server manager, we first install `mason` that automatically install most of the binaries that are required for this mini-blog to work. In principle `mason` installs an instance of `ltex-ls` along with `LanguageTool`. Just in case, you can install `LanguageTool` in your machine, but in most cases this is not needed.

```lua
	{ "williamboman/mason.nvim" },
	{ "williamboman/mason-lspconfig.nvim" },
```

### LTeX extra

To install LTeX one needs to open `mason` in NVIM and install `ltex-ls`. For LTeX-extra, we need to install a plugin and setup some variables. In my case, I only setup the file types for which this plugin will activates.

```lua
	{"barreiroleo/ltex_extra.nvim"] = {
	ft = { "bib", "gitcommit", "markdown", "org", "plaintex", "rst", "rnoweb", "tex", "pandoc" },
}
```

### LTeX setup

Technically, all is ready, what is left is to config `ltex-ls`/`ltex_extra`.

```lua
-- https://github.com/neovim/nvim-lspconfig/blob/master/lua/lspconfig/server_configurations/ltex.lua
return function(defaults)
	-- Config ltex-extra
	require("ltex_extra").setup({
		init_check = true,
		-- Path for check spelling changes.
		path = vim.fn.getcwd() .. "/spell",
		log_level = "error",
		server_opts = {
			on_attach = defaults.on_attach,
			capabilities = defaults.capabilities,
			single_file_support = true,
			-- Server
			cmd = { "ltex-ls" },
			settings = {
				ltex = {
					-- File types. Redundancy
					enabled = { "bib", "gitcommit", "markdown", "org", "plaintex", "rst", "rnoweb", "tex", "pandoc" },
					language = "en-US",
				},
			},
		},
	})
end
```

## Comments

If you want to learn more about the LaTeX LSP please check [their github repository][pap1], [its config][pap2], and obviously the [ltex-extra plugin][pap3].



[pap1]: https://github.com/valentjn/ltex-ls "LTeX"
[pap2]: https://github.com/neovim/nvim-lspconfig/blob/master/lua/lspconfig/server_configurations/ltex.lua "ltex minimum setup"
[pap3]: hhttps://github.com/barreiroleo/ltex_extra.nvim "LTeX-extra"

