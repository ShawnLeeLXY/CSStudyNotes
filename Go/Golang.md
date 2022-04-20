# Golang

## 第1节 开发环境

### Go语言介绍

相关链接：

- [Go官方网站](https://go.dev/)
- [Go国内官方网站](https://golang.google.cn/)
- [Go标准库中文文档](https://studygolang.com/pkgdoc)
- [Go语言之旅中文版](https://tour.go-zh.org/list)
- [Go语言简明教程](https://geektutu.com/post/quick-golang.html)
- [Go by Example中文版](https://gobyexample-cn.github.io/)
- [菜鸟教程-Go语言教程](https://www.runoob.com/go/go-tutorial.html)
- [地鼠文档](https://topgoer.cn/)
- [地鼠文档-Go中文学习文档](https://www.topgoer.com/)



官网对Golang的简介：

> - Go 是由Google支持的开源编程语言
> - 易于学习和入门
> - 内置并发和健壮的标准库
> - 持续发展的合作伙伴、社区和工具生态环境





### 配置环境

Windows环境配置步骤：

1. 官网下载对应操作系统的安装包或压缩包
2. 下载后安装或解压到可自定义的路径下
3. 打开新的命令行输入 `go version` 测试安装是否成功
4. 在系统变量里添加GOPATH，对应目录下必须有src、bin、pkg三个文件夹
5. 将GOPATH的路径添加进PATH
6. 在命令行输入 `go env` 查看GOPATH是否设置成功



Linux环境配置步骤：

1. 官网下载压缩包

2. 上传至Linux中

3. 如果之前存在/usr/local/go，需要先删除干净

4. 解压压缩包至/usr/local

   ```bash
   sudo tar -C /usr/local -zxvf go1.18.1.linux-amd64.tar.gz
   ```

5. 在$HOME/下创建gopath，并在gopath/下创建src、pkg、bin三个文件夹

6. 打开$HOME/.profile或/etc/profile（后者安装至整个操作系统）

7. 添加如下三行：

   ```bash
   export GOROOT=/usr/local/go
   export GOPATH=$HOME/gopath
   export PATH=$PATH:$GOROOT/bin:$GOPATH/bin
   ```

8. 命令行输入`source $HOME/.profile`或`source /etc/profile`

9. 命令行输入`go version`检查版本是否正确



GOPATH下的三个目录：

- **src**：源代码文件，可以存放多个项目
- **pkg**：包文件
- **bin**：可执行文件



IDE推荐：

- Goland
- VS Code：开源



第一个Go程序代码：

```go
package main

import "fmt"

func main() {
	fmt.Println("Hello, world!")
}

```







## 第2节 Go基础

### Golang的特点

Golang的主要特征：

1. 自动垃圾回收
2. 丰富的内置类型
3. 函数多返回值
4. 错误处理
5. 匿名函数和闭包
6. 类型和接口
7. 从语法层面支持并发
8. 反射
9. 语言交互性





### 基础语法

Go的关键字和保留字：

```go
/**
	Go只有27个关键字：

	break        default      func         interface    select
    case         defer        go           map          struct
    chan         else         goto         package      switch
    const        fallthrough  if           range        type
    continue     for          import       return       var

	37个保留字：

	Constants:    true  false  iota  nil

    Types:    int  int8  int16  int32  int64
              uint  uint8  uint16  uint32  uint64  uintptr
              float32  float64  complex128  complex64
              bool  byte  rune  string  error

    Functions:   make  len  cap  new  append  copy  close  delete
                 complex  real  imag
                 panic  recover
 */
```



可见性：

- 声明在函数**内部**，属于**本地值**（类似`private`）
- 声明在函数**外部**，对**当前包**的.go文件都可见（类似`protected`）
- 声明在函数**外部且首字母大写**，属于**全局值**，所有包均可见（类似`public`）



声明方式：

- `var`：变量
- `const`：常量
- `type`：类型
- `func`：函数



相关命令：

- `go build`：编译.go文件或包
- `go test`：测试编译后的包
- `go install`：安装编译后的包，在pkg下生成.a文件

可以直接在命令行输入`go`查看相关命令



每个包可以有多个**init**函数，用于包的初始化

- 同一个.go文件`init()`调用顺序从上到下
- 同一个包中按文件名字符串比较结果顺序调用
- 不同包：
  - 存在相互依赖，则按依赖先后顺序调用`init()`
  - 不存在依赖，则按main包中先`import`的后调用的顺序调用`init()`

**main**函数是程序的默认入口，只能存在于main包中



下划线的作用：

- `import`后：表示只调用包的`init()`
- 函数中：占位符



Golang中的switch分支表达式可以省略beark，默认自动终止

但是也可以使用`fallthrough`关键字强行执行后面的语句



`range`的返回参数：

| 类型        | 1st value | 2nd value |               |
| ----------- | --------- | --------- | ------------- |
| string      | index     | s[index]  | unicode, rune |
| array/slice | index     | s[index]  |               |
| map         | key       | m[key]    |               |
| channel     | element   |           |               |





### 变量和常量

声明和初始化：

```go
// 变量的声明
var name string
var age int
var isOk bool
var (
    a string
    b int
    c bool
    d float32
)
// 变量的初始化
var name string = "pprof.cn"
var sex int = 1
var name, sex = "pprof.cn", 1
var name = "pprof.cn"
var sex = 1
// 函数内部可以使用":="的方式声明
func main() {
    n := 10
}

// 常量的定义
const pi = 3.14
const (
	pi = 3.14
    e = 2.72
)
// iota是常量计数器，每一行计数一次
const (
    n1 = iota //0
    n2        //1
    _
    n4        //3
)
const (
    _  = iota
    KB = 1 << (10 * iota)
    MB = 1 << (10 * iota)
    GB = 1 << (10 * iota)
    TB = 1 << (10 * iota)
    PB = 1 << (10 * iota)
)
```



> 数值型常量没有确定的类型，直到被给定某个类型





### 数据类型简介

Golang的数据类型分为：

- 布尔型
- 数字类型
- 字符串类型
- 派生类型
  1. 指针
  2. 数组
  3. 结构体
  4. Channel
  5. 函数
  6. slice
  7. interface
  8. map

空指针值为nil

字符串string内部实现采用UTF-8编码

string底层是一个byte数组





### 输入与输出

`println()` 与 `fmt.Println()`的区别：

1. 前者在builtin包，不需要import；后者在fmt包，需要import
2. 前者输出到标准错误，后者输出到标准输出
3. 前者没有返回值，后者返回写入字节数和写入时发生的错误
4. 前者不接受数组或结构体作为参数

[Go语言初见println和fmt.Println区别](https://blog.csdn.net/m0_56289903/article/details/121079543)







## 第3节 派生数据类型

### 指针

Golang中的指针**不能进行偏移和运算**，是安全指针

- 指针地址

- 指针类型 如`*int`、`*string`等

- 指针取值

指针作为引用类型需要初始化后才会拥有内存空间，才可以给它赋值

可以使用`new()`函数对指针进行初始化

```go
func main() {
	var a *int
	a = new(int)
	*a = 10
	fmt.Println(*a)
}
```



`make()`函数用于且仅用于对slice、map和chan的内存创建





### 数组

Go中的数组：

- 数组是**值类型**
- 赋值和传参会**复制整个数组**，而不是指针。因此改变副本的值，不会改变本身的值
- 数组长度必须是常量，且是**其类型的组成部分**。一旦定义，长度不能变



```go
package main

import "fmt"

// 一维数组
var arr1 [5]int = [5]int{1, 2, 3} // "[5]int"可省略
var arr2 = [5]int{1, 2, 3, 4, 5}
var arr3 = [...]int{1, 2, 3, 4, 5, 6}
var arr4 = [5]string{3: "hello world", 4: "tom"}
var arr5 = [...]struct {
	key   string
	value int32
}{
	{"key1", 123},
	3: {"key2", 666},
	{"key3", 789},
}

// 多维数组
var arr6 [3][2]int
var arr7 = [...][3]int{{1, 2, 3}, {4, 5, 6}}

func arrDemo() {
	// 一维数组
	a := [3]int{1, 2}           // 未初始化元素值为 0。
	b := [...]int{1, 2, 3, 4}   // 通过初始化值确定数组长度。
	c := [5]int{2: 100, 4: 200} // 使用索引号初始化元素。
	d := [...]struct {
		name string
		age  uint8
	}{
		{"user1", 10}, // 可省略元素类型。
		{"user2", 20}, // 别忘了最后一行的逗号。
	}
	// 多维数组
	e := [...][2]int{{1, 1}, {2, 2}, {3, 3}}

	fmt.Println(arr1)
	fmt.Println(arr2)
	fmt.Println(arr3)
	fmt.Println(arr4)
	fmt.Println(arr5)
	fmt.Println(arr6)
	fmt.Println(arr7)
	fmt.Println(a, b, c, d, e)
	// len()和cap()都可返回数组长度
	fmt.Println(len(arr1), len(arr2), len(arr3), len(arr4), len(arr5))
	fmt.Println(len(a), len(b), len(c), len(d), len(e))
	fmt.Println(cap(a), cap(b), cap(c), cap(d), cap(e))

	// 多维数组遍历
	for k1, v1 := range e {
		for k2, v2 := range v1 {
			fmt.Printf("(%d, %d)=%d ", k1, k2, v2)
		}
		fmt.Println()
	}

	// 数组传参
	f := [5]int32{1, 2, 3, 4, 5}
	fmt.Println("f =", f)
	printArr(&f)
}

func printArr(arr *[5]int32) {
	arr[1] = 10
	fmt.Println("arr =", arr)
}

/*
输出结果：
[1 2 3 0 0]
[1 2 3 4 5]
[1 2 3 4 5 6]
[   hello world tom]
[{key1 123} { 0} { 0} {key2 666} {key3 789}]
[[0 0] [0 0] [0 0]]
[[1 2 3] [4 5 6]]
[1 2 0] [1 2 3 4] [0 0 100 0 200] [{user1 10} {user2 20}] [[1 1] [2 2] [3 3]]
5 5 6 5 5
3 4 5 2 3
3 4 5 2 3
(0, 0)=1 (0, 1)=1
(1, 0)=2 (1, 1)=2
(2, 0)=3 (2, 1)=3
f = [1 2 3 4 5]
arr = &[1 10 3 4 5]
*/

```





### 切片

**切片**是数组的一个引用，提供了一个指向数组的**动态窗口**

```go
package main

import "fmt"

var array = [...]int{10, 20, 30, 40, 50, 60, 70, 80, 90}

func sliceDemo1() {
	// 声明切片
	var s1 []int
	if s1 == nil {
		fmt.Println("空")
	} else {
		fmt.Println("非空")
	}
	s2 := []int{}
	// make([]type, len)
	// make([]type, len, cap)
	var s3 = make([]int, 0)
	var s4 = make([]int, 0, 0)
	fmt.Println(s1, s2, s3, s4)
	s5 := []int{1, 2, 3} // 注意没有省略号
	fmt.Println(s5)
	// 从数组切片
	arr := [5]int{1, 2, 3, 4, 5}
	var s6 []int
	// 前包后不包
	s6 = arr[1:4]
	fmt.Println(s6)
	// 切片的len=high-low, cap=max-low
	fmt.Println("len =", len(s6), "cap =", cap(s6))

	fmt.Println("----------")

	fmt.Println(array[3:6])
	fmt.Println(array[:6])
	fmt.Println(array[3:])
	fmt.Println(array[:])
	fmt.Println(array[:len(array)-1])

	fmt.Println("----------")

	// 切片是数组的引用
	data := [...]int{0, 1, 2, 3, 4, 5, 6, 7, 8, 9}
	s7 := data[2:5]
	s7[0] += 100
	s7[1] += 200
	p := &s7[2]
	*p += 300
	fmt.Println(s7)
	fmt.Println(data)

	fmt.Println("----------")

	// 通过初始化表达式构造，可使用索引号
	s8 := []int{0, 1, 2, 3, 8: 100}
	fmt.Println(s8, len(s8), cap(s8))
	// 使用 make 创建，指定 len 和 cap 值
	s9 := make([]int, 6, 8)
	fmt.Println(s9, len(s9), cap(s9))
	// 省略 cap，相当于 cap = len
	s10 := make([]int, 6)
	fmt.Println(s10, len(s10), cap(s10))

	fmt.Println("----------")

	arrs := [5]struct {
		x int
	}{}
	s := arrs[:]
	arrs[1].x = 10
	s[2].x = 20
	fmt.Println(arrs)
	fmt.Printf("%p, %p\n", &arrs, &arrs[0])

}

/*
输出结果：
空
[] [] [] []
[1 2 3]
[2 3 4]
len = 3 cap = 4
----------
[40 50 60]
[10 20 30 40 50 60]
[40 50 60 70 80 90]
[10 20 30 40 50 60 70 80 90]
[10 20 30 40 50 60 70 80]
----------
[102 203 304]
[0 1 102 203 304 5 6 7 8 9]
----------
[0 1 2 3 0 0 0 0 100] 9 9
[0 0 0 0 0 0] 6 8
[0 0 0 0 0 0] 6 6
----------
[{0} {10} {20} {0} {0}]
0xc00000a540, 0xc00000a540
*/

func sliceDemo2() {
	// append: 向 slice 尾部添加数据
	// 若追加元素后len<=cap，则地址不变
	// 若追加元素后len>cap，则重新开辟2*cap的空间去存储新slice
	a := []int{1, 2, 3}
	fmt.Printf("slice a : %v\n", a)
	b := []int{4, 5, 6}
	fmt.Printf("slice b : %v\n", b)
	c := append(a, b...)
	fmt.Printf("slice c : %v\n", c)
	d := append(c, 7)
	fmt.Printf("slice d : %v\n", d)
	e := append(d, 8, 9, 10)
	fmt.Printf("slice e : %v\n", e)
	fmt.Printf("%p\n", &a)
	fmt.Printf("%p\n", &b)
	fmt.Printf("%p\n", &c)
	fmt.Printf("%p\n", &d)
	fmt.Printf("%p\n", &e)

	fmt.Println("----------")

	// 复制slice
	arr := [...]int{0, 1, 2, 3, 4, 5, 6, 7, 8, 9}
	fmt.Println("arr =", arr)
	s1 := arr[:5]
	s2 := arr[8:]
	fmt.Printf("s1 = %v\n", s1)
	fmt.Printf("s2 = %v\n", s2)
	copy(s2, s1)
	fmt.Printf("copied s1 = %v\n", s1)
	fmt.Printf("copied s2 = %v\n", s2)
	fmt.Println("copied arr =", arr)

	fmt.Println("----------")

	str := "hello world"
	s3 := []byte(str)
	fmt.Println("s3 =", string(s3))
	fmt.Printf("str: %p, s3: %p\n", &str, s3)
	s3 = s3[:8]
	s3[6] = 'G'
	fmt.Println("s3 =", string(s3))
	fmt.Printf("str: %p, s3: %p\n", &str, s3)
	s3 = append(s3, '!')
	fmt.Println("s3 =", string(s3))
	fmt.Printf("str: %p, s3: %p\n", &str, s3)
}

/*
输出结果：
slice a : [1 2 3]
slice b : [4 5 6]
slice c : [1 2 3 4 5 6]
slice d : [1 2 3 4 5 6 7]
slice e : [1 2 3 4 5 6 7 8 9 10]
0xc000004078
0xc0000040a8
0xc0000040d8
0xc000004108
0xc000004138
----------
arr = [0 1 2 3 4 5 6 7 8 9]
s1 = [0 1 2 3 4]
s2 = [8 9]
copied s1 = [0 1 2 3 4]
copied s2 = [0 1]
copied arr = [0 1 2 3 4 5 6 7 0 1]
----------
s3 = hello world
str: 0xc00004c250, s3: 0xc00001a220
s3 = hello Go
str: 0xc00004c250, s3: 0xc00001a220
s3 = hello Go!
str: 0xc00004c250, s3: 0xc00001a220
*/

```



切片中容量不够时，将重新分配底层数组并复制数据，通常容量扩充为原先的2倍

slice可以组成多维数据结构，内部的slice长度可以不一致，这一点和多维数组不同

数组的内存分配通常在栈上，切片的内存分配通常在堆上



slice的数据结构定义：

```go
type slice struct {
	array unsafe.Pointer
	len   int
	cap   int
}
```





### Map

Golang中的Map是引用类型，必须初始化才能使用





### 结构体

> Go语言中的**方法**（Method）是一种作用于特定类型变量的函数。这种特定类型变量叫做**接收者**（Receiver）。接收者的概念就类似于其他语言中的this或者 self。

方法的格式：

`func (接收者变量 接收者类型) 方法名(参数列表) (返回参数) {函数体}`

> 方法与函数的区别是，函数不属于任何类型，方法属于特定的类型。

*不能给别的包的类型定义方法*

> 结构体中字段大写开头表示可公开访问，小写表示私有（仅在定义当前结构体的包中可访问）。







## 第4节 函数与方法

### 函数

**函数**也是一种**派生数据类型**，一个函数可以像普通变量一样被传递或使用



参数传递方式：

- **值传递**：在调用函数时将实际参数复制一份传递到函数中，这样在函数中如果对参数进行修改，将不会影响到实际参数
- **引用传递**：在调用函数时将实际参数的地址传递到函数中，那么在函数中对参数所进行的修改，将影响到实际参数

Golang默认采用值传递，但天生就是指针的类型，包括slice、map、channel、指针、interface采用引用传递



Golang可变参数本质上就是一个slice



**闭包**（Closure）是引用了自由变量的函数

闭包=函数+引用环境





### 延迟调用

defer特性：

1. 关键字 `defer` 用于注册延迟调用
2. 这些调用直到 `return` 前才被执行，因此可以用来做资源清理
3. 多个defer语句，按**先进后出**的方式执行
4. defer语句中的变量，在defer声明时就决定了



> defer后面的语句在执行的时候，函数调用的**参数**会被保存起来，但是不执行。也就是复制了一份。但是并没有说struct这里的this指针如何处理，通过这个例子可以看出go语言并没有把这个明确写出来的this指针当作参数来看待。



> 多个 defer 注册，按 FILO 次序执行 ( 先进后出 )。哪怕函数或某个延迟调用发生错误，这些调用依旧会被执行。





### 异常处理

Golang中 `panic` 用于抛出错误，`recover` 用于捕获错误，`error` 用于表示错误对象

`panic()` 之后的代码都不执行

`recover()` 只有在 `defer` 内直接调用才有效，否则总是返回nil

标准库 `errors.New()` 和 `fmt.Errorf()` 用于创建实现error接口的错误对象

演示代码：

```go
package main

import (
	"errors"
	"fmt"
)

func errDemo() {
	errTest1()
	// 输出"panic error!"

	errTest2()
	// 输出：
	// defer inner
	// <nil>
	// test panic

	errTest3()
	// 输出"test panic"

	errTest4()
	// 输出"divided by zero"
}

func errTest1() {
	defer func() {
		if err := recover(); err != nil {
			println(err.(string)) // 将interface{}转型为具体类型
		}
	}()

	panic("panic error!")
}

func errTest2() {
	defer func() {
		fmt.Println(recover()) // 有效
	}()
	defer recover()              // 无效！
	defer fmt.Println(recover()) // 无效！
	defer func() {
		func() {
			println("defer inner")
			recover() // 无效！
		}()
	}()

	panic("test panic")
}

func errTest3() {
	defer except() // 有效
	panic("test panic")
}

func except() {
	fmt.Println(recover())
}

var ErrDivByZero = errors.New("divided by zero")

func divTest(x, y int) (int, error) {
	if y == 0 {
		return 0, ErrDivByZero
	}
	return x / y, nil
}

func errTest4() {
	defer func() {
		fmt.Println(recover())
	}()
	switch z, err := divTest(10, 0); err {
	case nil:
		println(z)
	case ErrDivByZero:
		panic(err)
	}
}

```



> 延迟调用中引发的错误，可被后续延迟调用捕获，但仅最后一个错误可被捕获。





### 方法

一个方法就是一个包含了接收者的函数

方法格式：

```go
// 值类型接收者 方法内部对对象的副本进行操作
func (receiver T) methodName(args) (retVals) {
    // code
}

// 指针类型接收者 方法内部对指针进行操作
func (receiver *T) methodName(args) (retVals) {
    // code
}
```







## 第5节 面向对象

### 匿名字段

通过**匿名字段**可以实现类似**继承**的覆盖能力

```go
package main

import "fmt"

type User struct {
    id   int
    name string
}

type Manager struct {
    User // 匿名字段
    title string
}

func (u *User) ToString() string {
    return fmt.Sprintf("User: %p, %v", u, u)
}

func (m *Manager) ToString() string {
    return fmt.Sprintf("Manager: %p, %v", m, m)
}

// 通过匿名字段实现继承的覆盖能力
func main() {
    m := Manager{User{1, "Tom"}, "Administrator"}
    fmt.Println(m.ToString())
    fmt.Println(m.User.ToString())
}
```





### 接口

Golang中接口**interface**是一种**类型**

接口定义格式：

```go
type interfaceName interface {
    method1(args...) retVals
    method2(args...) retVals
    // ...
}

// for example:
type writer interface {
    Write([]byte) error
}
```



一个类型可以实现多个接口

如果一个类型实现了接口中的全部方法，那么就实现了这个接口

一个接口的方法，不一定需要由一个类型完全实现，接口的方法可以通过在类型中嵌入其他类型来实现

接口与接口间可以通过嵌套创造出新的接口



**空接口**是指没有定义任何方法的接口

`var x interface{}`

**任何类型**都实现了空接口，换言之空接口可以存储任意类型的值

`x = anyVar`

空接口作为map的值：

```go
var studentInfo = make(map[string]interface{})
studentInfo["name"] = "李白"
studentInfo["age"] = 18
studentInfo["married"] = false
fmt.Println(studentInfo)
```







## 第6节 并发编程

### goroutine

**协程**的概念：

- 协程是**轻量级**的线程
- 一个线程上可以运行多个协程
- 栈空间独立，堆空间共享
- 本质上类似于用户级线程



**goroutine**：

- 由官方实现的超级“线程池”
- 由**runtime**调度和管理，调度是在用户态下完成的
- goroutine中的任务会自动地合理分配给每个CPU
- 每个实例仅占**4~5KB**
- 提倡通过通信来共享内存，而不是共享内存来通信
- 一个goroutine对应一个函数
- goroutine的栈初始时通常为2KB，可**按需增大和缩小**



程序启动时会为 `main()` 创建一个默认的goroutine

当 `main()` 函数结束的时候该goroutine就结束了，所有在 `main()` 函数中启动的goroutine会一同结束



```go
// 一个简单的多线程同步示例
func goroutineDemo() {
	for i := 0; i < 10; i++ {
		wg.Add(1) // 启动一个goroutine就登记+1
		go hello(i)
	}
	wg.Wait() // 等待所有登记的goroutine都结束
}

func hello(i int) {
	defer wg.Done() // goroutine结束就登记-1
	fmt.Println("Hello Goroutine!", i)
}

var wg sync.WaitGroup
```



**GPM**是runtime层面实现的一套**调度系统**：

1. **G**代表存放本goroutine信息和P的绑定信息
2. **P**管理一组goroutine队列，并存储当前goroutine运行的上下文环境，与G一一映射
3. **M**是对操作系统内核线程的虚拟，与内核线程一般一一映射

*因此goroutine和OS线程是m:n的关系*

Go1.5版本后P的个数默认为物理线程数



Go并发的优势：

1. 并发编程相对容易（goroutine即涵盖了对进程、线程、协程的管理）
2. goroutine超轻量，创建、销毁成本低
3. 调度均在用户态完成，成本比操作系统调度OS线程低很多
4. 充分利用物理多核处理器的性能



**runtime**包的相关函数：

| 函数                 | 作用                                      |
| -------------------- | ----------------------------------------- |
| runtime.Gosched()    | 让当前goroutine让出CPU时间片              |
| runtime.Goexit()     | 退出当前goroutine                         |
| runtime.GOMAXPROCS() | 设置当前程序并发运行时占用的CPU逻辑核心数 |

Go1.5版本之前默认采用单核执行，1.5版本之后默认使用全部的CPU逻辑核心执行





### channel

channel的概念：

> 如果说goroutine是Go程序并发的执行体，channel就是它们之间的连接。channel是可以让一个goroutine发送特定值到另一个goroutine的通信机制。



channel的规则：

- FIFO

- 声明channel时需要为其指定一个数据类型

- 声明并初始化

  如 `var ch chan int = make(chan int)` 或 `ch := make(chan int)`

- 操作：

  - 发送和接收数据都使用`<-`

    如 `ch <- 10` 表示把10发送到ch中，`x := <- ch` 表示从ch中接收值并赋值给变量x

  - 关闭通道 `close(ch)`

    channel是可以被垃圾回收机制回收的，所以关闭channel不是必须的



**无缓冲通道**上的发送/接收将阻塞当前goroutine，直到channel的数据被另一个goroutine接收/发送

```go
func receiver(ch chan int) {
	temp := <-ch
	fmt.Println("接收成功：", temp)
}

func syncDemo() {
	ch := make(chan int)
	go receiver(ch) // 启用goroutine从通道接收值
	num := 10
	ch <- num
	fmt.Println("发送成功：", num)
}
```



只要通道的容量大于0，则该通道就是**有缓冲通道**

有缓冲的通道满了时就阻塞goroutine

```go
func chanDemo() {
	ch := make(chan int, 1) // 创建一个容量为1的有缓冲区通道
	ch <- 10                // main不会阻塞
	fmt.Println("发送成功")
}
```



判断channel是否关闭的方式常用的有两种：

1. 利用函数返回值
2. 利用for range循环

代码示例：

```go
// outCh是一个单向通道 对于goroutine来说只能发送
func counter(outCh chan<- int) {
	for i := 0; i < 100; i++ {
		outCh <- i
	}
	close(outCh)
}

// inCh是一个单向通道 对于goroutine来说只能接收
func squarer(outCh chan<- int, inCh <-chan int) {
	for {
		// 若通道关闭后再取值 ok==false
		i, ok := <-inCh
		if !ok {
			break
		}
		outCh <- i * i
	}
	close(outCh)
}

func printer(inCh <-chan int) {
	// 通道关闭后会退出for range循环
	for i := range inCh {
		fmt.Println(i)
	}
}

// 打印整数0~99的平方
func chanDemo4() {
	ch1 := make(chan int)
	ch2 := make(chan int)
	go counter(ch1)
	go squarer(ch2, ch1)
	printer(ch2)
}
```



在函数传参及任何赋值操作中将双向通道转换为单向通道是可以的，但反过来是不可以的



通过合理使用channel，可以手动实现goroutine池的效果



time包的 `time.NewTimer()` 可返回一个组合了`C <-chan Time`的Timer类型指针，结合chan的接收数据功能 `<- timer.C`，可实现**定时器**的功能





### select

**select**关键字可以**同时响应多个channel**的操作

```go
func selectDemo() {
	chSlice := make([]chan string, 5)
	for i := 0; i < 5; i++ {
		chSlice[i] = make(chan string)
		ind := i
		go func(ind int) {
			time.Sleep(time.Millisecond * (time.Duration)(rand.Int()%100))
			chSlice[ind] <- fmt.Sprintf("channel %d say: hello!\n", ind)
		}(ind)
	}
	// select同时监听多个chan 直到其中一个ready
	select {
	case s0 := <-chSlice[0]:
		fmt.Println(s0)
	case s1 := <-chSlice[1]:
		fmt.Println(s1)
	case s2 := <-chSlice[2]:
		fmt.Println(s2)
	case s3 := <-chSlice[3]:
		fmt.Println(s3)
	case s4 := <-chSlice[4]:
		fmt.Println(s4)
	}
}
```





### 锁

互斥锁：

```go
func mutexDemo() {
	var num int
	var wg sync.WaitGroup
	var lock sync.Mutex
	wg.Add(3)
	for i := 0; i < 3; i++ {
		go add(&num, &wg, &lock)
	}
	wg.Wait()
	fmt.Println(num) //3000
}

func add(num *int, wg *sync.WaitGroup, lock *sync.Mutex) {
	for i := 0; i < 1000; i++ {
		lock.Lock()
		*num += 1
		lock.Unlock()
	}
	wg.Done()
}
```



> 互斥锁是完全互斥的，但是有很多实际的场景下是**读多写少**的，当我们并发的去读取一个资源不涉及资源修改的时候是没有必要加锁的，这种场景下使用**读写锁**是更好的一种选择

读写锁：

```go
func RWMutexDemo() {
	var wg sync.WaitGroup
	var rwLock sync.RWMutex
	start := time.Now()
	wg.Add(1012)
	go func() {
		for i := 0; i < 1000; i++ {
			go read(&wg, &rwLock)
		}
		wg.Done()
	}()
	go func() {
		for i := 0; i < 10; i++ {
			go write(&wg, &rwLock)
		}
		wg.Done()
	}()
	wg.Wait()
	end := time.Now()
	fmt.Println("total time:", end.Sub(start))
}

func read(wg *sync.WaitGroup, rwLock *sync.RWMutex) {
	rwLock.RLock()
	time.Sleep(time.Millisecond) // 假设读操作用时1ms
	rwLock.RUnlock()
	wg.Done()
}

func write(wg *sync.WaitGroup, rwLock *sync.RWMutex) {
	rwLock.Lock()
	time.Sleep(time.Millisecond * 100) // 假设写操作用时100ms
	rwLock.Unlock()
	wg.Done()
}
```





### 原子操作

对于基本数据类型，可以使用标准库里的sync/atomic进行原子操作来保证并发安全，效率通常高于互斥锁

```go
func atomicDemo() {
	var num int64
	var wg sync.WaitGroup
	var lock sync.Mutex

	start := time.Now()
	for i := 0; i < 1000; i++ {
		wg.Add(1)
		go mutexAdd(&num, &lock, &wg)
	}
	wg.Wait()
	end := time.Now()
	fmt.Println("mutex用了", end.Sub(start))

	start = time.Now()
	for i := 0; i < 1000; i++ {
		wg.Add(1)
		go atomicAdd(&num, &wg)
	}
	wg.Wait()
	end = time.Now()
	fmt.Println("atomic用了", end.Sub(start))
}

// 互斥锁
func mutexAdd(num *int64, lock *sync.Mutex, wg *sync.WaitGroup) {
	lock.Lock()
	*num += 1
	lock.Unlock()
	wg.Done()
}

// 原子操作
func atomicAdd(num *int64, wg *sync.WaitGroup) {
	atomic.AddInt64(num, 1)
	wg.Done()
}
```







## 第7节 RPC

### RPC简介

**远程过程调用**（Remote Procedure Call，**RPC**）是一个计算机通信协议，该协议允许运行于一台计算机的程序调用另一台计算机的子程序，而程序员无需额外地为这个交互作用编程



Golang中封装好了实现RPC的库

net/rpc仅支持Go开发的服务器和客户端交互

net/rpc/jsonrpc支持跨语言调用

