---
title: JavaScript权威指南学习笔记01
date: 2017-11-08 16:16:33
tags: [JavaScript]
categories: [JavaScript]
---

前端必须掌握的技能

1. 描述网页内容的html
2. 描述网页样式的css
3. 描述网页行为的JavaScript

JavaScript 是高端、动态、弱类型的语言，适合面向对象和函数式的编程风格

JavaScript的语法源自Java，它的一等函数来自Scheme，它的基于原型的继承来自Self

JavaScript集健壮性、高效性和通用性


###1.1 语言核心

除了基本数据类型,数组和对象也是js的数据类型

对象是键值对的集合，或字符串到值映射的集合,用花括号包起来，

```javaScript
var book = {
    topic: "js",
    isApp: true
}
```

通过`.` 和 `[]`来访问对象属性

```javaScript
book.topic  // => "js"
book["isApp"] // => true
book.author = "lanbo" //通过赋值创建了一个新的对象
book.contents = {};  // {}是一个空对象，没有属性
```

JavaScript同样支持数组

```javaScript
var arrs = [2,3,5,7]
arrs[4] = 11 // 通过赋值来添加新的元素
arrs[1] = 9 // 通过赋值改变已有元素
var empty = []
```

>数组和对象都可以包含另一个数组或对象


###方法
`this`关键字是对定义方法对象的引用


