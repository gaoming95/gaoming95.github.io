---
title: Go语言基础
subtitle: Go 是一个开源的编程语言，它能让构造简单、可靠且高效的软件变得容易。Go是从2007年末由Robert Griesemer, Rob Pike, Ken Thompson主持开发，后来还加入了Ian Lance Taylor, Russ Cox等人，并最终于2009年11月开源，在2012年早些时候发布了Go 1稳定版本。现在Go的开发已经是完全开放的，并且拥有一个活跃的社区
date: 2020-12-02
author: 高明
tags:
	- Go基础
---



# Go语言基础

```go
package main // 定义了包名，必须在源文件中的非注释的第一行指明这个文件属于哪个包，package main表示一个可独立执行的程序，每个Go都包含一个名为 main 的包
import "fmt" // 告诉编译器使用fmt包，fmt包实现了格式化IO的函数

func main(){ // func main() 是程序开始执行的函数。main函数是每一个可执行程序所必须包含的，一般来说都是在启动后第一个执行的函数（如果有init函数则会先执行该函数）
    fmt.Println("hello world")
}
```



**当标识符（包括常量、变量、类型、函数名、结构字段等等）以一个大写字母开头，如Group1，那么使用这种形式的标识符的对象就可以被外部包的代码所使用（客户端程序需要先导入这个包）**这个被称为导出（面向对象语言中的public）；标识符如果以小写字母开头，则对包外是不可见的，但是他们在整个包的内部是可见并且可用的（面向对象语言中的protected）

```
go语言特性要求变量声明后和包引入后必须使用,这是个很好的特性能够避免一些低级错误.
全局变量是允许声明但不使用的
```

****

需要注意的是 **{** 不能单独放在一行，所以以下代码在运行时会产生错误

```go
package main
import "fmt"
func main()  
{  // 错误，{ 不能在单独的行上
    fmt.Println("Hello, World!")
}
```

****

**行分隔符**

```go
fmt.Println("Hello, World!")
fmt.Println("菜鸟教程：runoob.com")
// go语言中，一行代表一个语句的结束，每个语句不需要像C家族中的其他语言一样以分号;结尾，因为这些工作都将由Go编译器自动完成，如果打算将多个语句写在同一行，必须使用;认为区分，但是实际开发中并不鼓励
```

****

**注释**

```go
// 单行注释
/*
 * 多行注释
 */
```

****

## 数据类型

```
1 布尔值
2 数字类型
3 字符串类型
4 派生类型
（a）指针类型
（b）数组类型
（c）结构化类型
（d）Channel类型
（e）函数类型
（f）切片类型
（g）接口类型
（h）map类型
```

### 数字类型

1. uint8
   无符号8位整型（0-255）
2. uint16
   无符号16位整型（0-65535）
3. uint32
   无符号32位整型（0-4294967295）
4. uint64
   无符号64位整型（0-18446744073709551615）
5. int8
   有符号8位整型（-128-127）
6. int16
   有符号16位整型（-32768-32767）
7. int32
   有符号32位整型（-2147483648 - 2147483647）
8. int64
   有符号 64 位整型 (-9223372036854775808-9223372036854775807)

### 浮点型

1. float32 IEEE-754 32位浮点型数
2. float64 IEEE-754 64位浮点型数
3. complex64 32 位实数和虚数
4. complex128 64 位实数和虚数

### 其他数字类型

1. byte 类似 uint8
2. rune 类似 int32
3. uint 32 或 64 位
4. int 与 uint 一样大小
5. uintptr 无符号整型，用于存放一个指针

****

## 声明变量

**第一种**

```
var v_name v_type
v_name = value
```

零值就是变量没有做初始化时系统默认设置的值

```go
package main

import "fmt"

func main() {
	// 声明一个变量并初始化
	var a = "RUNOOB"
	fmt.Println(a)

	// 没有初始化就为零值
	var b int
	fmt.Println(b)

	// bool 零值为false
	var c bool
	fmt.Println(c)
}
```

```
RUNOOB
0
false
```

1. 数值类型（包括complex64/128）为0

2. 布尔类型为false

3. 字符串""

4. 以下几种类型为nil

   ```go
   var a *int
   var a []int
   var a map[string] int
   var a chan int
   var a func(string) int
   var a error // error 是接口
   ```

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

**第二种**

根据值自行判定变量类型

```
var v_name = value
```

```go
package main
import "fmt"

func main() {
	var d = true
	fmt.Println(d)
}
```

```
true
```

**第三种**

```
v_name := value
```

```go
var intVal int
intVal := 1 // 编译会产生编译错误
intVal, intVal1 := 1,2 // 此时不会产生编译错误，因为有声明新的变量，因为 := 是一个声明语句
```

### 多变量声明

```go
// 类型相同多个变量，非全局变量
var vname1, vname2, vname3 type
vname1, vname2, vname3 = v1, v2, v3

var vname1, vname2, vname3 = v1, v2 ,v3 // 和 python 很像，不需要显示声明类型，自动推断
vname1, vname2, vname3 := v1, v2, v3 // 出现在 := 左侧的变量不应该是已经被声明过的，否则会导致编译错误

// 这种因式分解关键字的写法一般用于声明全局变量
var {
    vname1 v_type1
    vname2 v_type2
}
```

```go
package main

var x, y int

var (
	a int
	b bool
)

var c, d int = 1, 2
var e, f = 123, "hello"

//这种不带声明格式的只能在函数体中出现
//g, h := 123, "hello"

func main() {
	g, h := 123, "hello"
	println(x, y, a, b, c, d, e, f, g, h)
}
```

```
0 0 0 false 1 2 123 hello 123 hello
```

### 值类型和引用类型

所有像int、float、bool和string这些基本类型都属于值类型，使用这些类型的变量直接指向存在内存中的值

当使用等于=将一个变量的值赋值给另一个变量时，如：j=i，实际上是在内存中将i的值进行了拷贝

```
可以通过&i来获取i的内存地址，例如：oxf840000040（每次地址都可能不一样），
```

#### 简短形式

```go
a := 50
b := false
```

a和b的类型（int和bool）将由编译器自动推断

如果在相同的代码块中，不可以再次对相同名称的变量使用初始化声明

```go
var a int
a = 2 
fmt.Print(a)

// 如果未申明变量a，不可以对其赋值，会产生编译器错误undefined: a
```

### 常量

常量是一个简单值得标识符，在程序运行时，不会被修改的量

常量的数据类型只可以是布尔型，数字型（整数型、浮点型和复数）和字符串型

const identifier [type] = value

可以省略类型说明符[type]，因为编译器可以根据变量的值来推断其类型

1. 显式类定义：const b string = "abc"
2. 隐式类型定义：const b = "abc"

```go
const LENGTH int = 10
fmt.Print(LENGTH)

const A, B, C = 1, false, "str"
print(A, B, C)
```

常量可以使用len()，cap()，unsafe.Sizeof()函数计算表达式的值

```go
package main
import "unsafe"
const (
	a = "abc"
	b = len(a)
	c = unsafe.Sizeof(a)
)
func main(){
	println(a, b, c)
}
```

```
abc 3 16
```

### iota

```go
func main() {
	const (
		a = iota
		b
		c
		d = 1
		e = iota
		f
		g = "k"
		h
		i
		j
		k
	)
	print(a, b, c, d, e, f, g, h, i, j, k)
}
```

```
012145kkkkk
```

```
package main

import "fmt"
const (
    i=1<<iota
    j=3<<iota
    k
    l
)

func main() {
    fmt.Println("i=",i)
    fmt.Println("j=",j)
    fmt.Println("k=",k)
    fmt.Println("l=",l)
}
```

```
i= 1
j= 6
k= 12
l= 24
```

### Go语言运算符

```
算术运算符
关系运算符
逻辑运算符
位运算符
赋值运算符
其他运算符
```

## 函数

```
func function_name( [parameter list] ) [return_types] {
   函数体
}
```

函数定义解析：

func：函数由func开始声明

function_name：函数名称，函数名称和参数列表一起构成了函数签名

parameter list：参数列表，参数就像一个占位符，当函数被调用时，你可以将值传递给参数，参数列表指定的是参数类型、顺序、及参数的个数。参数是可选的，也就是说参数不可以包含参数

return_types：返回类型，函数返回一列值。return_types是该列值的数据类型，有些功能不需要返回值，所以return_types不是必须的

函数体：函数定义的代码集合

```go
package main

import "fmt"

func max(num1 int, num2 int) int {
	var result int
	if num1 > num2 {
		result = num1
	} else {
		return num2
	}
	return result
}

func main() {
	i := max(1, 2)
	fmt.Print(i)
}
```

### 函数返回多个值

Go函数可以返回多个值

```go
package main
import "fmt"
func swap(x, y string) (string, string) {
   return y, x
}

func main() {
   a, b := swap("Google", "Runoob")
   fmt.Println(a, b)
}
```

**一般情况下，Go语言使用的是值传递，即在调用过程中不会影响到实际参数**

****

### 变量作用域

局部变量

```go
package main
import "fmt"
func main() {
	var a, b, c int
	a = 1
	b = 2
	c = a + b
	fmt.Print(c)
}
```

****

全局变量

在函数体外声明的变量称之为全局变量，全局变量可以在整个包甚至在外部包使用

全局变量可以在任何函数中使用

```go
package main

import "fmt"

/* 声明全局变量 */
var g int

func main() {

   /* 声明局部变量 */
   var a, b int

   /* 初始化参数 */
   a = 10
   b = 20
   g = a + b

   fmt.Printf("结果： a = %d, b = %d and g = %d\n", a, b, g)
}
```

Go 语言程序中全局变量与局部变量名称可以相同，但是函数内的局部变量会被优先考虑。实例如下：

```go
package main
import "fmt"
/* 声明全局变量 */
var g int = 20
func main() {
   /* 声明局部变量 */
   var g int = 10
   fmt.Printf ("结果： g = %d\n",  g)
}
```

```
结果： g = 10
```

****

Go语言数组

声明数组

```
var variable_name [SIZE] variable_type
```

```
var balance [10] float32
```

初始化数据

```
var balance = [5]float32{1000.0, 2.0, 3.4, 7.0, 50.0}
```

如果忽略[]中的数字不设置数组大小，Go语言会根据元素的个数来设置数组的大小

```
var balance = [...]float32{1000.0, 2.0, 3.4, 7.0, 50.0}
```

该实例与上面的实例是一样的，虽然没有设置数组的大小

```
 balance[4] = 50.0
```

访问数组元素

```go
package main

import "fmt"

func main() {
	var n [10]int
	var i, j int
	for i = 0; i < 10; i++ {
		n[i] = i + 100
	}

	for j = 0; j < 10; j++ {
		fmt.Printf("Element[%d] = %d\n", j, n[j])
	}
}
```

****

### 指针

```go
package main
import "fmt"
func main() {
	var a int = 1
	fmt.Printf("变量的地址: %x", &a)
}
```

```
变量的地址: c00000a0b0
```

指针申明

```go
var ip *int /*指向整型*/
var fp *float32 /*指向浮点型*/
```

指针使用流程

1. 定义指针变量
2. 为指针变量赋值
3. 访问指针变量中指向地址的值

```go
package main

import "fmt"

func main() {
	var a int = 20
	var ip *int

	ip = &a
	fmt.Printf("a 变量的地址是：%x\n", &a)

	fmt.Printf("ip 变量存储的指针地址：%x\n", ip)

	fmt.Printf("ip 变量的值：%d\n", *ip)
}
```

```
a 变量的地址是：c0000a2058
ip 变量存储的指针地址：c0000a2058
ip 变量的值：20
```

### 空指针

当一个指针被定义后没有分配到任何变量，它的值位nil

nil指针也成为空指针

nil在概念上和其他语言的null，None，nil，NULL一样，都指代零值或空值

一个指针变量通常缩写位ptr

```
package main

import "fmt"

func main() {
	var ptr *int

	fmt.Printf("ptr 的值为 : %x\n", ptr)

	fmt.Print(ptr == nil)
}
```

****

### 结构体

Go语言中数组可以存储同一类型的数据，但在结构体中我们可以认为不同项定义不同的数据类型

结构体是由一系列具有相同类型或不同类型的数据构成的数据集合

结构体格式如下：

```go
type struct_variable_type struct {
   member definition
   member definition
   ...
   member definition
}
```

一旦定义了结构体类型，它就能用于变量的声明，语法格式如下：

```go
variable_name := structure_variable_type {value1, value2...valuen}
或
variable_name := structure_variable_type { key1: value1, key2: value2..., keyn: valuen}
```

```go
package main
import "fmt"
type Books struct {
   title string
   author string
   subject string
   book_id int
}

func main() {

    // 创建一个新的结构体
    fmt.Println(Books{"Go 语言", "www.runoob.com", "Go 语言教程", 6495407})

    // 也可以使用 key => value 格式
    fmt.Println(Books{title: "Go 语言", author: "www.runoob.com", subject: "Go 语言教程", book_id: 6495407})

    // 忽略的字段为 0 或 空
   fmt.Println(Books{title: "Go 语言", author: "www.runoob.com"})
```

```go
package main

import "fmt"

type Student struct {
	name string
	age  int
}

func main() {
	student := Student{
		name: "gaoming",
		age:  0,
	}
	fmt.Print(student)
	fmt.Print(student.age)
	fmt.Print(student.name)
}
```

结构体作为函数参数

```go
package main

import "fmt"

type Books struct {
   title string
   author string
   subject string
   book_id int
}

func main() {
   var Book1 Books        /* 声明 Book1 为 Books 类型 */
   var Book2 Books        /* 声明 Book2 为 Books 类型 */

   /* book 1 描述 */
   Book1.title = "Go 语言"
   Book1.author = "www.runoob.com"
   Book1.subject = "Go 语言教程"
   Book1.book_id = 6495407

   /* book 2 描述 */
   Book2.title = "Python 教程"
   Book2.author = "www.runoob.com"
   Book2.subject = "Python 语言教程"
   Book2.book_id = 6495700

   /* 打印 Book1 信息 */
   printBook(Book1)

   /* 打印 Book2 信息 */
   printBook(Book2)
}

func printBook( book Books ) {
   fmt.Printf( "Book title : %s\n", book.title)
   fmt.Printf( "Book author : %s\n", book.author)
   fmt.Printf( "Book subject : %s\n", book.subject)
   fmt.Printf( "Book book_id : %d\n", book.book_id)
}
```

**结构体指针**

结构体指针定义

```
var struct_pointer *Books
```

存放地址

```
struct_pointer = &Book1
```

使用结构体指针访问结构体成员，使用"."操作符

```
struct_pointer.title
```

****

**语言切片**

Go语言切片是对数组的抽象

Go数组的长度不可改变，在特定场景下这样的集合不太适用，Go中提供了一种灵活、功能强悍的内置类型切片（"动态数组"），与数组相比切片的长度是不固定的，可以追加元素，在追加时可能使切片的容量增大

定义切片

```
var identifier []type
```

切片不需要说明长度

或使用make()函数来创建切片

```go
var slice1 []type = make([]type, len)
slice1 := make([]type, len)
```

也可以指定容量，其中capacity为可选参数

```go
make([]T, length, capacity)
```

这里 len 是数组的长度并且也是切片的初始长度。

**切片初始化**

```go
s := [] int {1,2,3}
```

直接初始化切片，[]表示是切片类型，{1,2,3}初始化值依次是1,2,3，其cap=len=3

```go
s := arr[:]
```

初始化切片s，是数组arr的引用

```go
s := arr[startIndex:endIndex]
```

将arr中从下标startIndex到endIndex-1下的元素创建为一个新的切片

```
s := arr[startIndex:]
```

默认startIndex时将表示从arr的第一个元素开始

```
s := arr[:endIndex]
```

默认endIndex时将表示一直到arr的最后一个元素

```
s1 := s[startIndex:endIndex] 
```

通过切片s初始化切片s1

```
s :=make([]int,len,cap) 
```

通过内置函数make()初始化切片s，[]int标识为其元素类型为int的切片

****

**len()和cap()函数**

切片是可索引的，并且可以由len()方法获取长度

切片提供了计算容量的方法cap()可以测量切片最长可以达到多少

```go
package main

import "fmt"

func main() {
	var numbers = make([]int,3,5)
	printSlice(numbers)
}

func printSlice(x []int) {
	fmt.Printf("len=%d cap=%d slice=%v\n",len(x),cap(x),x)
}
```

```
len=3 cap=5 slice=[0 0 0]
```

****

**空(nil)切片**

```go
package main
import "fmt"
func main() {
   var numbers []int
   printSlice(numbers)
   if(numbers == nil){
      fmt.Printf("切片是空的")
   }
}

func printSlice(x []int){
   fmt.Printf("len=%d cap=%d slice=%v\n",len(x),cap(x),x)
}
```

```
len=0 cap=0 slice=[]
切片是空的
```

****

**切片截取**

```go
package main

import "fmt"

func main() {
   /* 创建切片 */
   numbers := []int{0,1,2,3,4,5,6,7,8}  
   printSlice(numbers)

   /* 打印原始切片 */
   fmt.Println("numbers ==", numbers)

   /* 打印子切片从索引1(包含) 到索引4(不包含)*/
   fmt.Println("numbers[1:4] ==", numbers[1:4])

   /* 默认下限为 0*/
   fmt.Println("numbers[:3] ==", numbers[:3])

   /* 默认上限为 len(s)*/
   fmt.Println("numbers[4:] ==", numbers[4:])

   numbers1 := make([]int,0,5)
   printSlice(numbers1)

   /* 打印子切片从索引  0(包含) 到索引 2(不包含) */
   number2 := numbers[:2]
   printSlice(number2)

   /* 打印子切片从索引 2(包含) 到索引 5(不包含) */
   number3 := numbers[2:5]
   printSlice(number3)

}

func printSlice(x []int){
   fmt.Printf("len=%d cap=%d slice=%v\n",len(x),cap(x),x)
}
```

```
len=9 cap=9 slice=[0 1 2 3 4 5 6 7 8]
numbers == [0 1 2 3 4 5 6 7 8]
numbers[1:4] == [1 2 3]
numbers[:3] == [0 1 2]
numbers[4:] == [4 5 6 7 8]
len=0 cap=5 slice=[]
len=2 cap=9 slice=[0 1]
len=3 cap=7 slice=[2 3 4]
```

****

**append()和copy()函数**

如果想增加切片的容量，必须创建一个新的更大的切片并把原分片的内容都拷贝过来

```go
package main

import "fmt"

func main() {
   var numbers []int
   printSlice(numbers)

   /* 允许追加空切片 */
   numbers = append(numbers, 0)
   printSlice(numbers)

   /* 向切片添加一个元素 */
   numbers = append(numbers, 1)
   printSlice(numbers)

   /* 同时添加多个元素 */
   numbers = append(numbers, 2,3,4)
   printSlice(numbers)

   /* 创建切片 numbers1 是之前切片的两倍容量*/
   numbers1 := make([]int, len(numbers), (cap(numbers))*2)

   /* 拷贝 numbers 的内容到 numbers1 */
   copy(numbers1,numbers)
   printSlice(numbers1)  
}

func printSlice(x []int){
   fmt.Printf("len=%d cap=%d slice=%v\n",len(x),cap(x),x)
}
```

```
len=0 cap=0 slice=[]
len=1 cap=1 slice=[0]
len=2 cap=2 slice=[0 1]
len=5 cap=6 slice=[0 1 2 3 4]
len=5 cap=12 slice=[0 1 2 3 4]
```

****

### Go语言范围

go语言中range关键字用于for循环迭代数组（array），切片（slice）、通道（channel）或集合（map）的元素

在数组和切片中返回元素的索引和索引对应的值，在集合中返回key-value对

```go
package main
import "fmt"
func main() {
	nums := []int{2, 3, 4}
	sum := 0
	for _, num := range nums {
		sum += num
	}
	fmt.Println("sum:", sum)
	/*
		在数组上使用range将传入index和值两个变量
		上面不需要使用该元素，所以使用空白符"_"省略
		有时需要知道它的索引
	*/
	for i, num := range nums {
		if num == 3 {
			fmt.Println("index:", i)
		}
	}
	/*
		range也可以用在map的键值对上
	*/
	kvs := map[string]string{"a": "apple", "b": "banana"}
	for k, v := range kvs {
		fmt.Printf("%s->%s\n", k, v)
	}
	/*
		range也可以用来枚举unicode字符串
		第一个参数是字符的索引
		第二个参数是字符（unicode的值）本身
	*/
	for i, c := range "go" {
		fmt.Println(i, c)
	}
}
```

```
sum: 9
index: 1
a -> apple
b -> banana
0 103
1 111
```

****

**Map集合**

Map是一种无序的键值对的集合，Map最重要的一点是通过key来快速检索数据，key类似于索引，指向数据的值

Map是一种集合，所以我们可以向迭代数组和切片那样迭代它，不过，Map是无序的，我们无法决定它的返回顺序，这是因为Map是使用了Hash表来实现的

**定义Map**

可以使用内建函数make也可以使用map关键字来定义Map

```
/* 声明变量，默认 map 是 nil */
var map_variable map[key_data_type]value_data_type

/* 使用 make 函数 */
map_variable := make(map[key_data_type]value_data_type)
```

```Go
package main

import "fmt"

func main() {
	var countryCapitalMap map[string]string
	countryCapitalMap = make(map[string]string)

	/*
		map插入key-value对，各个国家对应的首都
	*/
	countryCapitalMap["France"] = "巴黎"
	countryCapitalMap["Italy"] = "罗马"
	countryCapitalMap["Japan"] = "东京"
	countryCapitalMap["India"] = "新德里"

	/*
		使用键输出地图值
	*/
	for country := range countryCapitalMap {
		fmt.Println(country, "首都是", countryCapitalMap[country])
	}
	/*
		查看元素在集合中是否存在
	*/
	capital, ok := countryCapitalMap["American"]
	if ok {
		fmt.Println("American 的首都是", capital)
	} else {
		fmt.Println("American 的首都不存在")
	}
}
```

**delete()函数**

delete()函数用于删除集合的元素，参数为map和其对应的key

```go
package main

import "fmt"

func main() {
	countryCapitalMap := map[string]string{"France": "Paris", "Italy": "Rome", "Japan": "Tokyo", "India": "New delhi"}
	fmt.Println("原始地图")

	// 打印地图
	for country := range countryCapitalMap {
		fmt.Println(country, "首都是", countryCapitalMap[country])
	}

	// 删除地图
	delete(countryCapitalMap, "France")
	fmt.Println("法国条目被删除")

	// 打印地图
	for country := range countryCapitalMap {
		fmt.Println(country, "首都是", countryCapitalMap[country])
	}
}
```

```
原始地图
France 首都是 Paris
Italy 首都是 Rome
Japan 首都是 Tokyo
India 首都是 New delhi
法国条目被删除
Italy 首都是 Rome
Japan 首都是 Tokyo
India 首都是 New delhi
```

****

### Go语言递归函数

递归，就是在运行过程中调用自己

```go
func recursion() {
   recursion() /* 函数调用自身 */
}

func main() {
   recursion()
}
```

Go 语言支持递归。但我们在使用递归时，开发者需要设置退出条件，否则递归将陷入无限循环中。

递归函数对于解决数学上的问题是非常有用的，就像计算阶乘，生成斐波那契数列等\

**斐波那契**

以下实例通过Go语言的递归函数

```
package main

import "fmt"

func Fac(n int) int {
	if n == 1 {
		return 1
	}
	if n == 2 {
		return 2
	}
	return Fac(n-1) + Fac(n-2)
}

func main() {
	fac := Fac(8)
	fmt.Println(fac)
}
```

****

### Go语言类型转换

```
类型转换用于将一种数据类型的变量转换为另外一种类型的变量。Go 语言类型转换基本格式如下：
```

```
type_name(expression)
```

type_name 为类型，expression 为表达式。

```go
package main
import "fmt"
func main() {
   var sum int = 17
   var count int = 5
   var mean float32
   mean = float32(sum)/float32(count)
   fmt.Printf("mean 的值为: %f\n",mean)
}
```

****

### 语言接口

```go
/* 定义接口 */
type interface_name interface {
   method_name1 [return_type]
   method_name2 [return_type]
   method_name3 [return_type]
   ...
   method_namen [return_type]
}

/* 定义结构体 */
type struct_name struct {
   /* variables */
}

/* 实现接口方法 */
func (struct_name_variable struct_name) method_name1() [return_type] {
   /* 方法实现 */
}
...
func (struct_name_variable struct_name) method_namen() [return_type] {
   /* 方法实现*/
}
```

```go
package main

import (
    "fmt"
)

type Phone interface {
    call()
}

type NokiaPhone struct {
}

func (nokiaPhone NokiaPhone) call() {
    fmt.Println("I am Nokia, I can call you!")
}

type IPhone struct {
}

func (iPhone IPhone) call() {
    fmt.Println("I am iPhone, I can call you!")
}

func main() {
    var phone Phone

    phone = new(NokiaPhone)
    phone.call()

    phone = new(IPhone)
    phone.call()
}
```

****

**Go错误处理**

go语言通过内置的错误接口，提供了非常简单的错误处理机制

error类型是一个接口类型，这是它的定义

```go
type error interface{
    Error() string
}
```

我们可以在编码中通过实现error接口类型来生成错误信息

函数通常在最后的返回值中返回错误信息，使用errors.New可返回一个错误信息

```go
func Sqrt(f float64) (float64, error) {
    if f < 0 {
        return 0, errors.New("math: square root of negative number")
    }
    // 实现
}
```

****

### Go并发

Go语言支持并发，只需要通过go关键字来开启goroutine即可

goroutine是轻量级线程，goroutine的调度是由GoLang运行时进行管理的

goroutine语法格式

```
go 函数名（参数列表）
```

```
go f(x,y,z)
```

开启一个新的goroutine

```
f(x,y,z)
```

Go允许使用go语句开启一个新的运行期线程，即goroutine，以一个不同的，新建的goroutine来执行一个函数。同一个程序中所有goroutine共享一个地址空间

```go
package main
import (
	"fmt"
	"time"
)
func say(s string) {
	for i := 0; i < 5; i++ {
		time.Sleep(100 * time.Millisecond)
		fmt.Println(s)
	}
}
func main() {
	go say("world")
	say("hello")
}
```

****

### 通道

通道是用来传递数据的一个数据结构

通道可以用于两个goroutine之间通过传递一个指定类型的值来同步运行和通讯

操作符<-用于指定通道的方向，发送或接收

如果未指定方向，则为双向通道

```
ch <- v // 把V发送到通道ch
v := <-ch // 从 ch 接收数据 并把值赋给 v
```

声明一个通道很简单，我们使用chan关键字即可，通道在使用前必须先创建

```
ch := make(chan int)
```

**注意**

默认情况下，通道是不带缓冲区的，发送端发送数据，同时必须有接收端相应的接收数据

以下实例通过两个goroutine来计算数字之和，在goroutine完成计算之后，他会计算两个结果的和

```go
package main

import "fmt"

func sum(s []int, c chan int) {
        sum := 0
        for _, v := range s {
                sum += v
        }
        c <- sum // 把 sum 发送到通道 c
}

func main() {
        s := []int{7, 2, 8, -9, 4, 0}

        c := make(chan int)
        go sum(s[:len(s)/2], c)
        go sum(s[len(s)/2:], c)
        x, y := <-c, <-c // 从通道 c 中接收

        fmt.Println(x, y, x+y)
}
```

```
-5 17 12
```

### 通道缓冲区

通道可以设置缓冲区，通过make的第二个参数指定缓冲区的大小

```go
ch := make(chan int, 100)
```

带缓冲区的通道允许发送端的数据发送和接收端的数据获取处于异步状态，就是说发送端发送的数据可以放在缓冲区里面，可以等待接收端去获取数据，而不是立刻需要接收端去获取数据

不过由于缓冲区的大小是有限的，所以还是必须有接收端来接收数据的，否则缓冲区一满，数据发送端就无法再发送数据了

**注意**

如果通道是不带缓冲的，发送方会阻塞直到接收方从通道中接收了值，如果通道带缓冲，发送方则会阻塞直到发送的值被拷贝到缓冲区内，如果缓冲区已满，则意味着需要等待直到某个接收方获取到一个值。接收方在有值可以接收之前会一直阻塞。

```go
package main

import "fmt"

func main() {
	// 这里我们定义了一个可以存储整数类型的带缓冲通道
	// 缓冲区大小为2
	ch := make(chan int, 2)

	// 因为ch是带缓冲的通道，我们可以同时发送两个数据
	// 而不用立刻需要去同步读取数据
	ch <- 1
	ch <- 2

	// 获取这两个数据
	fmt.Println(<-ch)
	fmt.Println(<-ch)
}
```

```
1
2
```

### Go遍历通道与关闭通道

go通过range关键字来实现遍历读取到的数据，类似于与数组或切片。格式如下

```
v, ok := <-ch
```

如果通道接收不到数据后ok就为false，这时通道就可以使用close()函数来关闭

```go
package main
import "fmt"
func fibonacci(n int, c chan int) {
	x, y := 0, 1
	for i := 0; i < n; i++ {
		c <- x
		x, y = y, x+y
	}
	close(c)
}

func main() {
	c := make(chan int, 10)
	go fibonacci(cap(c), c)
	for i := range c {
		fmt.Println(i)
	}
}
```



