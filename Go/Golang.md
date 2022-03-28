# Golang

## 第1节 开发环境

### Go语言介绍

相关链接：

- [Go官方网站](https://go.dev/)
- [Go国内官方网站](https://golang.google.cn/)
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

环境配置步骤：

1. 官网下载对应操作系统的安装包或压缩包
2. 下载后安装或解压到可自定义的路径下
3. 打开新的命令行输入 `go version` 测试安装是否成功
4. 在系统变量里添加GOPATH，对应目录下必须有src、bin、pkg三个文件夹
5. 将GOPATH的路径添加进PATH
6. 在命令行输入 `go env` 查看GOPATH是否设置成功



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





### Go基础语法

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





### 数据类型

空指针值为nil

字符串string内部实现采用UTF-8编码

string底层是一个byte数组





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

**切片**是数组的一个引用

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

