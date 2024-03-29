# nvim cheatsheet

## Terminal

Exit terminal mode: <kbd>CTRL</kbd> + <kbd>\\</kbd> <kbd>CTRL</kbd>+<kbd>n</kbd>

## motions/shortcuts

- (n) `f`: Jump to first occurrence of character on current line
- (n) `t`: Jump before first occurrence of character on current line
- (n) `<C-d>`: Scroll down half page
- (n) `<C-u>`: Scroll up half page

Another really good motion is `i` (inside text object) and `a` (around text object).
Combine this with `d` or `y` etc. and you'll have a good time :+1:

## autocmd

To set/run something in a specific path:

```lua
vim.api.nvim_create_autocmd("BufEnter", {
    pattern = "/path", -- supports wild-cards using *
    callback = function()
        -- set options/do stuff here
        vim.opt.shiftwidth = 2
    end,
})
```

## Disable autoformat

```vim
:lua vim.g.autoformat = false
```
