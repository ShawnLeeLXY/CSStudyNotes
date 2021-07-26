# Linux操作系统多进程与多线程编程

## 多进程编程

`fork()`函数用生成子进程（复制进程）

```c
//fork函数的生成常用代码段
#include <stdio.h>
#include <unistd.h>

int main() {
    pid_t pid;
	if ((pid = fork()) < 0) { //赋值运算符的值等于左值的地址在同level所有运算结束后的值
    	perror("fork");
	}
	if (pid == 0) { //子进程fork返回值为0
    	printf("Child Process!\n");
	} else { //父进程fork返回值为子进程的pid
    	printf("Parent Process!\n");
	}
    return 0;
}
```



`wait()`函数用来让父进程等待子进程结束以释放资源



进程的变身：`exec`函数族  
形式：`exec[lv][pe]`    

- l: list 列表提供参数
- v: vector 数组（矢量）提供参数
- p: path 用户的path路径
- e: environment 提供新的环境变量



> **进程**的概念：  
>
> - 进程是操作系统中资源分配与调度的最小单位
> - 进程是程序在内存中的镜像

进程的退出：`exit`

### 多进程编程练习

> 创建100个子进程

```c
#include <stdio.h>
#include <stdlib.h>
#Include <unistd.h>
#include <sys/wait.h>

int main() {
    pid_t pid;
    int i;
    for (i = 0; i < 100; i++) {
        if ((pid = fork()) < 0) {
            perror("fork");
            exit(1);
        } else if (pid == 0) {
            printf("I'm the %dth child process!\n", i + 1);
            break;
        }
    }
    if (pid == 0) {
        sleep(1);
    } else {
        for (int j = 1; j <= 100; j++) {
            wait(NULL); //父进程在此等待所有子进程的结束，没有此行将产生僵尸进程
        }
    }
    return 0;
}
```





## 多线程编程

> **线程**的概念：  
>
> - 线程是操作系统调度器可调度的最小执行单元
> - 一个进程至少包含一个线程，一个进程的多个线程共享内存空间

> 多线程的好处：  
>
> 1. 编程抽象模块化
> 2. 增加程序的并发性
> 3. 提高响应能力
> 4. IO阻塞可行
> 5. 上下文切换代价小
> 6. 内存保存，线程之间共享内存，切换无需置换内存

**线程的模式**
每个连接对应一个线程
事件驱动的线程模式

**线程的竞争**
竞争发生的窗口，也就是需要同步的代码区成为**临界区**

**线程的同步**
让原先不确定的访问顺序变得确定，从而解决线程竞争

**创建线程**  
`pthread_create`函数



**进程ID**是由**内核**分配的，而**线程ID**是由**线程库**分配的



**线程终止：**  

1. 返回
2. pthread_exit()终止自己的线程
3. pthread_cancel()取消其他函数的线程

> 在以下场景中，所有线程都会被杀死，进程也被杀死：  
>
> 1. 进程从主函数中返回
> 2. 进程通过`exit()`函数终结

**线程的join和detach**  
`pthread_join()`支持一个线程阻塞，等待另一个线程终止  
`pthread_detach()`默认情况下，线程都是可join的，detach可以让其不可join

**线程互斥mutex**

含`pthread`函数的源文件编译需要加命令`-lpthread`，如`gcc 1.pthread.c -lpthread`

**Linux下使用gcc编译**  
`gcc xxx.c -o xxx -lpthread`



### 多线程编程练习

> 多线程累加，实现一个多线程程序，并发度设置为5，也就是5各线程协同计算，算从1累加到1000的值。

```c
#include <stdio.h>
#include <pthread.h>

#define THREAD_NUM 5

int sum;

void *accum(void *arg) {
    int num = (int)arg + 1;
    for (int i = 0; num <= 1000; num += 5) {
        sum += num;
    }
    printf("I'm pthread %d and my sum result is %d\n", arg + 1, sum);
    return NULL;
}

int main() {
    int i;
    pthread_t *acc[THREAD_NUM];
    for (i = 0; i < THREAD_NUM; i++) {
        if (pthread_create(&acc[i], NULL, accum, (void *)i)) {
            printf("pthread %d creation failure!\n", i);
            pthread_exit(NULL);
        }
    }
    for (i = 0; i < THREAD_NUM; i++) {
        pthread_join(acc[i], NULL);
    }
    printf("sum = %d\n", sum);
    return 0;
}
```







# Linux操作系统复习

## 1 命令忘了怎么办

1. table键命令自动补全
2. `history`命令查看命令输入历史（然后可结合`grep`进行检索）
3. 查看`man`手册
4. `sudo apt-cache search xxx`命令查看xxx安装记录
5. 搜索引擎



## 2 查看帮助

1. 直接运行命令，系统会有`help`的反馈
2. 在命令后加`-h`或`--help`
3. `man`手册（在vim编辑器中时，光标停在一个函数名上，按shift+k（或K）可快速进入`man`手册）
4. `tldr`工具
5. 找程序员社区



## 3 stdio与管道

上一个命令的标准输出作为下一个命令的标准输入

标准输出(stdio)：打印到屏幕上

所有linux中的io都是缓冲io

命令`echo "1.c" | cat`把`1.c`作为文本给`cat`读  
命令`echo "1.c" | xargs cat`把`1.c`转换成参数给`cat`读

词频统计命令：`tr`

0标准输入`stdin`  
1标准输出`stdout`  
2标准错误输出`stderr`

`/dev/null`：一个数据黑洞，使导入的数据消失。命令`2>/dev/null`可将标准错误导入数据黑洞从而不显示



## 4 Linux三剑客

Linux三剑客`grep`、`awk`、`sed`  
- `grep`：数据查找  
- `awk`：数据整理  
- `sed`：数据替换

`awk`是一种编程语言



## 5 程序、进程和线程

`exec()`函数仅在错误发生时返回值且返回值为-1

