---
title: php
date: 2018-02-11 10:41:25
tags: [php]
categories: [php]
---

## PHP 能够做什么？

- PHP 能够生成动态页面内容
- PHP 能够创建、打开、读取、写入、删除以及关闭服务器上的文件
- PHP 能够接收表单数据
- PHP 能够发送并取回 cookies
- PHP 能够添加、删除、修改数据库中的数据
- PHP 能够限制用户访问网站中的某些页面
- PHP 能够对数据进行加密
- PHP 脚本在服务器上执行，结果已纯文本 (纯HTML、json格式、)、图像、PDF 文件、甚至 Flash 影片发送回浏览器。



## 基础 PHP 语法

PHP 脚本可放置于文档中的任何位置。

PHP 脚本以 *<?php* 开头，以 *?>* 结尾：

PHP 支持三种注释：

PHP 文件的默认文件扩展名是 ".php"。

PHP 文件通常包含 HTML 标签以及一些 PHP 脚本代码。

下面的例子是一个简单的 PHP 文件，其中包含了使用内建 PHP 函数 "echo" 在网页上输出文本 "Hello World!" 的一段 PHP 脚本：

```
<!DOCTYPE html>
<html>
<body>
<h1>我的第一张 PHP 页面</h1>

<?php
// 这是单行注释

# 这也是单行注释
echo "Hello World!";
/*
这是多行注释块
它横跨了
多行
*/
?>

</body>
</html>
```

注释：PHP 语句以分号结尾（;）。PHP 代码块的关闭标签也会自动表明分号（因此在 PHP 代码块的最后一行不必使用分号）。



## PHP 大小写敏感

在 PHP 中，自定义的函数、类和关键词（例如 if、else、echo 等等）都对大小写不敏感。

在下面的例子中，所有这三条 echo 语句都是合法的（等价）：

### 实例

```
<!DOCTYPE html>
<html>
<body>

<?php
ECHO "Hello World!<br>";
echo "Hello World!<br>";
EcHo "Hello World!<br>";
?>

</body>
</html>

```

[运行实例](http://www.w3school.com.cn/tiy/s.asp?f=demo_php_syntax_case1)

所有变量都对大小写敏感。

在下面的例子中，只有第一条语句会显示 $color 变量的值（这是因为 $color、$COLOR 以及 $coLOR 被视作三个不同的变量）：

### 实例

```
<!DOCTYPE html>
<html>
<body>

<?php
$color="red";
echo "My car is " . $color . "<br>";
echo "My house is " . $COLOR . "<br>";
echo "My boat is " . $coLOR . "<br>";
?>

</body>
</html>

```

[运行实例](http://www.w3school.com.cn/tiy/s.asp?f=demo_php_syntax_case2)



## PHP 变量

PHP 变量规则：

- 变量以 $ 符号开头，其后是变量的名称
- 变量名称必须以字母或下划线开头，不能以数字开头
- 变量名称只能包含字母数字字符和下划线（`A-z、0-9 以及 _`）
- 变量名称对大小写敏感（$y 与 $Y 是两个不同的变量）

> 注释：PHP 变量名称对大小写敏感！

####PHP 变量作用域

变量的作用域指的是变量能够被引用/使用区域。

PHP 有三种不同的变量作用域：

- local（局部）
- global（全局）
- static（静态）

####Local 和 Global 作用域

函数*之外*声明的变量拥有 Global 作用域，只能在函数以外进行访问。函数内部不能直接访问（和java不同）

函数*内部*声明的变量拥有 LOCAL 作用域，只能在函数内部进行访问。

下面的例子测试了带有局部和全局作用域的变量：

```
<?php
$x=5; // 全局作用域

function myTest() {
  $y=10; // 局部作用域
  echo "<p>测试函数内部的变量：</p>";
  echo "变量 x 是：$x";
  echo "<br>";
  echo "变量 y 是：$y";
} 

myTest();

echo "<p>测试函数之外的变量：</p>";
echo "变量 x 是：$x";
echo "<br>";
echo "变量 y 是：$y";
?>

```



global 关键词用于访问函数内的全局变量

要做到这一点，请在（函数内部）变量前面使用 global 关键词：实例

```
<?php
$x=5;
$y=10;

function myTest() {
  global $x,$y;
  $y=$x+$y;
}

myTest();
echo $y; // 输出 15
?>

```

PHP 同时在名为 $GLOBALS[index] 的数组中存储了所有的全局变量。下标存有变量名。这个数组在函数内也可以访问，并能够用于直接更新全局变量。 

```php
<?php
$x=5;
$y=10;

function myTest() {
  $GLOBALS['y']=$GLOBALS['x']+$GLOBALS['y'];
} 

myTest();
echo $y; // 输出 15
?>

```

####PHP static 关键词

通常，当函数完成/执行后，会回收所有局部变量。

```php
<?php

function myTest() {
  $x=1;
  echo $x;
  $x++;
}

myTest(); // 1
myTest(); // 1
myTest(); // 1
?>
```



不过，有时我需要不删除某个局部变量。实现这一点需要更进一步的工作。

要完成这一点，请在您首次声明变量时使用 *static* 关键词：

```php
<?php

function myTest() {
  static $x=0;
  echo $x;
  $x++;
}

myTest(); // 0
myTest(); // 1
myTest(); // 2
?>
```

然后，每当函数被调用时，这个变量所存储的信息都是函数最后一次被调用时所包含的信息。

> 注释：该变量仍然是函数的局部变量。