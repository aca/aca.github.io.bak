---
title: Speedup neovim
date: 2021-02-11T18:00
tags:
  - neovim
  - vim
  - timeline
---

I maintain the same neovim+tmux development environment in all my machines which also includes extremely slow free-tier cloud servers.
If you ever have experienced performance issues with neovim(or vim) on slow machines, here are some tips and tricks you might find useful.
Some of them are absolutely unnecessary if you are fine with your current configuration. But yes, overengineering workflow is always fun.

Here's my [vimrc](https://github.com/aca/dotfiles/tree/master/.config/nvim).

## Profile

First thing you should do to tackle performance issues is profiling.
Vim includes a native profiling system. Check `:help profile`, `:help --startuptime`. But
most of the performance issue is related to plugin's startuptime and you might want to check [vim-startuptime](https://github.com/rhysd/vim-startuptime) for this.

```
$ vim-startuptime -vimpath nvim 
Extra options: []
Measured: 10 times

Total Average: 32.805900 msec
Total Max:     33.308000 msec
Total Min:     32.470000 msec

  AVERAGE       MAX       MIN
------------------------------
13.683300 13.860000 13.560000: /home/rok/.config/nvim/init.vim
 4.965100  5.058000  4.902000: /usr/share/nvim/runtime/filetype.vim
 3.421500  3.514000  3.363000: reading ShaDa
 1.750200  1.808000  1.724000: loading plugins
 1.682500  1.755000  1.624000: loading packages
```

Find functions, plugins that slow down startup. Running tmux in background, I run vim per projects.
As I frequently start/exit whole vim instance(which is really bad habit that I just don't fix), I try to maintain startuptime under 50 msec.

## Disable standard plugins

Did you ever use netrw, archive view in vim, matchparen(which is based on regex)? 
If not, you should disable all of it. There are better alternatives which is better in most ways and can be lazyloaded.

- [vim-dirvish](https://github.com/justinmk/vim-dirvish)
- [xdg_open](https://github.com/arp242/xdg_open.vim) for netrw-gx
- [vim-matchup](https://github.com/andymass/vim-matchup)

```
let g:loaded_matchparen        = 1
let g:loaded_matchit           = 1
let g:loaded_logiPat           = 1
let g:loaded_rrhelper          = 1
let g:loaded_tarPlugin         = 1
" let g:loaded_man               = 1
let g:loaded_gzip              = 1
let g:loaded_zipPlugin         = 1
let g:loaded_2html_plugin      = 1
let g:loaded_shada_plugin      = 1
let g:loaded_spellfile_plugin  = 1
let g:loaded_netrw             = 1
let g:loaded_netrwPlugin       = 1
let g:loaded_tutor_mode_plugin = 1
let g:loaded_remote_plugins    = 1
```
## Visual settings

These kinds of visual helpers cause too much screen redraw and cause significant lag. Turn it on when you need it.
```
set nonumber
set norelativenumber
set nocursorcolumn
set nocursorline
```

Syntax highlighting of vim uses regex match. I believe it is one of the slowest
operations in vim but there's not much we can do with it. But these options
might help a bit. And, if syntax file is slow, `foldmethod=syntax` might also
cause issue if you are editing huge file. 

```
set synmaxcol=200
set lazyredraw

" Change foldmethod for specific filetype
au! BufNewFile,BufRead *.json set foldmethod=indent
```
Hope [treesitter](https://github.com/nvim-treesitter/nvim-treesitter) fix this in the future.

---

Many colorscheme plugins cause extreme slow startuptime. Check [u/-romainl- 's comment](https://www.reddit.com/r/vim/comments/gc05k1/why_are_colorschemes_so_slow_to_load/fp92t47?utm_source=share&utm_medium=web2x&context=3) on this. 
Color scheme based on these frameworks is recommended.
- [vim-colortemplate](https://github.com/lifepillar/vim-colortemplate) 
- [vim-rnb](https://github.com/romainl/vim-rnb)

If you like monokai, this is my own version of it. [vim-monokai-pro](https://github.com/aca/vim-monokai-pro). Here's startuptime.

```
AVERAGE   MAX       MIN 
0.474000  0.724000  0.305000
```


## Plugins

Vim supports lazy loading with "autoload". Plugins should not slow down your startup time if it's written in proper way.
But many of them, even famous ones, doesn't consider startuptime issue seriously. Now vim has `:packadd`  which is vim's native package loading system. You can circumvent this issues without fixing plugin itself.

But first of all, you should use plugin manager that works well with `:packadd` instead of famous [vim-plug](https://github.com/junegunn/vim-plug).
I use [savq/paq-nvim](https://github.com/savq/paq-nvim) written in lua. With only much much readable 150 LOC, it supports all the features like 
`:PaqInstall`,`:PaqClean`, `:PaqUpdate`. The most amazing part of it is that you can even make paq itself optional.

And with packadd you can do things like

```
" fzf/fzf.vim
nnoremap <silent><leader>rg :packadd fzf \| :packadd fzf.vim \| :Rg<cr>

" markdown-preview.nvim
command! MarkdownPreview packadd markdown-preview.nvim | call mkdp#util#open_preview_page()"

" phaazon/hop.nvim
nmap <silent><Leader>w :packadd hop.nvim \| :lua require'hop'.jump_words()<cr>
```

With these tricks, I could reduce 20ms of startuptime compared to the vim-plug setup. This kind of setup was not was easy to configure with vim-plug's lazyloading features.
And I felt that plug itself get quite slow with huge, complex configs.

In terms of the plugin itself, many of them are newly developed or rewritten from scratch(in lua) by awesome developers everyday.
And neovim is adopting many features of plugins into the core, like [lsp](https://neovim.io/doc/user/lsp.html) and [treesitter](https://github.com/nvim-treesitter/nvim-treesitter).
You might want to check [r/neovim](https://www.reddit.com/r/neovim), [gitter/neovim](https://gitter.im/neovim/neovim) for any updates.



