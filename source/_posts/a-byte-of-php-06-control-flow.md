---
title: 《简明 PHP 教程》06 流程控制
date: 2019-09-03 18:35:28
category: 《简明 PHP 教程》
tags: 《简明 PHP 教程》
---

截止到目前，在我们所看过的程序中，都是从上向下排列，然后由 PHP 执行。如果你想改变这个工作流程，应该怎么做？你需要程序作出一些判断，并依据不同的情况去完成不同的事情，例如依据每天时间的不同打印出 '早上好' 'Good Morning' 或 '晚上好' 'Good Evening'？

正如你可能已经猜到的那样，这是通过流程控制来实现的。在 PHP 中有多种流程控制语句。

## `if` 语句

`if` 语句用于判断条件：如果条件为真（True），我们将运行一部分代码，否则我们将运行另一部分代码。其中 `else` 从句是可选的。

示例（保存为 `if.php`）

```
<?php
$number = 23;
fwrite(STDOUT, 'Enter an integer: ');
$guess = (int)trim(fgets(STDIN));

if ($guess == $number) {
    echo 'Congratulations, you guessed it.' . PHP_EOL;
    echo '(but you do not win any prizes!)' . PHP_EOL;
} else if ($guess < $number) {
    echo 'No, it is a little higher than that' . PHP_EOL;
} else {
    echo 'No, it is a little lower than that' . PHP_EOL;
}

echo 'Done' . PHP_EOL;
```

输出：

```
$ php if.php
Enter an integer: 50
No, it is a little lower than that
Done

$ php if.php
Enter an integer: 22
No, it is a little higher than that
Done

$ php if.php
Enter an integer: 23
Congratulations, you guessed it.
(but you do not win any prizes!)
Done
```

### 它是如何工作的

在这个程序中，我们根据用户猜测的数字来检查这一数字是否是我们所设置的。我们将变量 `$number` 设为任何我们所希望的整数，例如 `23`。然后，我们通过 `fgets()` 函数来获取用户的猜测数。所谓函数是一种可重复使用的程序。我们将在下一章详细讨论它。

我们为内置的 `fwrite` 函数提供一串打印到屏幕上的字符串并等待用户的输入。一旦我们输入了某些内容并按下键盘上的 `enter` 键，`fgets()` 函数将以字符串的形式返回我们所输入的内容。然后我们通过 `(int)` 将这个字符串转换成一个整数并将其储存在变量 `$guess` 中。实际上，大多数情况下都不需要强制转换，因为当运算符，函数或流程控制需要一个 `integer` 参数时，值会自动转换。

接下来，我们将用户提供的猜测数与我们所选择的数字进行对比。如果它们相等，我们就打印一条成功信息。在这里要注意到我们使用大括号和缩进级别来告诉 PHP 哪些语句分别属于哪个块。

然后，我们检查猜测数是否小于我们选择的数字，如果是，我们将告诉用户他们必须猜一个更高一些的数字。在这里我们使用的是 `elseif` 语句，它们实际上将两个相连的 `if else-if else` 语句合并成一句 `if-elseif-else` 语句。这能够使程序更加简便。

你可以在 `if` 块的一个 `if` 语句中设置另一个 `if` 语句，并可以如此进行下去——这被称作嵌套的 `if` 语句。

要记住 `elseif` 和 `else` 部分都是可选的。一个最小规模且有效的 `if` 语句是这样的：

```
<?php
if (True) {
  echo 'Yes, it is true' . PHP_EOL;
}
```

当 PHP 完整执行了 `if` 语句或其相关的 `elseif` `else` 子句后，它会移动到下一部分的代码块。在本例中，也就是 `echo 'Done' . PHP_EOL;` 语句。在完成这些工作后，PHP 会发现运行至程序末尾并宣告工作的完成。

虽然这是一个很简单的程序（对于具有 C/C++ 背景的人来说非常简单易懂），但是最开始你可能需要刻意的去记忆，等到更多的操作之后你就会习惯其中的逻辑，到时候对你来说就是“自然而然”的事情了。

## `while` 语句

`while` 语句能够让你在条件为真的前提下重复执行某块语句。 `while` 语句是 *循环（Looping）* 语句的一种。

示例（保存为 `while.php`）：

```
<?php
$number = 23;
$running = True;

while ($running) {
    fwrite(STDOUT, 'Enter an integer: ');
    $guess = (int)trim(fgets(STDIN));

    if ($guess == $number) {
        echo 'Congratulations, you guessed it.' . PHP_EOL;
        // 使 while 循环终止
        $running = False;
    } elseif ($guess < $number) {
        echo 'No, it is a little higher than that' . PHP_EOL;
    } else {
        echo 'No, it is a little lower than that' . PHP_EOL;
    }
}

// while 循环结束执行
echo 'The while loop is over.' . PHP_EOL;
echo 'Done' . PHP_EOL;
```

输出：

```
$ php while.php
Enter an integer: 50
No, it is a little lower than that
Enter an integer: 22
No, it is a little higher than that
Enter an integer: 23
Congratulations, you guessed it.
The while loop is over.
Done
```

### 它是如何工作的

在这一程序中，我们依旧通过猜数游戏来演示，不过新程序的优点在于能够允许用户持续猜测直至他猜中为止，而无需像我们在上一节中所做的那样，每次猜测都要重新运行程序。这种变化恰到好处地演示了 `while` 语句的作用。

首先我们将 `fwrite` 与 `if` 语句移到 `while` 循环之中，并在 `while` 循环开始前将变量 `$running` 设置为 `True`。程序开始时，我们首先检查变量 `$running` 是否为 `True`，之后再执行相应的 `while` 代码块。在这一代码块被执行之后，将会重新对条件进行检查，在本例中也就是 `$running` 变量。如果它依旧为 `True`，我们将再次执行 `while` 代码块，否则我们将结束循环，然后进入到下一个语句中。

`True` 和 `False` 被称作布尔（Boolean）型，你可以将它们分别等价地视为 `1` 与 `0`。

## `do-while` 语句

`do-while` 循环和 `while` 循环非常相似，区别在于表达式的值是在每次循环结束时检查而不是开始时。和一般的 `while` 循环主要的区别是 `do-while` 的循环语句保证会执行一次（表达式的真值在每次循环结束后检查），然而在一般的 `while` 循环中就不一定了（表达式真值在循环开始时检查，如果一开始就为 `FALSE` 则整个循环立即终止）。

示例（保存为 `do_while.php`）：

```
<?php
$number = 23;
$running = False;

do {
    fwrite(STDOUT, 'Enter an integer: ');
    $guess = (int)trim(fgets(STDIN));

    if ($guess == $number) {
        echo 'Congratulations, you guessed it.' . PHP_EOL;
        // 使 while 循环终止
        $running = False;
    } elseif ($guess < $number) {
        echo 'No, it is a little higher than that' . PHP_EOL;
        // 使 do-while 循环继续
        $running = True;
    } else {
        echo 'No, it is a little lower than that' . PHP_EOL;
        // 使 do-while 循环继续
        $running = True;
    }
} while ($running);

// while 循环结束执行
echo 'The while loop is over.' . PHP_EOL;
echo 'Done' . PHP_EOL;
```

输出：

```
$ php do_while.php
Enter an integer: 50
No, it is a little lower than that
Enter an integer: 22
No, it is a little higher than that
Enter an integer: 23
Congratulations, you guessed it.
The while loop is over.
Done
```

### 它是如何工作的


在这一程序中，我们依旧通过猜数游戏来演示，与 `while` 语句的示例类似，同样能够允许用户持续猜测直到猜中为止，首先我们将 `fwrite` 与 `if` 语句移到 `while` 循环之中，并在 `while` 循环开始前将变量 `$running` 设置为 `False`。程序开始时，不会判断 `$running` 是否为真，而是先执行相应的 `while` 代码块。在这一代码块被执行之后，会对条件进行检查，在本例中也就是 `$running` 变量。如果它为 `True`，我们将再次执行 `while` 代码块，否则我们将结束循环，然后进入到下一个语句中。

## `for` 语句

`for` 循环是 PHP 中最复杂的循环结构。它的行为和 C 语言的相似。

示例（保存为 `for.php`）

```
<?php
for ($i = 1; $i <= 5; $i++) {
    echo $i . PHP_EOL;
}

echo 'The for loop is over' . PHP_EOL;
```

输出：

```
$ php for.php
1
2
3
4
5
The for loop is over
```

### 它是如何工作的

在这个程序里，会打印一列数字，在循环开始前表达式 `$i = 1;` 先无条件求值一次。表达式 `$i <= 5;` 在每次循环开始前求值一次，如果值为 `True` 则继续循环，执行嵌套的循环语句。如果为 `False` 则终止循环。表达式 `$i++;` 在每次循环之后被求值。其中，每个表达式都可以为空或包括逗号分隔的多个表达式。其中第二个表达式如果为空意味着将无限循环下去（和 C 一样，PHP 暗中认为其值为 `True`）。看起来似乎没什么用，其实不然，因为经常会使用有条件的 `break` 语句来结束循环而不是用 `for` 的表达式真值判断。

## `foreach` 语句

`foreach` 语法结构提供了遍历数组的简单方式。`foreach` 仅能够应用于数组和对象，如果尝试应用于其他数据类型的变量，或者未初始化的变量将发出错误信息。

示例（保存为 `foreach`）：

```
<?php
$arr = array('one', 'two', 'three');

foreach ($arr as $key => $value) {
    echo $key . ': ' . $value . PHP_EOL;
}

echo 'The foreach loop is over' . PHP_EOL;
```

输出：

```
$ php foreach.php
0: one
1: two
2: three
The foreach loop is over
```

### 它是如何工作的

在这个程序里，`foreach` 会遍历数组 `$arr`，在每次循环中，将每一个单元的键名赋给变量 `$key`，将每一个单元的值赋给变量 `$value`，并且数组内部的指针向前移一步（因此下一次循环中将会得到下一个单元）。另外，对于键名也可以不进行赋值。

如果要修改数组的元素可以通过在 `$value` 之前加上 `&`。此方法将以引用赋值而不是拷贝一个值。需要说明的是，`$value` 的引用只有在被遍历的数组可以引用时才可用（例如是个变量）。

## `switch` 语句

`switch` 语句类似于具有同一个表达式的一系列 `if` 语句。很多场合下需要把同一个变量（或表达式）与很多不同的值比较，并根据它等于哪个值来执行不同的代码。这正是 `switch` 语句的用途。

示例（保存为 `switch.php`）：

```
<?php
fwrite(STDOUT, 'Enter something: ');
$i = trim(fgets(STDIN));

switch ($i) {
    case 0:
        echo 'i equals 0' . PHP_EOL;
        break;
    case 1:
        echo 'i equals 1' . PHP_EOL;
        break;
    case 'apple':
        echo 'i is apple' . PHP_EOL;
        break;
    case 'bar':
    case 'cake':
        echo 'i is bar or cake' . PHP_EOL;
        break;
    default:
        echo 'i is unknown' . PHP_EOL;
}

echo 'The switch loop is over' . PHP_EOL;
```

输出：

```
$ php switch.php
Enter something: 0
i equals 0
The switch loop is over

$ php switch.php
Enter something: 1
i equals 1
The switch loop is over

$ php switch.php
Enter something: apple
i equals 0
The switch loop is over
```

### 它是如何工作的

在这个程序里，`switch` 语句一行接一行的执行，当一个 `case` 语句中的值和 `switch` 表达式的值匹配时，执行相关代码，我们分别输入了 `0` `1` `apple`，其中需要注意的是，因为 `switch/case` 采用的是松散比较，因此 `apple` 最先匹配到的是 `0`，所以输出的是 `i equals 0`。 匹配以后遇到第一个 `break` 语句 `switch` 语句就结束了。

我们可以看到 `case` 表达式可以是任何求值为简单类型的表达式，即整型或浮点数以及字符串。不能用数组或对象，除非它们被解除引用成为简单类型。另外，`default` 是 `case` 的一个特例，它匹配了任何和其它 `case` 都不匹配的情况。

## `break` 语句

`break` 结束当前 `for`，`foreach`，`while`，`do-while` 或者 `switch` 结构的执行。`break` 可以接受一个可选的数字参数来决定跳出几重循环。

示例（保存为 `break.php`）：

```
<?php
while (True) {
    fwrite(STDOUT, 'Enter something: ');
    $s = trim(fgets(STDIN));

    if ($s == 'quit') {
        break;
    }

    echo 'Length of the string is ' . strlen($s) . PHP_EOL;
}

echo 'Done' . PHP_EOL;
```

输出：

```
$ php break.php
Enter something: Programming is fun
Length of the string is 18
Enter something: When the work is done
Length of the string is 21
Enter something: if you wanna make your work also fun:
Length of the string is 37
Enter something: use PHP!
Length of the string is 8
Enter something: quit
Done
```

### 它是如何工作的

在这个程序里，我们重复地接受用户的输入内容并打印出每一次输入内容的长度。我们通过检查用户输入的是否是 `quit` 这一特殊条件来判断是否应该终止程序。我们通过中断循环并转到程序末尾来结束这一程序。

输入字符串的长度可以通过内置的 `strlen` 函数来获取到。

## `continue` 语句

`continue` 在循环结构用用来跳过本次循环中剩余的代码并在条件求值为真时开始执行下一次循环。

> 注意：在 PHP 中 switch 语句被认为是可以使用 continue 的一种循环结构。

`continue` 接受一个可选的数字参数来决定跳过几重循环到循环结尾。默认值是 `1`，即跳到当前循环末尾。

示例（保存为 `continue.php`）：

```
<?php
while (True) {
    fwrite(STDOUT, 'Enter something: ');
    $s = trim(fgets(STDIN));

    if ($s == 'quit') {
        break;
    }

    if (strlen($s) < 3) {
        echo 'Too small' . PHP_EOL;
        continue;
    }

    echo 'Input is of sufficient length' . PHP_EOL;
}
```

输出：

```
$ php continue.php
Enter something: a
Too small
Enter something: 12
Too small
Enter something: abc
Input is of sufficient length
Enter something: quit
```

### 它是如何工作的

在这个程序里，我们接受来自用户的输入内容，但是只有在输入的字符串其长至少 `3` 字符我们才会对其进行处理。为此，我们使用内置的 `strlen` 函数来获取字符串的长度，如果其长度小于 `3`，我们便使用 `continue` 语句跳过代码块中的其余语句。否则，循环中的剩余语句将被执行，并在此处进行我们所希望的任何类型的处理。

## 总结

我们已经了解了六种流程控制语句——`if`，`while`，`do-while`，`for`，`foreach` 和 `switch` ——及其相关的 `break` 与 `continue` 语句是如何工作的。这些语句是 PHP 中一些最常用的部分，因此，习惯去使用它们是必要的。

接下来，我们将了解如何创建并使用函数。