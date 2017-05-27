---
title: git 如何忽略不想提交的文件
date: 2017-05-27 09:29:26
tags: [git]
categories: [git]
---
例如在github，创建项目的时候会提示你创建`.gitignore`去忽略不需提交的文件

若项目已经创建忘记创建`.gitignore`文件，可以在代码目录下建立.gitignore文件：命令行中可以使用`vim .gitignore`创建并编辑文件
然后添加不想提交的文件，然后输入git status你会发现不想提交的文件不在变动文件中
然后调用git add. ，执行 git commit即可。注意：.gitignore只适用于尚未添加到git库的文件。如果已经添加了，则需用git rm移除后再重新commit。

