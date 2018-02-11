---
title: JavaScript权威指南学习笔记02
date: 2017-11-09 16:37:54
tags: [JavaScript]
categories: [JavaScript]
---


###字符集
JavaScript是用Unicode字符集编写的

#### 区分大小写

JavaScript是区分大小写的，但是HTML是不区分大小写的（XHTML是区分的）

#### 算数运算、二进制浮点数和四舍五入错误
JavaScript预定义了Infinity和NaN表示正无穷大和非数字值。

    Infinity
    Number.POSITIVE_INFINITY
    1/0
    Number.MAX_VALUE + 1
    
    Number.NEGATIVE_INFINITY
    -Infinity
    -1/0
    -Number.MAX_VALUE - 1
    
    NaN  // 将可读写的变量初始化为NaN
    Number.NaN
    0/0 // NaN
    
    Number.MAX_VALUE/2 //下溢 结果是0
    -Number.MAX_VALUE/2 // -0
    -1/Infinity // -0
    -0


实数有无数个，但是JavaScript通过浮点数的性质只能表现有限的个数（其实是真实值的一个近似表示

    .3-.2 不等于.1 计算0.09999999999999998
    .2-.1 等于 .1
    
0.3和0.2之间的近似差值实际上并不等于0.2和0.1之间的近似差值。所以尽量使用大整数进行重要的金融计算    



####日期和时间

JavaScript语言核心包括Date()构造函数


    var now = new Date() // 当前时间，月份从0开始到11
    var then = new Date(2017, 10, 19) // Wed Nov 11 2017 00:00:00 GMT+0800 (CST)
    var then2 = new Date(2017, 10, 11, 17, 10, 30) // Wed Nov 11 2017 17:10:30 GMT+0800 (CST)
    var day = then.getDay() // 获取星期（0-6） 0是星期日

####字符串操作

    var s = 'hello,world'
    s.charAt(0) // 第一个字符
    s.charAt(s.length - 1) // 最后一个字符 
    s.substring(1, 4) // 截取第2~4位置字符
    s.slice(1, 4) // 同上
    s.slice(-3) // 截取最后三个字符
    s.indexOf('l') // l首次出现的位置
    s.indexOf('l', 3) // 3位置往后，l首次出现的位置
    s.split(',') // 分割字符串
    s.replace('h', 'H') // 全文替换
    s.toUpperCase() // 大小写

####正则匹配

    var s = 'testing:1, 2, 3'
    var pattern = /\d+/g // 匹配所有包含一个或多个数字的实例
    pattern.test(s) // true 匹配成功
    s.search(pattern) // 首次匹配到数字的位置
    s.match(pattern) // 匹配成功结果组成的数组
    s.replace(pattern, '#') // 将匹配到结果替换

####null和undefined
null是JavaScript的关键字，但其类型为 object ，理解null为一个特殊的对象值，含义是非对象

#####全局对象

全局属性 undefined、Infinity、NaN
全局函数 isNaN parseInt
构造函数 Date RegExp String Object Array
全局对象 Math window

####包装对象

字符串 数字 布尔值 创建的时候，都是会调用各自的构造函数创建一个临时对象，所以才可以调用方法，但是null和undefined没有包装对象，访问他们的属性会造成一个类型错误

####变量作用域***

通常语言中，花括号内的每一段代码都有各自的作用域，而且变量在声明他们的代码段之外不可见。和其他语言不同，JavaScript是没有块级作用域，取而代之的时函数作用域。

    function test() {
        var i = 0;
        if (true) {
          var j = 0;
          for (var k=0; k<10;k++) {
        
          }
        }
    }

上述代码 变量 i，j，k 其实都在同一个作用域内，函数作用域是指在函数内申明的所有变量在函数体内始终共享，这就导致了变量在申明之前就已经可以使用了

