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
set encoding=utf-8
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
cd ~/.vim/plugged/YouCompleteMe
git submodule update --init --recursive
python3 install.py --clang-completer
```

其他系统和语言参考 [https://github.com/Valloric/YouCompleteMe#installation](https://github.com/Valloric/YouCompleteMe#installation).

### 配置.ycm_extra_conf.py
如果"~/.vim/plugged/YouCompleteMe/third_party/ycmd/cpp/ycm/"目录下没有.ycm_extra_conf.py文件，添加以下内容

```python
# This file is NOT licensed under the GPLv3, which is the license for the rest
# of YouCompleteMe.
#
# Here's the license text for this file:
#
# This is free and unencumbered software released into the public domain.
#
# Anyone is free to copy, modify, publish, use, compile, sell, or
# distribute this software, either in source code form or as a compiled
# binary, for any purpose, commercial or non-commercial, and by any
# means.
#
# In jurisdictions that recognize copyright laws, the author or authors
# of this software dedicate any and all copyright interest in the
# software to the public domain. We make this dedication for the benefit
# of the public at large and to the detriment of our heirs and
# successors. We intend this dedication to be an overt act of
# relinquishment in perpetuity of all present and future rights to this
# software under copyright law.
#
# THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND,
# EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
# MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT.
# IN NO EVENT SHALL THE AUTHORS BE LIABLE FOR ANY CLAIM, DAMAGES OR
# OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE,
# ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR
# OTHER DEALINGS IN THE SOFTWARE.
#
# For more information, please refer to <http://unlicense.org/>

from distutils.sysconfig import get_python_inc
import platform
import os
import ycm_core

# These are the compilation flags that will be used in case there's no
# compilation database set (by default, one is not set).
# CHANGE THIS LIST OF FLAGS. YES, THIS IS THE DROID YOU HAVE BEEN LOOKING FOR.
flags = [
'-Wall',
'-Wextra',
'-Werror',
'-Wno-long-long',
'-Wno-variadic-macros',
'-fexceptions',
'-DNDEBUG',
# You 100% do NOT need -DUSE_CLANG_COMPLETER and/or -DYCM_EXPORT in your flags;
# only the YCM source code needs it.
'-DUSE_CLANG_COMPLETER',
'-DYCM_EXPORT=',
# THIS IS IMPORTANT! Without the '-x' flag, Clang won't know which language to
# use when compiling headers. So it will guess. Badly. So C++ headers will be
# compiled as C headers. You don't want that so ALWAYS specify the '-x' flag.
# For a C project, you would set this to 'c' instead of 'c++'.
'-x',
'c++',
'-isystem',
'../pybind11',
'-isystem',
'../BoostParts',
'-isystem',
get_python_inc(),
'-isystem',
'../llvm/include',
'-isystem',
'../llvm/tools/clang/include',
'-I',
'.',
'-I',
'./ClangCompleter',
'-I',
'/usr/include/c++/5.4.0',
'-I',
'/usr/local/include',
'-I',
'/usr/include',
'-I',
'/usr/include/x86_64-linux-gnu',
'-I',
'/usr/lib/llvm-3.8/lib/clang/3.8.0/include',
'-isystem',
'./tests/gmock/gtest',
'-isystem',
'./tests/gmock/gtest/include',
'-isystem',
'./tests/gmock',
'-isystem',
'./tests/gmock/include',
'-isystem',
'./benchmarks/benchmark/include',
]

# Clang automatically sets the '-std=' flag to 'c++14' for MSVC 2015 or later,
# which is required for compiling the standard library, and to 'c++11' for older
# versions.
if platform.system() != 'Windows':
  flags.append( '-std=c++11' )


# Set this to the absolute path to the folder (NOT the file!) containing the
# compile_commands.json file to use that instead of 'flags'. See here for
# more details: http://clang.llvm.org/docs/JSONCompilationDatabase.html
#
# You can get CMake to generate this file for you by adding:
#   set( CMAKE_EXPORT_COMPILE_COMMANDS 1 )
# to your CMakeLists.txt file.
#
# Most projects will NOT need to set this to anything; you can just change the
# 'flags' list of compilation flags. Notice that YCM itself uses that approach.
compilation_database_folder = ''

if os.path.exists( compilation_database_folder ):
  database = ycm_core.CompilationDatabase( compilation_database_folder )
else:
  database = None

SOURCE_EXTENSIONS = [ '.cpp', '.cxx', '.cc', '.c', '.m', '.mm' ]

def DirectoryOfThisScript():
  return os.path.dirname( os.path.abspath( __file__ ) )


def IsHeaderFile( filename ):
  extension = os.path.splitext( filename )[ 1 ]
  return extension in [ '.h', '.hxx', '.hpp', '.hh' ]


def FindCorrespondingSourceFile( filename ):
  if IsHeaderFile( filename ):
    basename = os.path.splitext( filename )[ 0 ]
    for extension in SOURCE_EXTENSIONS:
      replacement_file = basename + extension
      if os.path.exists( replacement_file ):
        return replacement_file
  return filename


def FlagsForFile( filename, **kwargs ):
  # If the file is a header, try to find the corresponding source file and
  # retrieve its flags from the compilation database if using one. This is
  # necessary since compilation databases don't have entries for header files.
  # In addition, use this source file as the translation unit. This makes it
  # possible to jump from a declaration in the header file to its definition in
  # the corresponding source file.
  filename = FindCorrespondingSourceFile( filename )

  if not database:
    return {
      'flags': flags,
      'include_paths_relative_to_dir': DirectoryOfThisScript(),
      'override_filename': filename
    }

  compilation_info = database.GetCompilationInfoForFile( filename )
  if not compilation_info.compiler_flags_:
    return None

  # Bear in mind that compilation_info.compiler_flags_ does NOT return a
  # python list, but a "list-like" StringVec object.
  final_flags = list( compilation_info.compiler_flags_ )

  # NOTE: This is just for YouCompleteMe; it's highly likely that your project
  # does NOT need to remove the stdlib flag. DO NOT USE THIS IN YOUR
  # ycm_extra_conf IF YOU'RE NOT 100% SURE YOU NEED IT.
  try:
    final_flags.remove( '-stdlib=libc++' )
  except ValueError:
    pass

  return {
    'flags': final_flags,
    'include_paths_relative_to_dir': compilation_info.compiler_working_dir_,
    'override_filename': filename
  }
```

### 配置.vimrc文件
在.vimrc文件后追加以下内容
```vim
" for ycm
let g:ycm_auto_trigger = 1 
let g:ycm_error_symbol = '>>'
let g:ycm_warning_symbol = '>*'
let g:ycm_autoclose_preview_window_after_completion = 1 
let g:ycm_autoclose_preview_window_after_insertion = 1 
let g:ycm_global_ycm_extra_conf = "~/.vim/plugged/YouCompleteMe/third_party/ycmd/cpp/ycm/.ycm_extra_conf.py"
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
