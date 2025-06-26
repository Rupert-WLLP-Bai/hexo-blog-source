---
title: GoByExample
date: 2023-05-21 20:46:48
tags: [Golang, Go, 后端, 编程语言]
categories: [编程语言]
top: 9
---

# GoByExample

GoByExample 是一个针对 Go 语言的在线文档，其中包含了很多 Go 常用的代码示例。本文将介绍一些常用的 GoByExample 示例，并解释它们背后的原理。

参考: https://gobyexample.com

## Hello, World!

让我们从经典的“Hello, World!”程序开始吧：

```go
package main

import "fmt"

func main() {
    fmt.Println("Hello, World!")
}
```

这个程序打印出了字符串 “Hello, World!”。在 Go 中，所有代码都必须属于某个包（package），而 `main` 包是 Go 程序的入口点。`import "fmt"` 声明了一个依赖关系：当前包需要使用 `fmt` 包（即，“格式化”包）中的函数。`func main()` 是程序开始执行的函数。

要运行这个程序，你可以输入以下命令：

```bash
go run hello-world.go
```

输出结果：

```
Hello, World!
```

## Values

Go 支持不同类型的值，例如字符串、整数、浮点数等。下面的例子演示了如何定义变量并输出其值：

```go
package main

import "fmt"

func main() {
    var a = "initial"
    fmt.Println(a)

    var b, c int = 1, 2
    fmt.Println(b, c)

    var d = true
    fmt.Println(d)

    var e int
    fmt.Println(e)

    f := "short"
    fmt.Println(f)
}
```

输出结果：

```
initial
1 2
true
0
short
```

在 Go 中，可以使用 `var` 关键字来显式地定义变量类型。例如 `var a string = "hello"`。

也可以通过 `:=` 语法来隐式地定义变量类型，这种方式适用于在初始化变量时指定其类型。例如 `b := 3` 将创建一个整数类型的变量，并赋值为 3。

## Functions

除了 `main` 函数之外，Go 还支持自定义函数。以下是一个接受两个 `int` 类型参数的函数 `plus`：

```go
package main

import "fmt"

func plus(a int, b int) int {
    return a + b
}

func main() {
    res := plus(1, 2)
    fmt.Println("1+2 =", res)
}
```

输出结果：

```
1+2 = 3
```

如果两个或多个连续的参数具有相同的类型，则可以在最后一个参数后面省略类型。例如：

```go
func plus(a, b, c int) int {
    return a + b + c
}
```

## Multiple Return Values

Go 支持函数返回多个值，以下是一个返回两个值的函数：

```go
package main

import "fmt"

func vals() (int, int) {
    return 3, 7
}

func main() {
    a, b := vals()
    fmt.Println(a)
    fmt.Println(b)

    _, c := vals()
    fmt.Println(c)
}
```

输出结果：

```
3
7
7
```

在 Go 中，使用 `_` 来忽略某个返回值。

## Variadic Functions

Variadic 函数可以接受任意数量的参数。以下是一个例子：

```go
package main

import "fmt"

func sum(nums ...int) {
    fmt.Print(nums, " ")
    total := 0
    for _, num := range nums {
        total += num
    }
    fmt.Println(total)
}

func main() {
    sum(1, 2)
    sum(1, 2, 3)

    nums := []int{1, 2, 3, 4}
    sum(nums...)
}
```

输出结果：

```
[1 2] 3
[1 2 3] 6
[1 2 3 4] 10
```

在函数签名中，`...` 表示这个函数可以接受任意数量的 `int` 类型参数。当调用这个函数时，可以传递任意数量的参数。在函数内部，参数被表示为一个 slice。例如，在 `sum(1, 2)` 调用中，`nums` 将是一个长度为 2 的 slice，其中包含值 1 和 2。

如果已经有一个 slice，可以通过 `slice...` 表达式将其作为 variadic 函数的参数传递。

## Closures

Go 支持闭包（closure），即匿名函数，可以使用函数内部的变量。以下是一个例子：

```go
package main

import "fmt"

func intSeq() func() int {
    i := 0
    return func() int {
        i++
        return i
    }
}

func main() {
    nextInt := intSeq()

    fmt.Println(nextInt())
    fmt.Println(nextInt())
    fmt.Println(nextInt())

    newInts := intSeq()
    fmt.Println(newInts())
}
```

输出结果：

```
1
2
3
1
```

在上面的例子中，`intSeq` 返回了一个匿名函数，该函数保留了对 `i` 的引用，因此每次调用它时，`i` 都会递增。我们使用 `nextInt` 变量来保存返回的匿名函数，并且多次调用 `nextInt` 来查看 `i` 增加的效果。另外，我们还创建了一个新的函数 `newInts`，验证了这个函数与 `nextInt` 不共享状态。

## Recursion

Go 支持递归（recursion）。以下是一个计算阶乘的例子：

```go
package main

import "fmt"

func fact(n int) int {
    if n == 0 {
        return 1
    }
    return n * fact(n-1)
}

func main() {
    fmt.Println(fact(7))
}
```

输出结果：

```
5040
```

在上面的例子中，`fact` 函数调用自身（即递归），直到 `n` 等于 0。这个函数返回所有递归调用的乘积。

## Pointers

Go 支持指针。指针是一个变量，其值为另一个变量的地址。以下是一个将一个变量通过指针进行更新的例子：

```go
package main

import "fmt"

func zeroval(ival int) {
    ival = 0
}

func zeroptr(iptr *int) {
    *iptr = 0
}

func main() {
    i := 1
    fmt.Println("initial:", i)

    zeroval(i)
    fmt.Println("zeroval:", i)

    zeroptr(&i)
    fmt.Println("zeroptr:", i)

    fmt.Println("pointer:", &i)
}
```

输出结果：

```
initial: 1
zeroval: 1
zeroptr: 0
pointer: 0xc0000100e8
```

在上面的例子中，我们定义了两个函数：`zeroval` 和 `zeroptr`。`zeroval` 函数获取一个参数并将其设置为零，但因为它获取参数的副本，所以调用该函数不会对原始变量产生影响。相反，`zeroptr` 函数获取一个 `int` 类型指针，它将通过该指针修改原始变量的值。

`&i` 语法用于获取 `i` 的内存地址。例如，在上面的代码中，`&i` 将返回 `0xc0000100e8`。

