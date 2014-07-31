---
layout: post
title:  "GVIM 编辑器 _vimrc 配置文件设置"
date:   2014-07-31 14:12:18
categories: linux
---
<pre>
<code>
set nocompatible
source $VIMRUNTIME/vimrc_example.vim
source $VIMRUNTIME/mswin.vim
behave mswin

"linzl add start
"gvim字体设置
set guifont=新宋体:h14:cGB2312

"gvim 内部编码
set encoding=utf-8

"当前编辑的文件编码
set fileencoding=utf-8

"gvim 打开支持编码文件
set fileencodings=ucs-bom,utf-8,gbk,cp936,gb2312,big5,latin1
"set langmenu=zh_CN
"let $LANG = 'zh_CN.UTF-8'
"解决consle输出乱码
language messages zh_CN.utf-8

"解决菜单乱码
source $VIMRUNTIME/delmenu.vim
source $VIMRUNTIME/menu.vim
"设置终端编码为gvim内部编码encoding
let &termencoding=&encoding
"防止特殊符号无法正常显示
set ambiwidth=double
"缩进尺寸为4个空格
set sw=4
"tab宽度为4个字符
set ts=4
"编辑时将所有的tab替换为空格
set et
"按一次退格就删4个空格
set smarttab
"不生成备份文件
set nobackup
"开启行号
set number
"关闭上侧工具栏
set guioptions-=T
"关闭右侧滚动条
set guioptions-=r
"开启自动缩进
set autoindent
"配色方案
colo ron
"linzl add end
set diffexpr=MyDiff()
function MyDiff()
  let opt = '-a --binary '
  if &diffopt =~ 'icase' | let opt = opt . '-i ' | endif
  if &diffopt =~ 'iwhite' | let opt = opt . '-b ' | endif
  let arg1 = v:fname_in
  if arg1 =~ ' ' | let arg1 = '"' . arg1 . '"' | endif
  let arg2 = v:fname_new
  if arg2 =~ ' ' | let arg2 = '"' . arg2 . '"' | endif
  let arg3 = v:fname_out
  if arg3 =~ ' ' | let arg3 = '"' . arg3 . '"' | endif
  let eq = ''
  if $VIMRUNTIME =~ ' '
    if &sh =~ '\<cmd'
      let cmd = '""' . $VIMRUNTIME . '\diff"'
      let eq = '"'
    else
      let cmd = substitute($VIMRUNTIME, ' ', '" ', '') . '\diff"'
    endif
  else
    let cmd = $VIMRUNTIME . '\diff'
  endif
  silent execute '!' . cmd . ' ' . opt . arg1 . ' ' . arg2 . ' > ' . arg3 . eq
endfunction
</code>
</pre>