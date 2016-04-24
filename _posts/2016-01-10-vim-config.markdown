---
layout:     post
title:      VIM配置
date:       2016-01-10 21:19:00
author:     "lemon"
categories: 工具
---

vim配置可以参考我的[github](https://github.com/lemon0910/lemon-vim)

## 配置步骤

首先要安装好git。
安装插件管理利器Vundle。Vundle的安装很简单：

	git clone http://github.com/gmarik/vundle.git ~/.vim/bundle/vundle

然后Bundle+插件名就可以很方便的安装了，具体方法后面介绍。写完要安装的插件后，就需要下面的命令了：

	"安装插件:
	:BundleInstall

	"更新插件:
	:BundleInstall

	"卸载不在列表中的插件:
	:BundleClean

有了Vundle，插件安装就变得很简单了，打开.vimrc文件，输入Bundle "插件名"，比如安装taglist，只需要加入一行Bundle 'vim-scripts/taglist.vim'即可。然后执行:BundleInstall便会自动安装。

## 插件介绍

### gmarik/vundle

必装，用于管理所有插件。

### scrooloose/nerdtree

必装，在VIM的编辑窗口树状显示文件目录。效果如下:

![nerdtree](/images/vim-config/1.png)

演示如下：

![nerdtreegif](/images/vim-config/2.gif)

通过配置快捷键，使用`<leader>+n`可以展示，`map <leader>+n :NERDTreeToggle<CR>`

### majutsushi/tagbar

必装,标签导航，显示文件中的函数和变量。效果如下：

![tag](/images/vim-config/3.png)

演示如下：

![tagbargif](/images/vim-config/4.gif)

配置`<leader>+t`用于展示tagbar。

### kien/ctrlp.vim

非常强大，可以根据关键词查找相应的文件。
演示如下：

![ctrlp](/images/vim-config/5.gif)

配置`<leader>+p`来唤醒该插件。

### Lokaltog/vim-powerline

用于美观状态栏，演示如下：

![powerline](/images/vim-config/6.png)

### kien/rainbow_parentheses.vim

括号高亮，演示如下：

![rainbow_parentheses](/images/vim-config/7.png)

### Lokaltog/vim-easymotion

必装，快速转到指定位置，提高效率神器。演示如下：

![easy-motion](/images/vim-config/8.gif)

设置`<leader>+<leader>+w`

### Valloric/YouCompleteMe

YCM是目前最好的自动补全插件，用起来非常的爽，大大提高了写代码的效率。安装的时候要求vim的版本在7.3.584 以上。具体的步骤请参考 [官方文档](https://github.com/Valloric/YouCompleteMe)。

### AWESOMEVIM

[vimawesome](http://vimawesome.com/)这个网站有很多vim插件的介绍和安装，可以多浏览选取自己喜欢的插件，自己多尝试才能选到自己喜欢的插件。

## 主题介绍

vim下面比较流行的两个主题是molokai和solarized。我比较喜欢molokai。
molokai主题：

![molokai](/images/vim-config/11.png)

solarized主题：

![solarized](/images/vim-config/10.png)

## .vimrc

.vimrc是vim的配置文件，配置信息都写在这个文件里面。点击[vimrc](https://github.com/lemon0910/lemon-vim/blob/master/vimrc)可以查看我的vim配置。

