# lsp_lines.nvim

`lsp_lines` is a simple neovim plugin that renders diagnostics using virtual
lines on top of the real line of code.

![A screenshot of the plugin in action](screenshot.png)

Font is [Fira Code][font], a classic.
Theme is [tokyonight.nvim][theme].

[font]: https://github.com/tonsky/FiraCode
[theme]: https://github.com/folke/tokyonight.nvim

# Background

LSPs provide lots of useful diagnostics for code (typically: errors, warnings,
linting). By default they're displayed using virtual text at the end of the
line which is in many cases good enough, but often there's more than one
diagnostic per line. It's also quite common to have more than one diagnostic
per line, but again, there's no handy way to read the whole thing.

`lsp_lines` solves this issue.

# Installation

## With Lazy.nvim

Using lazy.nvim

```lua
{
    "steven-liou/lsp_lines.nvim",
    event = "LspAttach",
    config = function()
        require("lsp_lines").setup({
        severity = vim.diagnostic.severity.WARN, -- only severity at or above this level will show
        current_line_only = true, -- only show virtual lines on cursor line only
        show_virt_line_events = { "CursorHold" }, -- events to show virtual lines
        hide_virt_line_events = {"CursorMoved", "InsertEnter"}, -- events to hide virtual lines
        diagnostics_filter = require("lsp_lines").most_severe_level_of_buffer, -- pick available diagnostic filters or write your own for this key, or omit this key to not use any filter
        })
    end,
}
```
- Below examples are based on:
    - Minimum severity level of `INFO`
    - Given a buffer with:
        - One `ERROR` and one `WARN` on line 2
        - One `WARN` and one `HINT` on line 3
- `diagnostics_filter` function can be:
    - `minimum_severity_level` show diagnostics at or above the specified minimum severity level. Both `ERROR` and `WARN` will show on line 2, and only `WARN` will show on line 3, since `HINT` doesn't meet minimum severity criteria. Recommended if `current_line_only` is `true`
    - `most_severe_level_per_line` only shows the most severe diagnostics in a given line at or above the specified minimum severity level. Only `ERROR` will show on line 2, and `WARN` on line 3. Recommended if `current_line_only` is `false`
    - `most_severe_level_of_buffer` only shows the most severe diagnostics in a buffer at or above the specified minimum severity level. Only `ERROR` will show on line 2, since the most severe error in the whole buffer is `ERROR`
    - Any custom function that you write, with first parameter a table of LSP `diagnostics`, and second parameter any table of `options`

## With git

You can algo just clone the repo into neovim's plug-in directory:

    mkdir -p $HOME/.local/share/nvim/site/pack/plugins/start/
    cd $HOME/.local/share/nvim/site/pack/plugins/start/
    git clone git@git.sr.ht:~whynothugo/lsp_lines.nvim

And then in `init.lua`:

    require("lsp_lines").setup()

# Setup

It's recommended to also remove the regular virtual text diagnostics to avoid
pointless duplication:

```lua
-- Disable virtual_text since it's redundant due to lsp_lines.
vim.diagnostic.config({
  virtual_text = false,
})
```

# Usage

This plugin's functionality can be disabled with:

```lua
vim.diagnostic.config({ virtual_lines = false })
```

And it can be re-enabled via:

```lua
vim.diagnostic.config({ virtual_lines = true })
```

A helper is also provided to toggle, which is convenient for mappings:

```lua
vim.keymap.set(
  "",
  "<Leader>l",
  require("lsp_lines").toggle,
  { desc = "Toggle lsp_lines" }
)
```

# Contributing

- Discussion or patches: ~whynothugo/lsp_lines.nvim@lists.sr.ht
- Bugs / Issues: https://todo.sr.ht/~whynothugo/lsp_lines.nvim
- Tips: https://ko-fi.com/whynothugo

# Development

It would be nice to show connecting lines when there's relationship between
diagnostics (as is the case with `rust_analyzer`). Oh perhaps surface them via
hover().

# Licence

This project is licensed under the ISC licence. See LICENCE for more details.
