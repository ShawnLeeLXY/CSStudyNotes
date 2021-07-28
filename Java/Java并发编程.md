# Java并发编程

## 第1节 JUC概述

### 1 JUC简介

JUC是java.util.concurrent工具包的简称，一个用来处理线程的工具包，从JDK1.5开始出现



### 2 进程和线程

**进程**（process）：计算机中程序关于某数据集合上的一次运行活动

- **进程是系统进行资源分配和调度的最小单位**
- 进程是操作系统结构的基础
- 进程是程序在内存中的镜像
- 进程是线程的容器
- 每一个进程都有自己的内存空间和系统资源

**线程**（thread）：进程中的单个顺序控制流，是一条执行路径

- **线程是操作系统调度器可调度的最小执行单元**
- 进程的实际运作单位
- 一个进程至少包含一个线程，一个进程的多个线程共享内存空间



**管程**（Monitor）：监视器，即锁

管程一种同步机制，保证同一个时间，只有一个线程访问被保护的数据或者代码

JVM同步基于进入和退出，使用管程对象实现



**用户线程**：自定义线程

**守护线程**：后台运行的特殊线程，如垃圾回收器

主线程结束，若用户线程还在运行，JVM存活；若无用户线程运行了，只剩下守护线程，则JVM退出

守护线程代码演示：

```java
public class DaemonDemo {

    public static void main(String[] args) {
        // Lambda表达式实现Runnable接口
        Thread thread1 = new Thread(() -> {
            // true为守护线程 false为用户线程
            System.out.println(Thread.currentThread().getName() + ":" + Thread.currentThread().isDaemon());
            while (true) {
                // 主线程结束 若用户线程还在运行 JVM存活
                // 由于主线程被设为守护线程 故JVM退出
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println(Thread.currentThread().getName());
            }
        }, "thread1");
        // 设置守护线程
        thread1.setDaemon(true);
        thread1.start();
        System.out.println(Thread.currentThread().getName() + " is over.");
    }

}
```



### 3 线程状态

java.lang.Tread中的枚举类Sate枚举了6种线程状态：

```java
public enum State {
    /**
     * Thread state for a thread which has not yet started.
     */
    NEW,//新建

    /**
     * Thread state for a runnable thread.  A thread in the runnable
     * state is executing in the Java virtual machine but it may
     * be waiting for other resources from the operating system
     * such as processor.
     */
    RUNNABLE,//就绪

    /**
     * Thread state for a thread blocked waiting for a monitor lock.
     * A thread in the blocked state is waiting for a monitor lock
     * to enter a synchronized block/method or
     * reenter a synchronized block/method after calling
     * {@link Object#wait() Object.wait}.
     */
    BLOCKED,//阻塞

    /**
     * Thread state for a waiting thread.
     * A thread is in the waiting state due to calling one of the
     * following methods:
     * <ul>
     *   <li>{@link Object#wait() Object.wait} with no timeout</li>
     *   <li>{@link #join() Thread.join} with no timeout</li>
     *   <li>{@link LockSupport#park() LockSupport.park}</li>
     * </ul>
     *
     * <p>A thread in the waiting state is waiting for another thread to
     * perform a particular action.
     *
     * For example, a thread that has called <tt>Object.wait()</tt>
     * on an object is waiting for another thread to call
     * <tt>Object.notify()</tt> or <tt>Object.notifyAll()</tt> on
     * that object. A thread that has called <tt>Thread.join()</tt>
     * is waiting for a specified thread to terminate.
     */
    WAITING,//等待

    /**
     * Thread state for a waiting thread with a specified waiting time.
     * A thread is in the timed waiting state due to calling one of
     * the following methods with a specified positive waiting time:
     * <ul>
     *   <li>{@link #sleep Thread.sleep}</li>
     *   <li>{@link Object#wait(long) Object.wait} with timeout</li>
     *   <li>{@link #join(long) Thread.join} with timeout</li>
     *   <li>{@link LockSupport#parkNanos LockSupport.parkNanos}</li>
     *   <li>{@link LockSupport#parkUntil LockSupport.parkUntil}</li>
     * </ul>
     */
    TIMED_WAITING,//过时不候

    /**
     * Thread state for a terminated thread.
     * The thread has completed execution.
     */
    TERMINATED;//终结
}
```



### 4 wait和sleep

wait：

- wait是Object类的方法，所以任何对象实例都能调用
- wait会释放锁

sleep：

- sleep是Thread类的静态方法
- sleep不会释放锁，也不需要占用锁

wait()和sleep()都可以被Thread类的interrupt()方法中断



### 5 并发和并行

串行模式：多个任务按顺序单个执行

并行模式：多个任务同时执行



**并发**：同一时刻多个线程在访问同一个资源，多个线程对一个点

**并行**：多项工作一起执行，之后再汇总





## 第2节 Lock接口

### 1 synchronized关键字

synchronized是一种同步锁

synchronized作用范围：

1. 代码块：被大括号括起来的同步语句块
2. 方法：同步方法
3. 静态方法
4. 类



创建多线程的方式：

1. 继承Thread类
2. 实现Runnable接口
3. 使用Callable接口
4. 使用线程池



多线程编程步骤：

1. 创建资源类，在资源类创建属性和操作方法
2. 创建多个线程，在线程中调用资源类的操作方法



#### 卖票案例1

多线程编程代码演示：

```java
public class SellTicket {

    static class Ticket {

        // 票数
        private int number = 30;

        // 操作方法：卖票
        public synchronized void sell() {
            // 判断是否有票 有则卖一张票
            if (number > 0) {
                System.out.println(Thread.currentThread().getName() + "卖出了一张票，还剩下" + (--number) + "张票");
            }
        }

    }

    public static void main(String[] args) {
        // 创建资源类
        Ticket ticket = new Ticket();
        // 创建三个线程
        for (int i = 1; i <= 3; i++) {
            new Thread(new Runnable() {
                @Override
                public void run() {
                    for (int i = 0; i < 40; i++) {
                        ticket.sell();
                        try {
                            Thread.sleep(100);
                        } catch (InterruptedException e) {
                            e.printStackTrace();
                        }
                    }
                }
            }, "thread" + i).start();
        }

    }

}
```

运行结果：

<img src="Java并发编程.assets/sell-ticket.png" style="zoom: 67%;" />



### 2 ReentrantLock

Lock接口是java.util.concurrent.locks包下的一个接口

synchronized上锁和解锁是自动的，而Lock需要手动上锁和解锁

Lock接口下有ReentrantLock，ReentrantReadWriteLock.ReadLock，ReentrantReadWriteLock.WriteLock三个实现类



ReentrantLock是一种**可重入锁**



#### 卖票案例2

ReentrantLock代码演示：

```java
public class SellTicketLock {

    static class Ticket {
        // 票数
        private int number = 30;

        // 创建可重入锁
        private final ReentrantLock lock = new ReentrantLock();

        // 卖票方法
        public void sell() {
            // 上锁
            lock.lock();
            try {
                // 判断是否有票可卖
                if (number > 0) {
                    System.out.println(Thread.currentThread().getName() + "卖出了一张票，还剩下" + (--number) + "张票");
                }
            } catch (Exception e) {
                e.printStackTrace();
            } finally {
                // 解锁
                lock.unlock();
            }
        }

    }

    public static void main(String[] args) {
        // 创建资源类
        Ticket ticket = new Ticket();
        // 创建三个线程
        for (int i = 1; i <= 3; i++) {
            new Thread(() -> {
                for (int j = 0; j < 40; j++) {
                    ticket.sell();
                    try {
                        Thread.sleep(100);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            }, "thread" + i).start();
        }
    }

}
```

运行结果：

<img src="Java并发编程.assets/sell-ticket-2.png" style="zoom:67%;" />

