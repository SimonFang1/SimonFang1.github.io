---
layout: post
title: Vim编辑器及其插件的配置
date: 2018-06-15
categories: blog
tags: [Vim, 开发环境配置]
description: 本文介绍在Ubuntu/Debian下，Vim编辑器及其插件的配置
---

## 开始体验编辑器之神
### Requirements
1. vim
2. git

不熟悉vim命令的新手，可以在终端运行vimtutor快速入门。
若不慎按下了Ctrl+s锁定了终端，可通过Ctrl+q解除锁定。

```bash
sudo apt install vim git # Ubuntu/Debian
vimtutor
```

## 安装[vim-plug](https://github.com/junegunn/vim-plug)

### 下载vim-plug
```bash
curl -fLo ~/.vim/autoload/plug.vim --create-dirs \
    https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```

### 配置.vimrc文件安装插件
.vimrc 文件是 Vim 程序启动后会自动执行的脚本文件(run command)，位于home目录下。
将想要安装的插件，将插件地址填写在vundle#begin和vundle#end之间就可以，插件地址填写的具体方法见[Vundle](https://github.com/VundleVim/Vundle.vim)项目说明文档。

用 vim 编辑 .vimrc，将其替换成如下脚本内容。
插件只是示例，更多的插件请自行探索，不需要的插件请自行删除。

```vim
set nocompatible              " be iMproved, required
filetype plugin indent on     " required

call plug#begin('~/.vim/plugged')
Plug 'Valloric/YouCompleteMe'
Plug 'scrooloose/syntastic'
Plug 'Raimondi/delimitMate'
Plug 'tpope/vim-fugitive'
Plug 'tpope/vim-surround'
Plug 'Yggdroot/indentLine' 
Plug 'scrooloose/nerdtree'
Plug 'scrooloose/nerdcommenter'
Plug 'terryma/vim-multiple-cursors'
Plug 'flazz/vim-colorschemes'
call plug#end()
" Put your non-Plugin stuff after this line
```

具体操作步骤为：
1. 复制脚本内容
2. vim .vimrc打开文件
3. :set paste进入粘贴模式（取消自动缩进，括号引号匹配等自动补全，:set nopaste可退出）
4. i进入INSERT模式，若文件非空，则用 ggVGc代替，gg意为移动光标到第一行，V进入VISUAL LINE模式，G移动光标到最后一行，c删除并进入INSERT模式
5. Ctrl+Shift+v粘贴脚本内容
6. Esc退出插入模式，回到NORMAL模式
7. :w 保存
8. 运行:source ~/.vimrc，或者退出并再次进入 vim
9. 运行 :PluginInstall，安装完成之后，插件就可以使用了。

### vim-plug的主要命令

|命令|作用|
|-|-|
|PlugStatus|检查插件状态|
|PlugUpdate|更新插件|
|PlugDiff|查看更改|
|PlugInstall|安装在配置文件中声明的插件|
|PlugClean|删除 vim 配置文件中所有未声明的插件|
|PlugUpgrade|升级vim-plug本身|

## 自定义配置
在.vimrc文件后追加以下内容
```vim
set nu
"set cc=80
set cursorline
set cursorcolumn
"set expandtab shiftwidth=2 tabstop=2 softtabstop=2
set autoindent
set nospell
colorscheme molokai

let mapleader=','
imap {<CR> {<CR>}<ESC>O

" 定位到上次退出时的位置
if has("autocmd")
  au BufReadPost * if line("'\"") > 1 && line("'\"") <= line("$") | exe "normal! g'\"" | endif
endif
```

## 配置[YouCompleteMe](https://github.com/Valloric/YouCompleteMe)插件

Ubuntu 系统和C/C++语言可运行如下命令：

```bash
sudo apt-get install build-essential cmake
sudo apt-get install python-dev python3-dev
cd ~/.vim/bundle/YouCompleteMe
git submodule update --init --recursive
python3 ./install.py --clang-completer
```

其他系统和语言参考 [https://github.com/Valloric/YouCompleteMe#installation](https://github.com/Valloric/YouCompleteMe#installation).

### 配置.vimrc文件
在.vimrc文件后追加以下内容
```vim
" for ycm
let g:ycm_auto_trigger = 1 
let g:ycm_error_symbol = '>>'
let g:ycm_warning_symbol = '>*'
let g:ycm_autoclose_preview_window_after_completion = 1 
let g:ycm_autoclose_preview_window_after_insertion = 1 
let g:ycm_global_ycm_extra_conf = "~/.vim/bundle/YouCompleteMe/third_party/ycmd/cpp/ycm/.ycm_extra_conf.py"
nnoremap <leader>gl :YcmCompleter GoToDeclaration<CR>
nnoremap <leader>gf :YcmCompleter GoToDefinition<CR>
nnoremap <leader>gg :YcmCompleter GoToDefinitionElseDeclaration<CR>
nnoremap <F4> :YcmDiags<CR>
inoremap <c-o> <c-x><c-o>
let g:ycm_semantic_triggers = { 
\     'c' : ['->', '.', '(', '[', '&'],
\     'cpp,objcpp' : ['->', '.', '(', '[', '&', '::'],
\     'perl' : ['->', '::', ' '],
\     'php' : ['->', '::', '.'],
\     'cs,java,javascript,d,vim,python,perl6,scala,vb,elixir,go' : ['.'],
\     'ruby' : ['.', '::'],
\     'lua' : ['.', ':']
\ }
```
