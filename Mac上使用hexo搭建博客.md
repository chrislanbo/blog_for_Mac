---
title: Mac上使用hexo搭建博客
date: 2017-05-27 09:03:46
tags: [hexo,搭建blog]
categories:
---


# Mac 上使用hexo搭建博客
安装node
到Node.js官网下载相应平台的最新版本
安装Git
//TODO 如何在mac上安装git
安装Xcode就自带有Git
申请GitHub
创建一个文件夹存放hexo配置文件
全局安装Hexo 
输入`sudo npm install -g hexo`
在文件夹目录下执行hexo init搭建默认博客

ssh-keygen -C 'your-email-address' -t rsa
一步一步生成密钥，然后去github添加ssh Key
或者将自己备份的.ssh文件移动到用户目录下（方便）
ssh -T git@github.com

直接
git clone git@github.com:chrislanbo/blog_for_Mac.git 
(不需要密码)https要输密码
git push -u origin master


