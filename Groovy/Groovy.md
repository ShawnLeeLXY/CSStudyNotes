# Groovy

## 第1节 Groovy快速入门

### 1 简介

[Apache Groovy官方网站](http://www.groovy-lang.org/)

Groovy是一种**动态**/**敏捷**开发语言，在**Java虚拟机**中运行，同事具备**脚本**语言的特点

当运行Groovy脚本时它会先被编译成Java类字节码，然后通过JVM执行这个Java字节码类

Groovy可以极好地兼容Java语法

[Groovy官方文档](http://www.groovy-lang.org/documentation.html)

[Groovy语言快速入门](https://www.jianshu.com/p/e8dec95c4326)

常见使用场景：

- Grails：一个基于Groovy语言，构建于Spring/Spring Boot、Hibernate之上的高生产力、一站式Web开发框架。
- Gradle：一个基于Apache Ant和Apache Maven概念的项目自动化构建工具。它使用一种基于Groovy的特定领域语言(DSL)来进行构建配置，抛弃了基于XML的各种繁琐配置，主要以面向Java应用为主，支持从Maven仓库下载依赖。
- Spring支持：从Spring 2.0版本开始对动态脚本语言进行了支持，其中便包括Groovy，Spring提供了`<lang:groovy/>`标签来定义Groovy Bean。



### 2 安装与配置

[Groovy下载地址](https://groovy.apache.org/download.html)

下载完成后解压

配置系统环境变量：

1. 新建GROOVY_HOME = X:\xxx\\...\groovy-3.0.8
2. 修改PATH，新建%GROOVY_HOME%\bin

打开命令行，输入`groovy -v`，如果打印Groovy版本则说明配置成功



### 3 创建Groovy项目

[基于IntelliJ IDEA创建第一个Groovy工程](https://www.cnblogs.com/taoweizhong/p/10991434.html)

这里以IDEA为例创建Groovy项目（也可以新建.grrovy文件用命令行运行）

新建一个Project，选择Groovy，注意要选择Project SDK和Groovy library，默认是没有Groovy library的，选择右边的create按钮 ，之后选择自己安装Groovy的目录即可，然后点击next

右键src新建HelloWorld.groovy

```groovy
class HelloWorld {
    static void main(args) {
        println 'Hello, world!'
    }
}
```

右键Run，结果打印`Hello, world!`

再新建HelloAgain,groovy

```groovy
println 'Hello again, world!'
```

右键Run，结果打印`Hello again, world!`

说明两种格式Groovy都支持

事实上：

> 如果 Groovy 脚本文件里只有执行代码，没有类的定义，则 Groovy 编译器会生成一个 Script 的子类，类名和脚本文件的文件名一样，而脚本中的代码会被包含在一个名为run的方法中，同时还会生成一个main方法，作为整个脚本的入口。





## 第2节 Groovy与Java的区别

### 1 导包

Groovy默认（隐式）导包列表：

- java.io.*
- java.lang.*
- java.math.BigDecimal
- java.math.BigInteger
- java.net.*
- java.util.*
- groovy.lang.*
- groovy.util.*



### 2 多重方法

- Java：编译时根据声明的类型来选择方法
- Groovy：多重方法(multi-methods)的机制，即根据运行时参数的类型来选择方法

```groovy
int method(Object arg) {
    return 1;
}
int method(String arg) {
    return 2;
}
Object o = "Object";
int result = method(o);
// true in Java
println result == 1
// true in Groovy
println result == 2
```

运行结果打印：`false`和`true`



### 3 数组初始化语法

在Groovy中大括号用于闭包（closures）。所以Groovy的数组声明方式为：

```groovy
int[] arr = [1, 2, 3];
def arr2 = new int[] {1, 2, 3};//Groovy 3+
```

关于闭包见：第3节 - 6 闭包



### 4 包访问权限

对于Java普通类，Groovy 默认会隐式的创建getter、setter方法，并且会提供带参的构造器

```groovy
class Person {
    String name//默认创建setter、getter方法和带参构造器
    @PackageScope String name2//加上该注解后才真正私有
}
```



### 5 ARM语句块

Java7引入的特性ARM(Automatic Resource Management)：

```java
Path file = Paths.get("./test.txt");
Charset charset = Charset.forName("UTF-8");
try (BufferedReader reader = Files.newBufferedReader(file, charset)) {
    String line;
    while ((line = reader.readLine()) != null) {
        System.out.println(line);
    }

} catch (IOException e) {
    e.printStackTrace();
}
```

Groovy 3+开始支持这样的语句块

Groovy同等效果的惯用闭包如下：

```groovy
new File('./test.txt').eachLine('UTF-8') {
   println it
}
```

两者运行都可以打印出同路径下的test.txt文件内容，可以看到Groovy代码明显简洁许多



### 6 内部类

匿名内部类和嵌套类的实现与Java非常相似，但存在一些差异，例如从这些类中访问的局部变量不必是final的

Groovy 3.0.0+ 支持用于创建非静态内部类实例的 Java 风格语法，而此版本之前并不完美支持；但是静态内部类是完美支持的

```groovy
class A {
    static class B {}
}

new A.B()
```



### 7 Lambda表达式和方法引用符

Groovy 3+支持Java的Lambda表达式和方法引用符"::"

Groovy 3以下使用闭包实现：

```groovy
// Java 8 or Groovy 3+
Runnable run = () -> System.out.println("Run");
list.forEach(System.out::println);

// Groovy 3 below
Runnable run = { println 'run' }
list.each { println it } // or list.each(this.&println)
```



### 8 GString

GString：Groovy字符串

由于"xxx"这样的字符串字面量被解释为GString值，如果使用Groovy和Java编译器编译具有包含"$"的字符串字面量的类，则Groovy可能会编译错误或生成略有不同的代码。

通常情况下，如果API声明了参数的类型，Groovy会在GString和String之间自动转换，但仍需注意接受Object参数的API的实际参数类型。



### 9 字符串和字符字面量

在Groovy中，由单引号所创建的字面量属于String类型对象，而双引号创建的字面量则可能是String或GString对象，具体分类由字面量中是否有插值来决定。

```groovy
assert 'c'.getClass()==String
assert "c".getClass()==String
assert "c${1}".getClass() in GString
```

只有在声明为char类型的变量时，Groovy才会自动将单字符String转换为char



### 10 基础类型和包装类型

Groovy不支持Java扩展优于装箱的行为（Groovy对于对象有""多重方法""的机制）

```groovy
int i
m(i)

// Java调用
void m(long i) {
    println "in m(long i)"
}

// Groovy调用
void m(Integer i) {
    println "in m(Integer i)"
}
```



### 11 关系运算符"=="

在Groovy中：

- 判断a全等于b：`a ==b `
  - 对Comparable对象，转换为`a.compareTo(b) == 0`
  - 对其他对象，转换为`a.equals(b)`
- 判断引用a等于引用b：
  - `a.is(b)`
  - Groovy 3开始可以使用`a === b`



### 12 类型转换

Groovy的类型转换包含基本类型和包装类型、大数类，因此比Java的类型转换更复杂。

详细转换表参见官方文档



### 13 新增关键字

Groovy比Java新增了更多关键字，且Groovy对变量名和关键字的重名判定并不严格

- `var` Java 10以上及Groovy 3以上
- `as`
- `def` 省略变量类型的定义，根据变量的值进行类型推导
- `in`
- `trait`
- `it` 仅用于闭包中





## 第3节 基本语法

### 1 注释

```groovy
//单行注释

/*
多行注释
*/

/**
* 文档注释
*/

/*
#!/usr/bin/env groovy
UNIX系统支持的Shebang line单行注释
*/
```



### 2 变量

变量的默认修饰符`public`

变量名命名规范同Java

对于浮点型字面量为了精度 Groovy 默认使用的类型为 BigDecimal

```groovy
def decimal = 123.456
println decimal.getClass() // class java.math.BigDecimal
```



### 3 字符

```groovy
char c1 = 'A' //声明类型
assert c1 instanceof Character

def c2 = 'B' as char //用as关键字
assert c2 instanceof Character

def c3 = (char) 'C' //强制类型转换
assert c3 instanceof Character
```



### 4 字符串

- String普通字符串，单引号

- GString插值字符串，双引号

```groovy
println 'hello'

def name = 'Alice'
println "hello $name"

//三单引号'''用于保留文本的换行及缩进格式
```



### 5 方法

```groovy
String method1() {
    return 'hello'
}
assert method1() == 'hello';

def method2() {
    return 'hello'
}
assert method2() == 'hello';

def method3() {
    // code here
    // 默认返回最后一行代码的运行结果
    'hello'
}
assert method3() == 'hello';

def add(int a, int b) {
    return a + b
}
// 与上面的等价
def add(a, b) {
    a + b
}
```

Groovy支持句末不加分号`;`，还支持方法重载、不定长参数`...`等等



### 6 闭包

> 在Groovy中，闭包是一段匿名的代码段，它可以有参数，返回值，并且能够赋值给一个变量。闭包中使用的变量可以是在闭包外部定义的，也可以是在闭包内部定义的。

[Groovy Closure简介](https://blog.csdn.net/u014162857/article/details/54708831)

闭包在 Groovy 中是groovy.lang.Closure类的实例，这使得闭包可以赋值给变量，或者作为参数传递

```groovy
// 闭包格式，其中参数为可选
def closure = { [closureParameters -> ] statements }

def str = 'hello'
def closure = {
    println str
}
closure()//打印hello 

def closure = {
    param -> println param
}
closure('hello')
closure.call('hello')
closure 'hello'

def closure = { String x, int y ->                             
    println "hey ${x} the value is ${y}"
}

// Groovy提供了一个隐式的参数it来代替只有一个参数且省略其定义的情况
def closure = { it -> println it }
def closure = { println it }
// 以上两行等价
closure('hello')

// 闭包可作为参数传入
def eachLine(lines, closure) {
    for (String line : lines) {
        closure(line)
    }
}
eachLine('a'..'z',{ println it }) 
eachLine('a'..'z') { println it }
// 以上两行等价
```



### 7 数组

```groovy
// 直接类型定义或者通过as关键字指定类型
String[] arr = ['Alice', 'Betty', 'Cindy']
//def arr = ['Alice', 'Betty', 'Cindy'] as String[]
println arr instanceof String[]//ture
println arr instanceof List//false
```



### 8 List

```groovy
def list = [1, 2, 3]
// Groovy中的List其实就是java.util.List
assert list instanceof List
// 默认使用ArrayList实现
assert list.class == java.util.ArrayList
assert list.size() == 3
```



### 9 Map

```groovy
// key虽然没有加引号，不过Groovy会默认将其转换为字符串
def colors = [red: '#FF0000', green: '#00FF00', blue: '#0000FF']
assert colors['red'] == '#FF0000' //使用中括号访问
assert colors.green == '#00FF00' //使用点表达式访问

// 使用中括号添加元素
//相当于Java Map的 put(key, value)方法
colors['pink'] = '#FF00FF'
// 使用点表达式添加元素
colors.yellow = '#FFFF00'
assert colors.pink == '#FF00FF'
assert colors['yellow'] == '#FFFF00'
//默认使用LinkedHashMap实现
assert colors instanceof java.util.LinkedHashMap

// Groovy Map的key默认语法不支持变量
// 这里的key实际上是字符串'keyVal'而不是keyVal变量的值'keyName'
def keyVal = 'keyName'
def persons = [keyVal: 'someone'] 
assert !persons.containsKey('keyName')
assert persons.containsKey('keyVal')

// 要使用变量作为key，需要使用括号
def keyVal = 'keyName'
def persons = [(keyVal): 'Guillaume'] 
assert persons.containsKey('name')
assert !persons.containsKey('keyVal')
```



### 10 Range

```groovy
def range = 0..5
assert (0..5).collect() == [0, 1, 2, 3, 4, 5]
assert (0..<5).collect() == [0, 1, 2, 3, 4]//相当于左闭右开区间
assert (0..5) instanceof List//Range实际上是List接口的实例
assert (0..5).size() == 6
assert ('a'..'d').collect() == ['a','b','c','d']

for (x in 1..10) {
    println x
}

('a'..'z').each {
    println it
}
```

