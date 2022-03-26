# Golang

## 第1节 开发环境

### Go语言介绍

相关链接：

- [Go官方网站](https://golang.google.cn/)
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





### init和main函数

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





### 基本类型

空指针值为nil

字符串string内部实现采用UTF-8编码

string底层是一个byte数组

