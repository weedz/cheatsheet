# Tmux cheat-sheet

Some good stuff here, [Tmux Cheat Sheet](https://tmuxcheatsheet.com/).

View key binds with `CTRL+B` `?`. (`M+2` means "meta"+2. "meta" is usually `ALT`.)

## Split

- Horizontally: `CTRL+b` `%`
- Vertically: `CTRL+b` `"`

Make all panes even size with:

- `CTRL+B` `M+1` for horizontal (will also set the layout to horizontal)
- `CTRL+B` `M+2` for vertical (will also set the layout to vertical)

## Copy mode (and scrolling)

Enter "copy mode" with `CTRL+b` `[` or `CTRL+b` `PGUP`.

Exit "copy mode" with `q`.

Select text with `CTRL+SPACE`. Copy text and exit "copy mode" with `Alt+w`.

## Organize windows

Break/move active pane into a new window: `CTRL+b`+`!`.

Join a pane into a window with `join-pane`. `CTRL+b` `:` then enter `join-pane -s 1 -t 2`.
Joining can be easier by "marking" a pane (`CTRL+b` `m`) and then moving to the window
where we want the pane and do `join-pane` without arguments.

Toggle pane layout between vertical/horizontal with `CTRL+b` `<space>`.
