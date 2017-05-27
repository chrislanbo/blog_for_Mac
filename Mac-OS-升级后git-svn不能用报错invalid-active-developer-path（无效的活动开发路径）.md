---
title: Mac OS 升级后git/svn不能用报错invalid active developer path（无效的活动开发路径）
date: 2017-05-27 09:04:27
tags:
categories:
---


# Mac OS 升级后git/svn不能用报错invalid active developer path（无效的活动开发路径）

报错内容：

    invalid active developer path (/Library/Developer/  CommandLineTools), missing xcrun at: /Library/Developer/CommandLineTools/usr/bin/xcrun

解决：

    进入命令行`Terminal`
    输入`xcode-select --install `
    根据提示一点一点安装即可

