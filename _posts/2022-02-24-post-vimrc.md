---
title: "[VIM] my .vimrc file"
categories:
  - vim
tags:
  - vim
  - development
last_modified_at: 2022-03-10T22:00:00 +09:00
---

This is a summary of my vim settings.

![center-aligned-image](/images/vim/2022-03-10-my_vim_ver.png){: .align-center}

For vim or neovim, use **version 8.2 or higher**. There was a vulnerability issue in the previous version.
{: .notice--warning}

**refer to** <a href= "https://velog.io/@mythos/Linux-neovim-%EC%84%A4%EC%A0%95-CoC-Vim-Plug-treesitter-NERDTree" target="_black">this site</a>

Plugin
-----

- **vim ver** : 8.2
- **vim-plug**
    * vim plugin manager
    * should be installed first
    * Use `:Plug<Tab>` to check if it is installed properly in vim
    * <a href= "https://github.com/junegunn/vim-plug" target="_black">github</a>
- **coc.nvim**
    * depend on `NodeJS` ver 12.12 or higher
    * true snippet and additional text editing support
    * add LSP by `:CocInstall <LSP server name>`
    * <a href= "https://github.com/neoclide/coc.nvim/wiki/Language-servers#example-language-server-configuration" target="_black">LSP server list</a>
    * if use C, type `:CocInstall coc-clangd` and follow the procedure
    * if there is no `clangd`, This will command: `:CocCommand clangd.install` and `:CocCommand clangd.update`
    * <a href= "https://github.com/neoclide/coc.nvim" target="_black">github</a>
- **NERDTree**
    * file system explorer for the Vim editor
    * <a href= "https://github.com/preservim/nerdtree" target="_black">github</a>
- **tagbar**
    * browse the tags of the current file
    * get an overview of its structure
    * must install <a href= "https://github.com/universal-ctags/ctags" target="_black">ctags</a>
    * <a href= "https://github.com/preservim/tagbar" target="_black">github</a>
- **vim-airline**
    * nice statusline at the bottom of each vim window
    * managing other tabs
    * <a href= "https://github.com/vim-airline/vim-airline" target="_black">github</a>
- **ctrlp**
    * file managing system
    * <a href= "https://github.com/ctrlpvim/ctrlp.vim" target="_black">github</a>
- **vim-indent-guides**
    * visually displaying indent levels
    * <a href= "https://github.com/nathanaelkane/vim-indent-guides" target="_black">github</a>

## Install Plugin

1. add Plugin in `.vimrc`
2. type `:source %` in vim command
3. type `:PlugInstall` in vim command
4. check each github in more details

```
" plug-in setting
call plug#begin('~/.vim/plugged')

" plug-in
Plug 'neoclide/coc.nvim', {'branch': 'release'}

Plug 'preservim/nerdtree'

Plug 'preservim/tagbar'

Plug 'vim-airline/vim-airline'

Plug 'ctrlpvim/ctrlp.vim'

Plug 'nathanaelkane/vim-indent-guides'

call plug#end()
```

editor setting
-----
```
" Editor
"
" Syntax Highlighting
if has("syntax")
  syntax on
endif
 
" new lines inherit the indentation of previous lines
set autoindent

" c auto indent
set cindent

" numbering line
set number relativenumber

" hightlight matching parenthesis
set showmatch

" highlighting search
set hlsearch

" ignore case when searching
set ignorecase

" increase string when searching
set incsearch

" find exact string when search upper
set smartcase

" use an encoding that supports unicode
set encoding=utf-8

" always display the status bar
set laststatus=2

" status line config
" set statusline=\ %<%l:%v\ [%P]%=%a\ %h%m%r\ %F\
" show command
set showcmd

" Tab to space
set expandtab

" Tab to space = 2
set tabstop=2

" >> << to space = 2
set shiftwidth=2

" delete 2 space (recongize 2 space = 1 tab)
set softtabstop=2

" open files no fold
set nofoldenable

" set cursor
set ruler
set cul

" set cursor in last modified point
au BufReadPost * 
\ if line("'\"") > 0 && line("'\"") <= line("$") | 
\ exe "norm g`\"" | 
\ endif

" vim indent guides
let g:indent_guides_guide_size=1
let g:indent_guides_start_level=2
```
