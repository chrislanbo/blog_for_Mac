---
title: react-native环境搭建
date: 2017-11-08 10:13:20
tags: [react-native]
categories: [react-native]
---

MAC
没安装Homebrew的看
>Mac系统的包管理器，安装软件方便

    /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

>译注：在Max OS X 10.11（El Capitan)版本中，homebrew在安装软件时可能会碰到/usr/local目录不可写的权限问题。可以使用下面的命令修复：

    sudo chown -R `whoami` /usr/local

没安装node、npm的看

    brew install node

设置npm镜像加速

    npm config set registry https://registry.npm.taobao.org --global
    npm config set disturl https://npm.taobao.org/dist --global


安装Yarn、React Native的命令行工具

>Yarn是Facebook提供的替代npm的工具，可以加速node模块的下载。React Native的命令行工具用于执行创建、初始化、更新项目、运行打包服务（packager）等任务。

    npm install -g yarn react-native-cli

设置yarn镜像加速

    yarn config set registry https://registry.npm.taobao.org --global
    yarn config set disturl https://npm.taobao.org/dist --global
    
>如果你看到EACCES: permission denied这样的权限报错，那么请参照上文的homebrew译注，修复/usr/local目录的所有权：

    sudo chown -R `whoami` /usr/local
    
>安装完yarn之后就可以用yarn代替npm了，例如用yarn代替npm install命令，用yarn add 某第三方库名代替npm install --save 某第三方库名。

初始化项目
>init命令默认会创建最新的版本，而目前最新的0.45及以上版本需要下载boost库编译。此库体积庞大，在国内即便翻墙也很难下载成功，导致很多人无法正常运行iOS项目，中文网在论坛中提供了这些库的国内[下载链接](http://bbs.reactnative.cn/topic/4301/ios-rn-0-45%E4%BB%A5%E4%B8%8A%E7%89%88%E6%9C%AC%E6%89%80%E9%9C%80%E7%9A%84%E7%AC%AC%E4%B8%89%E6%96%B9%E7%BC%96%E8%AF%91%E5%BA%93-boost%E7%AD%89)。如果你嫌麻烦，又没有对新版本的需求，那么可以暂时创建0.44.3的版本。

    react-native init MyApp --version 0.44.3
    cd AwesomeProject
    react-native run-ios

    react-native init MyApp --version 0.49.3


