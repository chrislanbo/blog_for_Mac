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



## PHP echo 和 print 输出语句

echo 和 print 之间的差异：

- echo - 能够输出一个以上的字符串
- print - 只能输出一个字符串，并始终返回 1

提示：echo 比 print 稍快，因为它不返回任何值。

##PHP 数据类型

**字符串、整数、浮点数、逻辑、数组、对象、NULL。**

string、int 、float、bool、array、NULL



**字符串**可以是引号内的任何文本。您可以使用单引号或双引号

整数是没有小数的数字

整数规则：

- 整数必须有至少一个数字（0-9）
- 整数不能包含逗号或空格
- 整数不能有小数点
- 整数正负均可
- 可以用三种格式规定整数：十进制、十六进制（前缀是 0x）或八进制（前缀是 0）

在下面的例子中，我们将测试不同的数字。PHP var_dump() 会返回变量的数据类型和十进制的值

```
<?php 
$x = 5985;
var_dump($x);
echo "<br>"; 
$x = -345; // 负数
var_dump($x);
echo "<br>"; 
$x = 0x8C; // 十六进制数
var_dump($x);
echo "<br>";
$x = 047; // 八进制数
var_dump($x);
?>
```

**PHP 浮点数**

浮点数是有小数点或指数形式的数字。

在下面的例子中，我们将测试不同的数字。PHP var_dump() 会返回变量的数据类型和值：

```
<?php 
$x = 10.365;
var_dump($x);
echo "<br>"; 
$x = 2.4e3;
var_dump($x);
echo "<br>"; 
$x = 8E-5;
var_dump($x);
?>
```

**PHP 逻辑**

逻辑是 true 或 false。

```
$x=true;
$y=false;

```

逻辑常用于条件测试。您将在本教程稍后的章节学到更多有关条件测试的知识。

**PHP 数组**

数组在一个变量中存储多个值。

在下面的例子中，我们将测试不同的数组。PHP var_dump() 会返回变量的数据类型和值：

```php
<?php 
$cars=array("Volvo","BMW","SAAB");
var_dump($cars); // array(3) { [0]=> string(5) "Volvo" [1]=> string(3) "BMW" [2]=> string(4) "SAAB" }
?>
```

**PHP 对象**

对象是存储数据和有关如何处理数据的信息的数据类型。

在 PHP 中，必须明确地声明对象。

首先我们必须声明对象的类。对此，我们使用 class 关键词。类是包含属性和方法的结构。

然后我们在对象类中定义数据类型，然后在该类的实例中使用此数据类型：

```php
<?php
class Car
{
  var $color;
  function Car($color="green") {
    $this->color = $color;
  }
  function what_color() {
    return $this->color;
  }
}
function print_vars($obj) {
   foreach (get_object_vars($obj) as $prop => $val) {
     echo "\t$prop = $val\n";
   }
}

// instantiate one object
$herbie = new Car("white");
echo "herbie: Properties\n";
print_vars($herbie); //herbie: Properties color = white
?>
```

**PHP NULL 值**

 NULL 值表示变量无值。NULL 是数据类型 NULL 唯一可能的值。

NULL 值标示变量是否为空。也用于区分空字符串与空值数据库。

可以通过把值设置为 NULL，将变量清空：

```PHP
<?php
$x="Hello world!";
$x=null;
var_dump($x);
?>

```

##PHP 字符串函数

> 转义字符需要用双引号

int strlen() 函数返回字符串和数字的长度，输入有效数字也会返回长度，

```php
<?php
echo strlen("Hello world!");
echo "\n";
echo strlen(036);
echo "\n";
echo strlen(0000036);
echo "\n";
echo strlen(1036);
echo "\n";
echo strlen(1.036);
echo "\n";
echo strlen(000.1036);
echo "\ncccc:";
echo strlen(cccc);
echo "\n";
echo strlen();
?>
```

```php
12
2
2
4
5
6
cccc:4
PHP Notice:  Use of undefined constant cccc - assumed 'cccc' in ..... on line 14
PHP Warning:  strlen() expects exactly 1 parameter, 0 given in ..... on line 16
```

## PHP strpos() 函数

strpos() 函数用于检索字符串内指定的字符或文本。

如果找到匹配，则会返回首个匹配的字符位置。如果未找到匹配，则将返回 FALSE。

下例检索字符串 "Hello world!" 中的文本 "world"：

### 实例

```
<?php
echo strpos("Hello world!","world"); // 有就返回下标，没有则不返回
?>
```