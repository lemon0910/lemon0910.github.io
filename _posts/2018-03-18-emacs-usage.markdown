---
layout:     post
title:      emacs使用心得
date:       2018-03-18 21:23:00
author:     "lemon"
categories: 工具
---

学习了emacs一段时间，走完了从入门到放弃这条路，这条路走得很折腾，记录一下自己的心得体会。首先展示一下我的成果[lemon-emacs](https://github.com/lemon0910/lemon-emacs.d)。
对emacs感兴趣是因为大家说世界上只有两种编辑器：vim和emacs。vim学习使用了一段时间，自己已经很有心得，所以对emacs产生了兴趣，另一方面是因为大学时和朋友聊天说大牛都用emacs，emacs非常的强大，所以想体验一下到底和vim有什么不同。

## 1 emacs使用

第一次使用emacs实在mac下，确实足够简陋，感觉真是远古时代的东西，只是记住了"C-c C-x"是关闭。后来从网上发现了spacemacs，说只要会vim就可以很快迁移过来，所以使用了spacemacs的配置，确实可以完成大部分的操作。学习时吧SPC所有能触发的功能都看了一遍，确实比较容易记忆，比如"SPC b b"表示打开buffer，确实很好用。后面开始看子龙山人的emacs教学视频，因为之前有vim的配置经验，所以很快掌握了emacs的配置，并且开始尝试配置自己的.spacemacs文件，学习过程中把spacemacs的文档看了好几遍，也编写了自己的.spacemacs文件，能够做到基本的使用。

## 2 配置emacs

自己配置emacs参考了[seagle0128](https://github.com/seagle0128/.emacs.d) 和 [bbatsov](https://github.com/bbatsov/emacs.d) 。主要工作就是思考我在vim上有什么功能，在emacs如何配置实现，配置过程中掌握了很多插件的使用，并且对emacs和elisp有了新的，开始配的和vim很像，并且有更加强大的功能，如dired，magit，eshell等等，体会到了emacs的强大。现在在mac下就是使用emacs作为主力编辑器了。
配置vim时也参考了很多文章，如陈斌的一年成为emacs高手，从vim迁移到emacs，ivy和helm，abo-abo的博客，收获了很多，从他们身上也吸取了很多emacs和elisp相关的知识。

## 3 放弃emacs

我说放弃，主要是说我在服务器上放弃了使用emacs，主要有两方面原因：1，我觉得emacs在终端的很多配色有问题，自己总是不喜欢；2，emacs会卡。是的，我觉得用久了emacs比vim卡，当然没有具体验证，但确实是我的真实感受。所以最后又换回了vim。折腾一段时间，发现vim才是真爱，但我也是希望emacs越来越强大，因为我也喜欢emacs。
