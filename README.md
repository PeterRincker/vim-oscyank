# vim-oscyank

A Vim / Neovim plugin to copy text to the system clipboard from anywhere using
the [ANSI OCS52](https://invisible-island.net/xterm/ctlseqs/ctlseqs.html#h3-Operating-System-Commands)
sequence.

When this sequence is emitted by Vim, the terminal will copy the given text into
the system clipboard. **This is totally location independent**, users can copy
from anywhere including from remote SSH sessions.

The only requirement is that the terminal must support the sequence. Here is a
non-exhaustive list of the status of popular terminal emulators regarding OSC52
(as of May 2021):

| Terminal | OCS52 support |
|----------|:-------------:|
| [Alacritty](https://github.com/alacritty/alacritty) | **yes** |
| [GNOME Terminal](https://github.com/GNOME/gnome-terminal) (and other VTE-based terminals) | [not yet](https://bugzilla.gnome.org/show_bug.cgi?id=795774) |
| [hterm (Chromebook)](https://chromium.googlesource.com/apps/libapps/+/master/README.md) | [**yes**](https://chromium.googlesource.com/apps/libapps/+/master/nassh/doc/FAQ.md#Is-OSC-52-aka-clipboard-operations_supported) |
| [iTerm2](https://iterm2.com/) | **yes** |
| [kitty](https://github.com/kovidgoyal/kitty) | [**yes**](https://sw.kovidgoyal.net/kitty/protocol-extensions.html#pasting-to-clipboard) |
| [screen](https://www.gnu.org/software/screen/) | **yes** |
| [tmux](https://github.com/tmux/tmux) | **yes** |
| [Windows Terminal](https://github.com/microsoft/terminal) | **yes** |
| [rxvt](http://rxvt.sourceforge.net/) | **yes** (to be confirmed) |
| [urxvt](http://software.schmorp.de/pkg/rxvt-unicode.html) | **yes** (with a script, see [here](https://github.com/ojroques/vim-oscyank/issues/4)) |

## Installation
Using [vim-plug](https://github.com/junegunn/vim-plug):
```vim
Plug 'ojroques/vim-oscyank'
```

## Usage
Enter Visual mode, select your text and run `:OSCYank`.

You may want to map the command:
```vim
xmap <leader>y  <Plug>(oscyank)
nmap <leader>y  <Plug>(oscyank)
nmap <leader>yy <Plug>(oscyank-yank)
```

These are the default mappings. Use `let g:oscyank_no_mappings = 1` to prevent default mappings.

If you prefer to copy text from a particular register, use:
```vim
:OSCYankReg +  " this will copy text from register '+'
```

For the impatient one, copy this line to your config. Content will be copied to
clipboard after any yank operation:
```vim
autocmd TextYankPost * if v:event.operator is 'y' && v:event.regname is '' | OSCYankReg " | endif
```

Or to copy to clipboard the `+` register (vim's *system clipboard* register):
```vim
autocmd TextYankPost * if v:event.operator is 'y' && v:event.regname is '+' | OSCYankReg + | endif
```

## Configuration
By default you can copy up to 100000 characters at once. If your terminal
supports it, you can raise that limit with:
```vim
let g:oscyank_max_length = 1000000
```

The plugin treats *tmux*, *screen* and *kitty* differently than other terminal
emulators. The plugin should automatically detects the terminal used but you can
bypass detection with:
```vim
let g:oscyank_term = 'tmux'  " or 'screen', 'kitty', 'default'
```

## Features
There are already Vim plugins implementing OSC52. However this plugin fixes
several issues I've had with them:
* It supports Neovim.
* It supports Windows.
* It does not mandate users to overwrite their unnamed register (`"`).
* It makes the maximum length of strings configurable.
* It supports [kitty](https://github.com/kovidgoyal/kitty) which has a
  [slightly modified OSC52 protocol](https://sw.kovidgoyal.net/kitty/protocol-extensions.html#pasting-to-clipboard)
  by default.

## Other terminals with OSC52 support
Other terminals that support OSC52:

| Terminal | OCS52 support |
|----------|:-------------:|
| [foot](https://codeberg.org/dnkl/foot) | **yes** |
| [wezterm](https://github.com/wez/wezterm) | [**yes**](https://wezfurlong.org/wezterm/escape-sequences.html#operating-system-command-sequences) |

Feel free to add terminals to this list by submitting a pull request.

## Credits
The code is derived from
[hterm's script](https://github.com/chromium/hterm/blob/master/etc/osc52.vim).

[hterm's LICENCE](https://github.com/chromium/hterm/blob/master/LICENSE)<br/>
[LICENSE](LICENSE)
