---
title: 《简明 PHP 教程》07 函数
date: 2020-05-07 07:19:21
category: 《简明 PHP 教程》
tags: 《简明 PHP 教程》
---
函数是指可重复使用的程序片段。它们允许你为某个代码块赋予名字，允许你通过这一特殊的名字在你的程序任何地方来运行代码块，并可重复任何次数。这就是所谓的调用函数。我们已经使用过了许多内置函数，例如：`strlen` 和 `trim`。

函数概念可能是在任何复杂的软件（无论使用的是何种编程语言）中最重要的部分，所以我们接下来将在本章中探讨有关函数的各个方面。

函数可以通过关键字 `function` 来定义。这一关键字后跟一个函数的标识符名称，在跟一对圆括号，其中可以包括一些变量的名称，随后而来的是包含在大括号中语句块。下面的示例将会展示出这其实非常简单：

示例（保存为 `function1.php`）：
```
<?php
function say_hello() {
    // 该块属于这一函数
    echo 'hello world' . "\n";
}

say_hello(); // 调用函数
say_hello(); // 再次调用函数
```

输出：

```
$ php function1.php
hello world
hello world
```

### 它是如何工作的

我们以上文解释过的方式定义名为 `say_hello` 的函数。这个函数不使用参数，因此在括号中没有声明变量。函数的参数只能输入到函数中，以便可以传递不同的值给它，并获得相应的结果。

可以注意到我们能够两次调用相同的函数，这意味着我们不需要把代码再重新写一遍。

## 函数的参数

通过参数列表可以传递信息到函数，即以逗号作为分隔符的表达式列表。参数是从左向右求值的。要注意在此使用的术语——在定义函数时给定的名称称作“形参”（Parameters），在调用函数时你所提供给函数的值称作“实参”（Arguments）。

示例（保存为 `function_param.php`）：

```
<?php
function print_max($a, $b) {
    if ($a > $b) {
        echo $a . ' is maximum' . "\n";
    } elseif ($a == $b) {
        echo $a . ' is equal to ' . $b . "\n";
    } else {
        echo $b . ' is maximum' . "\n";
    }
}

// 直接传递字面值
print_max(3, 4);

$x = 5;
$y = 7;

// 以参数的形式传递变量
print_max($x, $y);
```

输出：

```
$ php function_param.php
4 is maximum
7 is maximum
```

### 它是如何工作的

在这里，我们将函数命名为 `print_max` 并使用两个参数分别称作 `$a` 和 `$b`。我们使用一个简单的 `if...else` 语句来找出更大的那个数，并将它打印出来。

第一次调用函数 `print_max` 时，我们以实参的形式直接向函数提供这一数字。在第二次调用时，我们将变量作为实参来调用函数。`print_max(x, y)` 将使得实参 `$x` 的值将被赋值给形参 `$a`，而实参 `$y` 的值将被赋值给形参 `$b`。在两次调用中，`print_max` 都以相同的方式工作。

## 局部变量

当你在一个函数的定义中声明变量时，它们不会以任何方式与身处函数之外但具有相同名称的变量产生关系，也就是说，这些变量名只存在于函数这一局部（Local）。这被称为变量的作用域（Scope）。所有变量的作用域是它们被定义的块，从定义它们的名字的定义点开始。

示例（保存为 `function_local.php`）：

```
<?php
$x = 50;

function func($x) {
    echo '$x is ' . $x . "\n";
    $x = 2;
    echo 'Changed local $x to ' . $x . "\n";
}

func($x);
echo '$x is still ' . $x . "\n";
```

输出：

```
$ php function_local.php
$x is 50
Changed local $x to 2
$x is still 50
```

### 它是如何工作的

当我们第一次打印出存在于函数块的第一行的名为 `$x` 的值时，PHP 使用的是在函数声明之上的主代码块中声明的这一参数的值。

接着，我们将值 `2` 赋值给 `$x`。`$x` 是我们这一函数的局部变量。因此，当我们改变函数中 `$x` 的值的时候，主代码块中的 `$x` 则不会受到影响。

随着最后一句 `echo` 语句，我们展示出主代码块中定义的 `$x` 的值，由此确认它实际上不受先前调用的函数中的局部变量的影响。

## `global` 语句

如果你想给一个在程序顶层的变量赋值（也就是说它不存在于任何作用域中，无论是函数还是类），那么你必须告诉 PHP 这一变量并非局部的，而是全局（Global）的。我们可以通过 `global` 语句来完成这件事。

示例（保存为 `function_global.php`）：

```
<?php
$x = 50;

function func($x) {
    global $x;
    echo '$x is ' . $x . "\n";
    $x = 2;
    echo 'Changed global $x to ' . $x . "\n";
}

func($x);
echo 'Value of $x is ' . $x . "\n";
```

输出：

```
$ php function_global.php
$x is 50
Changed global $x to 2
Value of $x is 2
```

### 它是如何工作的

`global` 语句用以声明 `$x` 是一个全局变量——因此，当我们在函数中为 `$x` 进行赋值时，这一改动将影响到我们在主代码块中使用的 `$x` 的值。

你可以在同一句 `global` 语句中指定不止一个的全局变量，例如 `global $x, $y, $z;`。

## 通过引用传递参数

默认情况下，函数参数通过值传递（因而即使在函数内部改变参数的值，它并不会改变函数外部的值）。如果希望允许函数修改它的参数值，必须通过引用传递参数。

如果想要函数的一个参数总是通过引用传递，可以在函数定义中该参数的前面加上符号 `&`：

示例（保存为 `function_update.php`）：

```
<?php
function add_some_extra(&$string)
{
    $string .= 'and something extra.';
}

$str = 'This is a string, ';
add_some_extra($str);
echo $str;
```

输出：

```
$ php function_update.php
This is a string, and something extra.
```

### 它是如何工作的

通过在参数 `$string` 前加上符号 `&`，按照引用进行传值，从而允许函数修改它的参数值。

## 默认参数值

对于一些函数来说，你可能为希望使一些参数（可选）并使用默认的值，以避免用户不想为他们提供值的情况。默认参数值可以有效帮助解决这一情况。你可以通过在函数定义时附加一个赋值运算符（`=`）来为参数指定默认参数值。

默认值必须是常量表达式，不能是诸如变量，类成员，或者函数调用等。

示例（保存为 `function_default.php`）：

```
<?php
function say($message, $times = 1) {
    for ($i = 0; $i < $times; $i++) {
        echo $message;  
    }

    echo "\n";
}

say('Hello');
say('World', 5);
```

输出：

```
$ php function_default.php
Hello
WorldWorldWorldWorldWorld
```

### 它是如何工作的

名为 `say` 的函数用以按照给定的次数打印一串字符串。如果我们没有提供一个数值，则将按照默认设置，只打印一次字符串。我们通过为参数 `$times` 指定默认参数值 `1` 来实现这一点。

在第一次使用 `say` 时，我们只提供字符串因而函数只会将这个字符串打印一次。在第二次使用 `say` 时，我们既提供了字符串，同时也提供了一个参数 `5`，声明我们希望说（Say）这个字符串五次。

> 注意
>
> 只有那些位于参数列表末尾的参数才能被赋予默认参数值，意即在函数的参数列表中拥有默认参数值的参数不能位于没有默认参数值的参数之前。
>
> 这是因为值是按参数所处的位置依次分配的。举例来说，`function func($a, $b = 5)` 是有效的，但 `function func($a = 5, b)` 是无效的。

## 可变数量的参数

有时你可能想定义的函数里面能够有任意数量的变量，也就是参数数量是可变的，这可以通过使用 `...` 来实现（将下方示例保存为 `function_varargs.php`）：

```
<?php
function sum(...$numbers) {
    $acc = 0;

    foreach ($numbers as $n) {
        $acc += $n;
    }
    
    return $acc;
}

echo sum(1, 2, 3, 4);
```

输出：

```
$ php function_varargs.php
10
```

### 它是如何工作的

当我们声明一个诸如 `...$numbers` 的参数时，从此处开始直到结束的所有位置参数（Positional Arguments）都将被收集并汇集成一个称为 `$numbers` 的数组。

## `return` 语句

`return` 语句用于从函数中返回，也就是中断函数。我们也可以选择在中断函数时从函数中返回一个值。

示例（保存为 `function_return.php`）：

```
<?php
function maximum($x, $y) {
    if ($x > $y) {
        return $x;
    } elseif ($x == $y) {
        return 'The numbers are equal';
    } else {
        return $y;
    }
}

echo maximum(2, 3);
```

输出：

```
$ php function_return.php
3
```

### 它是如何工作的

`maximum` 函数将会返回参数中的最大值，在本例中是提供给函数的数值。它使用一套简单的 `if...else` 语句来找到较大的那个值并将其返回。

如果省略了 `return`，则返回值为 `NULL`。

## 总结

我们已经了解了许多方面的函数，但我们依旧还未覆盖到所有类型的函数。不过，我们已经覆盖到了大部分你每天日常使用都会使用到的 PHP 函数。

接下来，我们将学习一些有趣的概念，它们被称为数据结构。