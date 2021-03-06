---
title: Go指南总结
date: 2017-12-07 09:09:51
categories: Golang
tags: golang
---

# GO 指南总结

## 包

每个 Go 程序都是由包组成的。

程序运行的入口是包 `main` 。

这个程序使用并导入了包 `"fmt"` 和 `"math/rand"` 。

按照惯例，包名与导入路径的最后一个目录一致。例如，`"math/rand"` 包由 `package rand` 语句开始。

**注意**：这个程序的运行环境是确定性的，因此 `rand.Intn` 每次都会返回相同的数字。 （为了得到不同的随机数，需要提供一个随机数种子，参阅 [`rand.Seed`](https://go-zh.org/pkg/math/rand/#Seed)。）

## 导入

这个代码用圆括号组合了导入，这是“打包”导入语句。

同样可以编写多个导入语句，例如：

```go
import "fmt"
import "math"
```

不过使用打包的导入语句是更好的形式。

## 导出名

在 Go 中，首字母大写的名称是被导出的。

在导入包之后，你只能访问包所导出的名字，任何未导出的名字是不能被包外的代码访问的。

`Foo` 和 `FOO` 都是被导出的名称。名称 `foo` 是不会被导出的。

执行代码，注意编译器报的错误。

然后将 `math.pi` 改名为 `math.Pi` 再试着执行一下。

## 函数

函数可以没有参数或接受多个参数。

在这个例子中， `add` 接受两个 `int` 类型的参数。

注意类型在变量名 *之后* 。

（参考 [这篇关于 Go 语法定义的文章](https://blog.go-zh.org/gos-declaration-syntax)了解类型以这种形式出现的原因。）



当两个或多个连续的函数命名参数是同一类型，则除了最后一个类型之外，其他都可以省略。

在这个例子中 ，

```
x int, y int
```

被缩写为

```
x, y int
```

## 多值返回

函数可以返回任意数量的返回值。

`swap` 函数返回了两个字符串。

## 命名返回值

Go 的返回值可以被命名，并且就像在函数体开头声明的变量那样使用。

返回值的名称应当具有一定的意义，可以作为文档使用。

没有参数的 `return` 语句返回各个返回变量的当前值。这种用法被称作“裸”返回。

直接返回语句仅应当用在像下面这样的短函数中。在长的函数中它们会影响代码的可读性。

```go
func split(sum int) (x, y int) {
	x = sum * 4 / 9
	y = sum - x
	return
}

func main() {
	fmt.Println(split(17))
}
```

## 变量

`var` 语句定义了一个变量的列表；跟函数的参数列表一样，类型在后面。

就像在这个例子中看到的一样， `var` 语句可以定义在包或函数级别。

## 初始化变量

变量定义可以包含初始值，每个变量对应一个。

如果初始化是使用表达式，则可以省略类型；变量从初始值中获得类型。

## 短声明变量

在函数中， `:=` 简洁赋值语句在明确类型的地方，可以用于替代 `var` 定义。

函数外的每个语句都必须以关键字开始（ `var` 、 `func` 、等等）， `:=` 结构不能使用在函数外。

```go
func main() {
	var i, j int = 1, 2
	k := 3
	c, python, java := true, false, "no!"

	fmt.Println(i, j, k, c, python, java)
}
```

## 基本类型

Go 的基本类型有Basic types

```go
bool

string

int  int8  int16  int32  int64
uint uint8 uint16 uint32 uint64 uintptr

byte // uint8 的别名

rune // int32 的别名
     // 代表一个Unicode码

float32 float64

complex64 complex128
```

这个例子演示了具有不同类型的变量。 同时与导入语句一样，变量的定义“打包”在一个语法块中。

`int`，`uint` 和 `uintptr` 类型在32位的系统上一般是32位，而在64位系统上是64位。当你需要使用一个整数类型时，你应该首选`int`，仅当有特别的理由才使用定长整数类型或者无符号整数类型。

```go
package main

import (
	"fmt"
	"math/cmplx"
)

var (
	ToBe   bool       = false
	MaxInt uint64     = 1<<64 - 1
	z      complex128 = cmplx.Sqrt(-5 + 12i)
)

func main() {
	const f = "%T(%v)\n"
	fmt.Printf(f, ToBe, ToBe)
	fmt.Printf(f, MaxInt, MaxInt)
	fmt.Printf(f, z, z)
}
```

## 零值

变量在定义时没有明确的初始化时会赋值为 *零值* 。

零值是：

- 数值类型为 `0` ，
- 布尔类型为 `false` ，
- 字符串为 `""` （空字符串）。

```go
package main

import "fmt"

func main() {
	var i int
	var f float64
	var b bool
	var s string
	fmt.Printf("%v %v %v %q\n", i, f, b, s)
}
```

```
0 0 false ""
```

## 类型转换

表达式 `T(v)` 将值 `v` 转换为类型 `T` 。

一些关于数值的转换：

```
var i int = 42
var f float64 = float64(i)
var u uint = uint(f)
```

或者，更加简单的形式：

```
i := 42
f := float64(i)
u := uint(f)
```

与 C 不同的是 Go 的在不同类型之间的项目赋值时需要显式转换。 试着移除例子中 `float64` 或 `int` 的转换看看会发生什么。

## 类型推导

在定义一个变量却并不显式指定其类型时（使用 `:=` 语法或者 `var =` 表达式语法）， 变量的类型由（等号）右侧的值推导得出。

当右值定义了类型时，新变量的类型与其相同：

```go
var i int
j := i // j 也是一个 int
```

但是当右边包含了未指名类型的数字常量时，新的变量就可能是 `int` 、 `float64` 或 `complex128` 。 这取决于常量的精度：

```go
i := 42           // int
f := 3.142        // float64
g := 0.867 + 0.5i // complex128
```

尝试修改演示代码中 `v` 的初始值，并观察这是如何影响其类型的。

```go
package main

import "fmt"

func main() {
	v := 42.0+1i // change me!
	fmt.Printf("v is of type %T\n", v)
}
```

```
v is of type complex128
```

## 常量

常量的定义与变量类似，只不过使用 `const` 关键字。

常量可以是字符、字符串、布尔或数字类型的值。

常量不能使用 `:=` 语法定义。

```go
package main

import "fmt"

const Pi = 3.14

func main() {
	const World = "世界"
	fmt.Println("Hello", World)
	fmt.Println("Happy", Pi, "Day")

	const Truth = true
	fmt.Println("Go rules?", Truth)
}
```

## 数值常量

数值常量是高精度的 *值* 。

一个未指定类型的常量由上下文来决定其类型。

也尝试一下输出 `needInt(Big)` 吧。

（`int` 可以存放最大64位的整数，根据平台不同有时会更少。）

## for

Go 只有一种循环结构—— `for` 循环。

基本的 `for` 循环包含三个由分号分开的组成部分：

- 初始化语句：在第一次循环执行前被执行
- 循环条件表达式：每轮迭代开始前被求值
- 后置语句：每轮迭代后被执行

初始化语句一般是一个短变量声明，这里声明的变量仅在整个 `for` 循环语句可见。

如果条件表达式的值变为 `false`，那么迭代将终止。

*注意*：不像 C，Java，或者 Javascript 等其他语言，`for` 语句的三个组成部分 并不需要用括号括起来，但循环体必须用 `{ }` 括起来。

```go
package main

import "fmt"

func main() {
	sum := 0
	for i := 0; i < 10; i++ {
		sum += i
	}
	fmt.Println(sum)
}
```

循环初始化语句和后置语句都是可选的。

```go
package main

import "fmt"

func main() {
	sum := 1
	for ; sum < 1000; {
		sum += sum
	}
	fmt.Println(sum)
}
```

## for 是 Go 的 “while”

基于此可以省略分号：C 的 `while` 在 Go 中叫做 `for` 。

```go
package main

import "fmt"

func main() {
	sum := 1
	for sum < 1000 {
		sum += sum
	}
	fmt.Println(sum)
}
```

## 死循环

如果省略了循环条件，循环就不会结束，因此可以用更简洁地形式表达死循环。

```go
package main

func main() {
	for {
	}
}
```

## if

就像 `for` 循环一样，Go 的 `if` 语句也不要求用 `( )` 将条件括起来，同时， `{ }` 还是必须有的。

```go
package main

import (
	"fmt"
	"math"
)

func sqrt(x float64) string {
	if x < 0 {
		return sqrt(-x) + "i"
	}
	return fmt.Sprint(math.Sqrt(x))
}

func main() {
	fmt.Println(sqrt(2), sqrt(-4))
}
```

## if 的便捷语句

跟 `for` 一样， `if` 语句可以在条件之前执行一个简单语句。

由这个语句定义的变量的作用域仅在 `if` 范围之内。

（在最后的 `return` 语句处使用 `v` 看看。）

```go
package main

import (
	"fmt"
	"math"
)

func pow(x, n, lim float64) float64 {
	if v := math.Pow(x, n); v < lim {
		return v
	}
	return lim
}

func main() {
	fmt.Println(
		pow(3, 2, 10),
		pow(3, 3, 20),
	)
}
```

## if 和 else

在 `if` 的便捷语句定义的变量同样可以在任何对应的 `else` 块中使用。

（提示：两个 `pow` 调用都在 `main` 调用 `fmt.Println` 前执行完毕了。）

```go
package main

import (
	"fmt"
	"math"
)

func pow(x, n, lim float64) float64 {
	if v := math.Pow(x, n); v < lim {
		return v
	} else {
		fmt.Printf("%g >= %g\n", v, lim)
	}
	// 这里开始就不能使用 v 了
	return lim
}

func main() {
	fmt.Println(
		pow(3, 2, 10),
		pow(3, 3, 20),
	)
}
```

## 练习：循环和函数

作为练习函数和循环的简单途径，用牛顿法实现开方函数。

在这个例子中，牛顿法是通过选择一个初始点 *z* 然后重复这一过程求 `Sqrt(x)` 的近似值：

为了做到这个，只需要重复计算 10 次，并且观察不同的值（1，2，3，……）是如何逐步逼近结果的。 然后，修改循环条件，使得当值停止改变（或改变非常小）的时候退出循环。观察迭代次数是否变化。结果与 [math.Sqrt](https://go-zh.org/pkg/math/#Sqrt) 接近吗？

提示：定义并初始化一个浮点值，向其提供一个浮点语法或使用转换：

```
z := float64(1)
z := 1.0
```

```go
package main

import (
	"fmt"
)

func Sqrt(x float64) float64 {
	const E = 0.000001
	z := float64(1)
	k := float64(0)

	for ;; z = z - (z*z-x)/(2*z) {
		if z - k <= E && z - k >= -E {
			return z
		}
		k = z
	}
}

func main() {
	fmt.Println(Sqrt(144))
}
```

## switch

你可能已经知道 `switch` 语句会长什么样了。

除非以 `fallthrough` 语句结束，否则分支会自动终止。

```go
package main

import (
	"fmt"
	"runtime"
)

func main() {
	fmt.Print("Go runs on ")
	switch os := runtime.GOOS; os {
	case "darwin":
		fmt.Println("OS X.")
	case "linux":
		fmt.Println("Linux.")
	default:
		// freebsd, openbsd,
		// plan9, windows...
		fmt.Printf("%s.", os)
	}
}
```

## switch 的执行顺序

switch 的条件从上到下的执行，当匹配成功的时候停止。

（例如，

```
switch i {
case 0:
case f():
}
```

当 `i==0` 时不会调用 `f` 。）

注意：Go playground 中的时间总是从 2009-11-10 23:00:00 UTC 开始， 如何校验这个值作为一个练习留给读者完成。

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	fmt.Println("When's Saturday?")
	today := time.Now().Weekday()
	switch time.Saturday {
	case today + 0:
		fmt.Println("Today.")
	case today + 1:
		fmt.Println("Tomorrow.")
	case today + 2:
		fmt.Println("In two days.")
	default:
		fmt.Println("Too far away.")
	}
}
```

## 没有条件的 switch

没有条件的 switch 同 `switch true` 一样。

这一构造使得可以用更清晰的形式来编写长的 if-then-else 链。

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	t := time.Now()
	switch {
	case t.Hour() < 12:
		fmt.Println("Good morning!")
	case t.Hour() < 17:
		fmt.Println("Good afternoon.")
	default:
		fmt.Println("Good evening.")
	}
}
```

## defer

defer 语句会延迟函数的执行直到上层函数返回。

延迟调用的参数会立刻生成，但是在上层函数返回前函数都不会被调用。

```go
package main

import "fmt"

func main() {
	defer fmt.Println("world")

	fmt.Println("hello")
}
```

```
hello
world
```

## defer 栈

延迟的函数调用被压入一个栈中。当函数返回时， 会按照后进先出的顺序调用被延迟的函数调用。

阅读[博文](http://blog.go-zh.org/defer-panic-and-recover)了解更多关于 `defer` 语句的信息。

## 指针

Go 具有指针。 指针保存了变量的内存地址。

类型 `*T` 是指向类型 `T` 的值的指针。其零值是 `nil` 。

```
var p *int
```

`&` 符号会生成一个指向其作用对象的指针。

```
i := 42
p = &i
```

`*` 符号表示指针指向的底层的值。

```
fmt.Println(*p) // 通过指针 p 读取 i
*p = 21         // 通过指针 p 设置 i
```

这也就是通常所说的“间接引用”或“非直接引用”。

与 C 不同，Go 没有指针运算。

```go
package main

import "fmt"

func main() {
	i, j := 42, 2701

	p := &i         // point to i
	fmt.Println(*p) // read i through the pointer
	fmt.Println(p)
	*p = 21         // set i through the pointer
	fmt.Println(i)  // see the new value of i

	p = &j         // point to j
	*p = *p / 37   // divide j through the pointer
	fmt.Println(j) // see the new value of j
}
```

```
42
0x10410020
21
73
```

## 结构体

一个结构体（ `struct` ）就是一个字段的集合。

（而 `type` 的含义跟其字面意思相符。）

```go
package main

import "fmt"

type Vertex struct {
	X int
	Y int
}

func main() {
	fmt.Println(Vertex{1, 2})
}
```

## 结构体字段

结构体字段使用点号来访问。

```go
package main

import "fmt"

type Vertex struct {
	X int
	Y int
}

func main() {
	v := Vertex{1, 2}
	v.X = 4
	fmt.Println(v.X)
}
```

## 结构体指针

结构体字段可以通过结构体指针来访问。

通过指针间接的访问是透明的。

```go
package main

import "fmt"

type Vertex struct {
	X int
	Y int
}

func main() {
	v := Vertex{1, 2}
	p := &v
	p.X = 1e9
	fmt.Println(v)
}
```

## 结构体文法

结构体文法表示通过结构体字段的值作为列表来新分配一个结构体。

使用 `Name:` 语法可以仅列出部分字段。（字段名的顺序无关。）

特殊的前缀 `&` 返回一个指向结构体的指针。

```go
package main

import "fmt"

type Vertex struct {
	X, Y int
}

var (
	v1 = Vertex{1, 2}  // 类型为 Vertex
	v2 = Vertex{X: 1}  // Y:0 被省略
	v3 = Vertex{}      // X:0 和 Y:0
	p  = &Vertex{1, 2} // 类型为 *Vertex
)

func main() {
	fmt.Println(v1, p, v2, v3)
}
```

```
{1 2} &{1 2} {1 0} {0 0}
```

## 数组

类型 `[n]T` 是一个有 `n` 个类型为 `T` 的值的数组。

表达式

```
var a [10]int
```

定义变量 `a` 是一个有十个整数的数组。

数组的长度是其类型的一部分，因此数组不能改变大小。 这看起来是一个制约，但是请不要担心； Go 提供了更加便利的方式来使用数组。

```go
package main

import "fmt"

func main() {
	var a [2]string
	a[0] = "Hello"
	a[1] = "World"
	fmt.Println(a[0], a[1])
	fmt.Println(a)
}
```

```
Hello World
[Hello World]
```

## slice

一个 slice 会指向一个序列的值，并且包含了长度信息。

`[]T` 是一个元素类型为 `T` 的 slice。

`len(s)` 返回 slice s 的长度。

```go
package main

import "fmt"

func main() {
	s := []int{2, 3, 5, 7, 11, 13}
	fmt.Println("s ==", s)

	for i := 0; i < len(s); i++ {
		fmt.Printf("s[%d] == %d\n", i, s[i])
	}
}
```

```
s == [2 3 5 7 11 13]
s[0] == 2
s[1] == 3
s[2] == 5
s[3] == 7
s[4] == 11
s[5] == 13
```

## slice 的 slice

slice 可以包含任意的类型，包括另一个 slice。

```go
package main

import (
	"fmt"
	"strings"
)

func main() {
	// Create a tic-tac-toe board.
	game := [][]string{
		[]string{"_", "_", "_"},
		[]string{"_", "_", "_"},
		[]string{"_", "_", "_"},
	}

	// The players take turns.
	game[0][0] = "X"
	game[2][2] = "O"
	game[2][0] = "X"
	game[1][0] = "O"
	game[0][2] = "X"

	printBoard(game)
}

func printBoard(s [][]string) {
	for i := 0; i < len(s); i++ {
		fmt.Printf("%s\n", strings.Join(s[i], " "))
	}
}
```

```
X _ X
O _ _
X _ O
```

## 对 slice 切片

slice 可以重新切片，创建一个新的 slice 值指向相同的数组。

表达式

```
s[lo:hi]
```

表示从 `lo` 到 `hi-1` 的 slice 元素，含前端，不包含后端。因此

```
s[lo:lo]
```

是空的，而

```
s[lo:lo+1]
```

有一个元素。

```go
package main

import "fmt"

func main() {
	s := []int{2, 3, 5, 7, 11, 13}
	fmt.Println("s ==", s)
	fmt.Println("s[1:4] ==", s[1:4])

	// 省略下标代表从 0 开始
	fmt.Println("s[:3] ==", s[:3])

	// 省略上标代表到 len(s) 结束
	fmt.Println("s[4:] ==", s[4:])
}
```

```
s == [2 3 5 7 11 13]
s[1:4] == [3 5 7]
s[:3] == [2 3 5]
s[4:] == [11 13]
```

## 构造 slice

slice 由函数 `make` 创建。这会分配一个全是零值的数组并且返回一个 slice 指向这个数组：

```go
a := make([]int, 5)  // len(a)=5
```

为了指定容量，可传递第三个参数到 `make`：

```go
b := make([]int, 0, 5) // len(b)=0, cap(b)=5

b = b[:cap(b)] // len(b)=5, cap(b)=5
b = b[1:]      // len(b)=4, cap(b)=4
```

```go
package main

import "fmt"

func main() {
	a := make([]int, 5)
	printSlice("a", a)
	b := make([]int, 0, 5)
	printSlice("b", b)
	c := b[:2]
	printSlice("c", c)
	d := c[2:5]
	printSlice("d", d)
}

func printSlice(s string, x []int) {
	fmt.Printf("%s len=%d cap=%d %v\n",
		s, len(x), cap(x), x)
}
```

```
a len=5 cap=5 [0 0 0 0 0]
b len=0 cap=5 []
c len=2 cap=5 [0 0]
d len=3 cap=3 [0 0 0]
```

## nil slice

slice 的零值是 `nil` 。

一个 nil 的 slice 的长度和容量是 0。

```go
package main

import "fmt"

func main() {
	var z []int
	fmt.Println(z, len(z), cap(z))
	if z == nil {
		fmt.Println("nil!")
	}
}
```

```
[] 0 0
nil!
```

## 向 slice 添加元素

向 slice 的末尾添加元素是一种常见的操作，因此 Go 提供了一个内建函数 `append` 。 内建函数的[文档](https://go-zh.org/pkg/builtin/#append)对 `append` 有详细介绍。

```
func append(s []T, vs ...T) []T
```

`append` 的第一个参数 `s` 是一个元素类型为 `T` 的 slice ，其余类型为 `T` 的值将会附加到该 slice 的末尾。

`append` 的结果是一个包含原 slice 所有元素加上新添加的元素的 slice。

如果 `s` 的底层数组太小，而不能容纳所有值时，会分配一个更大的数组。 返回的 slice 会指向这个新分配的数组。

（了解更多关于 slice 的内容，参阅文章[Go 切片：用法和本质](https://blog.go-zh.org/go-slices-usage-and-internals)。）

