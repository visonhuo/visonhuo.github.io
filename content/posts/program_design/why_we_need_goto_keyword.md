---
title: "关于 goto 语句那些事"
date: 2022-02-18T16:55:34+08:00
lastmod: 2022-02-18T16:55:34+08:00
draft: false
author: visonhuo
description: "该文章将会探索 goto 关键字，并讨论为何它不推荐被使用，却仍然被现代编程语言所保留。"
featuredImage: "/images/feature/program_language/why_we_need_goto_keyword_bg.png"
featuredImagePreview: "/images/feature/program_language/why_we_need_goto_keyword_bg.png"
categories: ["程序设计"]
tags: ["编程语言", "代码规范"]
---
<!--more-->
## 前言
当提到 goto 关键字时，相信大多数人的第一感觉是既熟悉又陌生。
熟悉是因为几乎主流的编程语言都支持这个关键字，陌生是因为大部分的语言规范又不推荐我们使用 goto 语句，
以至于很多人都认为在代码中只要出现了 goto 关键字，那就是不好的代码设计。

然而，事实真的是如此么？如果 goto 本身糟糕透顶，那为什么至今主流的语言仍然选择保留这个关键字呢？
我们将通过本篇文章对 goto 关键字一探究竟，将讨论如下几个主题：
- [x] 关于 goto 的争论
- [x] goto 的使用方法
- [x] goto 适用于哪些场景？

## 关于 goto 的争论
首先我们引用一段在 Wiki 百科中关于 goto 的介绍：
> GOTO是一条可以在许多计算机编程语言中找到的语句，它是英文单词go和to的组合。
> 当执行这条语句的时候，它将控制流程**无条件**地转到另一条语句（也叫“**跳转**”）。
> 跳转语句需要指明标记，在不同语言中，标记可以是标识符或行号。
> 在机器码级别，goto是一种分支的形式。

综上所述，goto 本身是一个用于控制程序流的跳转指令。
从这一点上看，它与 if、while、for 这些流程控制指令并无不同，但最大的问题是出在 **无条件** 这个前提下。

在写这篇文章前，我曾想通过查找一些资料来探寻 goto 指令的起源，即： 
1. goto 指令是什么时候诞生的？
2. 该指令诞生的初衷是什么？

可惜在网上并没有找到关于这些疑问的详细资料，但通过 goto 的争论史，我们却能够从中推敲出一些线索。

### 争论起源
在 1968 年，E.W.Dijkstra 发表了一篇著名的论文：**《Go to Statement Considered Harmful》**。
在论文中，提出了 **"goto语句是有害的"** 的论点，激起了人们对传统程序设计方法的讨论。

而争论中出现两种声音，主张从高级程序语言中废除 goto 语句的人认为：
> goto 语句会造成程序静态结构和动态执行之间巨大的差异，并且破坏了程序单入口、单出口的基本结构，
> 导致难以排查问题，去掉 goto 语句能够使程序更加易读和理解。

而存在另一些人则认为，若废除 goto 语句，某些情况下反而会在程序中增加许多不必要的计算量，
使程序变得更加复杂，而灵活地使用 goto 语句会提高程序的效率。

这是一切争论的起点，并且关于 **"goto语句是有害的"** 的论点至今也是程序设计界的主流观点，
即我们应该在代码中禁止使用 goto 语句，它将使我们的代码变得难以理解。

### 争论平息
于 1974 年，D.E.Knuth 发表了文章：**《Structured Programming with GOTO Statement》**，其中发表了更加客观的观点：
> 在一些使用 goto 会使程序变得难以理解的情况下，要避免使用 goto 语句，
> 但在不破坏程序良好结构的前提下，有控制地使用一些 goto 语句来提高程序的效率是可行的。

这种观点的提出最终平息了长达 10 年之久的争论。而随着后来 Dijkstra 和他的研究团队证明了：
**所有的程序都可以用顺序、分支和循环结构来描述**。
结构化程序设计思想逐渐深入人心，goto 语句才渐渐淡出了人们的视线。

### 再谈起源
虽然没有明确的资料来证实 goto 指令的起源，但是通过以上信息，我们也可以从侧面推敲出一些线索：
- 在 1968 年之前，goto 语句就已经应用于日常的程序设计中；（否则不可能存在争论）
- 在结构化程序设计被证明之前，goto 指令就已经存在了；

以上信息说明，goto 是比条件、循环等指令出现时间更早的程序流控制指令。
goto 设计之初的初衷是为了完成 "指令跳转" 的任务。但因为其过于灵活的特性，
如果在程序中过度使用，将导致程序难以控制和理解，才会有接下来更规范的控制流指令出现。

## goto 的使用方法
无论在哪种语言中使用 goto 语句，基本都符合以下使用方式：
1. 在目标代码片段前添加 Label 信息；
2. 在需要跳转逻辑的地方，使用 goto Label 进行跳转；

以下是一段使用 Golang 编写的示例代码，通过使用 goto 语句将错误处理放置到程序的末尾处。
```Go
package main

import "fmt"

func printPositive(num int) {
	if num < 0 {
		goto Panic
	}
	fmt.Println("value: ", num)
Panic:
	panic("not positive number")
}

func main() {
	printPositive(-1)
}
```

## goto 适用于哪些场景？
虽然现如今仍存在部分高级语言保留了 goto 关键字，但几乎都对其跳转行为进行了不同程度的限制。比如：
- 在 Golang 中，goto 跳转被限制在只能在同一个函数体内进行跳转 ([Goto statements](https://go.dev/ref/spec#Goto_statements)；
- 在 Java 中，只是保留了 goto 关键字，而禁用了 goto 的功能；

功能受限的 goto 语句会失去一定的灵活性，但同时也给予程序更多的 **安全性和可控性**。

那么，我们应该在什么场景下才考虑使用 goto 语句呢？以下将会列举一些场景供参考。

### 跳出多层循环
在许多语言中，在一个循环中跳到外部可以通过 break 关键字实现。
但是 break 只能跳出一层循环，如果在一个两层、三层甚至多层的循环中要跳转到外部，
则需要我们在每一层的循环体中都添加该层对应的 break 语句。

在这种场景下，我们可以通过 **goto+标签** 的方式来直接跳出整个大循环体，如下：
```go
func twoLayerLoop() {
	i, j := 0, 0
	for i = 0; i < 5; i++ {
		for j = 0; j < 5; j++ {
			if i == 2 && j == 2 {
				goto Out
			}
		}
	}
Out:
	fmt.Println("i == 2 & j == 2")
}
```
在 Java 中，虽然不支持 goto 语句，但它拓展了 break 语句的能力，可以使用 **break+标签** 来退出多层循环体。

### 提取共享的代码片段
当在一个函数中的几个地方都需要使用同一个程序片段，并且又没有将其封装成另外一个函数的必要，
但如果只是简单的拷贝片段又不利于日后修改的情况下，使用 goto 语句就很有价值。

一个最典型的场景就是在函数中统一错误处理，比如说：
```go
func big_function() error {
	if [error1] {
	    /* clean up logic */
		return error1
    } 
	if [error2] {
        /* clean up logic */
        return error2
    }
    if [error3] {
        /* clean up logic */
        return error3
    }
	return nil
}
```

以上的代码就在每一个分支中都拷贝了同样的 clean up 逻辑。
此时，如果 clean up 逻辑需要进行调整，我们就要同时修改多个代码片段，
这种方式增加了代码调整的繁琐程度以及出错的可能性。

此处如果我们使用 goto 语句对其进行改造，则代码流程就变为：
```go
func big_function() (err error) {
	if [error1] {
		err = error1
		goto Error
    } 
	if [error2] {
		err = error2
		goto Error
    }
    if [error3] {
		err = error3
		goto Error
    }
	return nil
	
Error:
	/* clean up logic */
	return err
}
```
这样不仅缩小了 clean up 逻辑修改而导致的代码改动范围，还增加了代码整体的可读性，
将整个函数的错误处理逻辑都统一放置到函数末尾处。

### 提高代码可读性
在 Golang 的标准库中也存在使用 goto 的例子，以下代码来自 math/gamma.go 。
```go
func function() {
    for x < 0 {
        if x > -1e-09 {
            goto small
        }
        z = z / x
        x = x + 1
    }
    for x < 2 {
        if x < 1e-09 {
            goto small
        }
        z = z / x
        x = x + 1
    }
    
    if x == 2 {
        return z
    }
    
    x = x - 2
    p = (((((x*_gamP[0]+_gamP[1])*x+_gamP[2])*x+_gamP[3])*x+_gamP[4])*x+_gamP[5])*x + _gamP[6]
    q = ((((((x*_gamQ[0]+_gamQ[1])*x+_gamQ[2])*x+_gamQ[3])*x+_gamQ[4])*x+_gamQ[5])*x+_gamQ[6])*x + _gamQ[7]
    return z * p / q
    
small:
    if x == 0 {
        return Inf(1)
    }
    return z / ((1 + Euler*x) * x)
}
```
在这个例子中，虽然我们也可以通过将 small 标签段抽象为一个函数来实现，
但 goto 版本的优势在于其性能更好，因为无需进行函数调用和变量拷贝等操作。

但是我们同样需要记住的是：在现代编译器的优化下，这种小函数极有可能被内联到大函数中。
所以此处使用 goto 并不能真正提高多少性能（即使没有内联，在大多数场景下，这种函数调用开销也可以忽略不计），
本人同样也不认为提升性能是我们使用 goto 语句的首要考量标准。

使用 goto 的首要考量标准应该是：**可读性**。
相比于封装函数的方式，goto 版本的好处在于， 
它将仅作用于当前函数的代码片段放在了当前函数体中，以此提高了函数内聚和可读性。

如果某个代码片段仅在当前函数内使用，通过 **goto+标签** 的方式来直接引用该代码片段，
从而避免在其他地方创建一个新函数，在某些场景下确实可以提高代码的可读性。

## 小结
综上所述，goto 语句在现代编程语言中的能力已经被大大地削弱，其行为也更加可控，
所以我们不应该 **教条式** 地认为 goto 语句就是有害的。
Dijkstra 以及之后的人极力反对它是因为滥用 goto 将会导致软件难以理解和跟踪，
所以其核心问题 **在于使用者，而非 goto 语句本身**。

上面同样列举了一些场景指出，适当的使用 goto 语句是有益于代码可读性的。
如果我们本身对于代码可读性就没有客观的判断，那么即使不使用 goto 语句，也极可能写出更为糟糕的代码。

对于程序设计而言，我们的出发点始终是：**构建清晰、易懂的代码**。 
goto 和其他语句一样，都只是为我们这个思想服务的手段或工具。
**在合适的地方用合适的结构，避免教条式地编程** 才是我们应该真正追寻的目标。

## Reference
- [Goto wiki page](https://zh.wikipedia.org/wiki/Goto)
- [浅析C语言中的goto语句](https://www.docin.com/p-2599123704.html)
- [Why does Go have a "goto" statement?](https://stackoverflow.com/questions/11064981/why-does-go-have-a-goto-statement)
- [When To Use Goto in C](https://www.cprogramming.com/tutorial/goto.html)