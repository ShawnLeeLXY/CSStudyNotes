# Python基础

*如无特殊说明，本篇所有笔记对应版本均以Python 3为准*



## 1 语法

Python是一种**解释型动态**语言

Python 中的变量**不需要声明**。每个变量在使用前都必须赋值，变量赋值以后该变量才会被创建

默认以**UTF-8**编码

可以用反斜杠`\`实现单行长语句变多行语句

缩进相同的一组语句构成一个代码块，称之**代码组**



注释：

```python
# 单行注释

'''
多行注释
'''

"""
多行注释
"""

print('Hello, world!')
```



六种标准的数据类型：

- 不可变数据：
  - **Number**
    - int（`0b100 == 4`，`0o100 == 100`，`0x100 == 256`）
    - float（123.456或1.23456e2）
    - bool（bool是int的子类，`True == 1` 且 `False == 0`）
    - complex（复数，j）
    
  - **String**（单引号或双引号括起来）
  
    Python中单引号`'`'和双引号`"`的使用完全相同
  
  - **Tuple**（元组）
  
- 可变数据：
  - **List**（列表）
  - **Dictionary**（字典）
  - **Set**（集合）



**不可变数据**意味着如果改变该类型的值，将**重新分配内存空间**



Python中没有用花括号来构造代码块而是使用了缩进的方式来设置代码的层次结构，如果`if`条件成立的情况下需要执行多条语句，只要保持多条语句具有相同的缩进就可以了

Python不支持函数重载，但是支持**可变参数**和**参数默认值**



> Python查找一个变量时会按照“局部作用域”、“嵌套作用域”、“全局作用域”和“内置作用域”的顺序进行搜索，......所谓的“内置作用域”就是Python内置的那些隐含标识符`min`、`len`等都属于内置作用域）。



### 列表

列表用`[]`把元素括起来

列表中的元素类型可以不相同

生成器对象不占用额外的空间存储数据

和字符串一样，列表同样可以被索引和截取

创建一个空列表： `list1 = []`



生成斐波那契数列：

```python
def fib(n):
    a, b = 0, 1
    for _ in range(n):
        a, b = b, a + b
        yield a


def main():
    for val in fib(20):
        print(val)


if __name__ == '__main__':
    main()
```



### 元组

Python 的元组与列表类似，不同之处在于元组的元素不能修改

元组用`()`把元素括起来

元组在创建时间和占用的空间上面都优于列表

创建一个空元组：`tuple1 = ()`

创建只有一个元素的元组：`tuple1 = (1,)`



### 集合

集合是一个无序不重复元素序列

Python中的集合跟数学上的集合是一致的，不允许有重复元素，而且可以进行交集、并集、差集等运算。

集合用`{}`把元素括起来

set能进行集合运算

创建一个空集合：`set1 = set()`



### 字典

字典是一种键值对容器，键必须是唯一的，且键不可变

格式：`dict1 = {key1 : val1, key2 : val2, key3 : val3}`

创建一个空字典：`dict1 = {}`





## 2 面向对象

### 类和对象

Python的类可以定义函数`__init__()`用于初始化

Python的属性和方法访问权限只有两种：**公开的**和**私有的**，后者命名以双下划线开头，如`__var`或`__method()`

类的私有成员仍然可以通过单下划线 + 类名的方式访问到，如`_A__var`

建议类中属性和方法都设置为公开，但是对受保护的属性或方法以单下划线开头，以此对类外的代码表明访问此属性或方法需谨慎



实例方法、类方法和静态方法：

- 实例方法，第一个参数必须要默认传实例对象，一般习惯用`self`
- 类方法，由`@classmethod`修饰，第一个参数必须要默认传类，一般习惯用`cls`
  - 使用场景：用于模拟其他一些语言中多个构造函数的情况
- 静态方法，由`@staticmethod`修饰，对参数没有要求
  - 使用场景：不需要修改类或者对象的属性的时候，静态方法放在这个类中可以让代码更加有管理性



面向对象演示代码：

```python
from abc import ABCMeta, abstractmethod
from random import randint, randrange


class Fighter(object, metaclass=ABCMeta):
    """战斗者"""

    # 通过__slots__魔法限定对象可以绑定的成员变量
    __slots__ = ('_name', '_hp')

    def __init__(self, name, hp):
        """初始化方法

        :param name: 名字
        :param hp: 生命值
        """
        self._name = name
        self._hp = hp

    @property
    def name(self):
        return self._name

    @property
    def hp(self):
        return self._hp

    @hp.setter
    def hp(self, hp):
        self._hp = hp if hp >= 0 else 0

    @property
    def alive(self):
        return self._hp > 0

    @abstractmethod
    def attack(self, other):
        """攻击

        :param other: 被攻击的对象
        """
        pass


class Ultraman(Fighter):
    """奥特曼"""

    __slots__ = ('_name', '_hp', '_mp')

    def __init__(self, name, hp, mp):
        """初始化方法

        :param name: 名字
        :param hp: 生命值
        :param mp: 魔法值
        """
        super().__init__(name, hp)
        self._mp = mp

    def attack(self, other):
        other.hp -= randint(15, 25)

    def huge_attack(self, other):
        """究极必杀技(打掉对方至少50点或四分之三的血)

        :param other: 被攻击的对象

        :return: 使用成功返回True否则返回False
        """
        if self._mp >= 50:
            self._mp -= 50
            injury = other.hp * 3 // 4
            injury = injury if injury >= 50 else 50
            other.hp -= injury
            return True
        else:
            self.attack(other)
            return False

    def magic_attack(self, others):
        """魔法攻击

        :param others: 被攻击的群体

        :return: 使用魔法成功返回True否则返回False
        """
        if self._mp >= 20:
            self._mp -= 20
            for temp in others:
                if temp.alive:
                    temp.hp -= randint(10, 15)
            return True
        else:
            return False

    def resume(self):
        """恢复魔法值"""
        incr_point = randint(1, 10)
        self._mp += incr_point
        return incr_point

    def __str__(self):
        return '~~~%s奥特曼~~~\n' % self._name + \
            '生命值: %d\n' % self._hp + \
            '魔法值: %d\n' % self._mp


class Monster(Fighter):
    """小怪兽"""

    __slots__ = ('_name', '_hp')

    def attack(self, other):
        other.hp -= randint(10, 20)

    def __str__(self):
        return '~~~%s小怪兽~~~\n' % self._name + \
            '生命值: %d\n' % self._hp


def is_any_alive(monsters):
    """判断有没有小怪兽是活着的"""
    for monster in monsters:
        if monster.alive > 0:
            return True
    return False


def select_alive_one(monsters):
    """选中一只活着的小怪兽"""
    monsters_len = len(monsters)
    while True:
        index = randrange(monsters_len)
        monster = monsters[index]
        if monster.alive > 0:
            return monster


def display_info(ultraman, monsters):
    """显示奥特曼和小怪兽的信息"""
    print(ultraman)
    for monster in monsters:
        print(monster, end='')


def main():
    u = Ultraman('迪迦', 500, 120)
    m1 = Monster('小型怪兽', 100)
    m2 = Monster('中型怪兽', 200)
    m3 = Monster('大型怪兽', 300)
    ms = [m1, m2, m3]
    fight_round = 1
    while u.alive and is_any_alive(ms):
        print('========第%02d回合========' % fight_round)
        m = select_alive_one(ms)  # 选中一只小怪兽
        skill = randint(1, 10)   # 通过随机数选择使用哪种技能
        if skill <= 6:  # 60%的概率使用普通攻击
            print('%s使用普通攻击打了%s.' % (u.name, m.name))
            u.attack(m)
            print('%s的魔法值恢复了%d点.' % (u.name, u.resume()))
        elif skill <= 9:  # 30%的概率使用魔法攻击(可能因魔法值不足而失败)
            if u.magic_attack(ms):
                print('%s使用了魔法攻击.' % u.name)
            else:
                print('%s使用魔法失败.' % u.name)
        else:  # 10%的概率使用究极必杀技(如果魔法值不足则使用普通攻击)
            if u.huge_attack(m):
                print('%s使用究极必杀技虐了%s.' % (u.name, m.name))
            else:
                print('%s使用普通攻击打了%s.' % (u.name, m.name))
                print('%s的魔法值恢复了%d点.' % (u.name, u.resume()))
        if m.alive > 0:  # 如果选中的小怪兽没有死就回击奥特曼
            print('%s回击了%s.' % (m.name, u.name))
            m.attack(u)
        display_info(u, ms)  # 每个回合结束后显示奥特曼和小怪兽的信息
        fight_round += 1
    print('\n========战斗结束!========\n')
    if u.alive > 0:
        print('%s奥特曼胜利!' % u.name)
    else:
        print('小怪兽胜利!')


if __name__ == '__main__':
    main()
```





## 3 命令行

`python` 显示Python信息并进入Python运行环境

`python --version` 或 `python -V` 打印Python版本信息并退出Python

`python -v` 以一种特殊模式进入 Python，该模式下每次有模块被初始化就会打印相应信息，展示载入的模块名或者内置模块

退出交互式编程：

- `exit()`
- `quit()`
- 快捷键Ctrl + Z





## 4 图形用户界面和游戏开发

### tkinter

一个使用tkinter模块的简单的GUI应用代码演示：

```python
import tkinter
import tkinter.messagebox


def main():
    flag = True

    # 修改标签上的文字
    def change_label_text():
        nonlocal flag
        flag = not flag
        color, msg = ('red', 'Hello, world!')\
            if flag else ('blue', 'Goodbye, world!')
        label.config(text=msg, fg=color)

    # 确认退出
    def confirm_to_quit():
        if tkinter.messagebox.askokcancel('温馨提示', '确定要退出吗?'):
            top.quit()

    # 创建顶层窗口
    top = tkinter.Tk()
    # 设置窗口大小
    top.geometry('240x160')
    # 设置窗口标题
    top.title('小游戏')
    # 创建标签对象并添加到顶层窗口
    label = tkinter.Label(top, text='Hello, world!', font='Arial -32', fg='red')
    label.pack(expand=1)
    # 创建一个装按钮的容器
    panel = tkinter.Frame(top)
    # 创建按钮对象 指定添加到哪个容器中 通过command参数绑定事件回调函数
    button1 = tkinter.Button(panel, text='修改', command=change_label_text)
    button1.pack(side='left')
    button2 = tkinter.Button(panel, text='退出', command=confirm_to_quit)
    button2.pack(side='right')
    panel.pack(side='bottom')
    # 开启主事件循环
    tkinter.mainloop()


if __name__ == '__main__':
    main()
```



### pygame

如果还没有安装pygame包，那么首先确保安装了pip（Python的包管理工具），再在控制台输入 `pip -install pygame` 即可



"大球吃小球"代码演示：

```python
from enum import Enum, unique
from math import sqrt
from random import randint

import pygame


@unique
class Color(Enum):
    """颜色"""

    RED = (255, 0, 0)
    GREEN = (0, 255, 0)
    BLUE = (0, 0, 255)
    BLACK = (0, 0, 0)
    WHITE = (255, 255, 255)
    GRAY = (242, 242, 242)

    @staticmethod
    def random_color():
        """获得随机颜色"""
        r = randint(0, 255)
        g = randint(0, 255)
        b = randint(0, 255)
        return (r, g, b)


class Ball(object):
    """球"""

    def __init__(self, x, y, radius, sx, sy, color=Color.RED):
        """初始化方法"""
        self.x = x
        self.y = y
        self.radius = radius
        self.sx = sx
        self.sy = sy
        self.color = color
        self.alive = True

    def move(self, screen):
        """移动"""
        self.x += self.sx
        self.y += self.sy
        if self.x - self.radius <= 0 or \
                self.x + self.radius >= screen.get_width():
            self.sx = -self.sx
        if self.y - self.radius <= 0 or \
                self.y + self.radius >= screen.get_height():
            self.sy = -self.sy

    def eat(self, other):
        """吃其他球"""
        if self.alive and other.alive and self != other:
            dx, dy = self.x - other.x, self.y - other.y
            distance = sqrt(dx ** 2 + dy ** 2)
            if distance < self.radius + other.radius \
                    and self.radius > other.radius:
                other.alive = False
                self.radius = self.radius + int(other.radius * 0.146)

    def draw(self, screen):
        """在窗口上绘制球"""
        pygame.draw.circle(screen, self.color,
                           (self.x, self.y), self.radius, 0)


def main():
    # 定义用来装所有球的容器
    balls = []
    # 初始化导入的pygame中的模块
    pygame.init()
    # 初始化用于显示的窗口并设置窗口尺寸
    screen = pygame.display.set_mode((800, 600))
    # 设置当前窗口的标题
    pygame.display.set_caption('大球吃小球')
    running = True
    # 开启一个事件循环处理发生的事件
    while running:
        # 从消息队列中获取事件并对事件进行处理
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                running = False
            # 处理鼠标事件的代码
            if event.type == pygame.MOUSEBUTTONDOWN and event.button == 1:
                # 获得点击鼠标的位置
                x, y = event.pos
                radius = randint(10, 100)
                sx, sy = randint(-10, 10), randint(-10, 10)
                color = Color.random_color()
                # 在点击鼠标的位置创建一个球(大小、速度和颜色随机)
                ball = Ball(x, y, radius, sx, sy, color)
                # 将球添加到列表容器中
                balls.append(ball)
        screen.fill((255, 255, 255))
        # 取出容器中的球 如果没被吃掉就绘制 被吃掉了就移除
        for ball in balls:
            if ball.alive:
                ball.draw(screen)
            else:
                balls.remove(ball)
        pygame.display.flip()
        # 每隔50毫秒就改变球的位置再刷新窗口
        pygame.time.delay(50)
        for ball in balls:
            ball.move(screen)
            # 检查球有没有吃到其他的球
            for other in balls:
                ball.eat(other)


if __name__ == '__main__':
    main()
```





## 5 文件和异常

### JSON

`json` 模块有四个比较重要的函数：

- `dump` - 将Python对象按照JSON格式序列化到文件中
- `dumps` - 将Python对象处理成JSON格式的字符串
- `load` - 将文件中的JSON数据反序列化成对象
- `loads` - 将字符串的内容反序列化成Python对象



### 正则表达式

Python中的 `re` 包提供了多种支持正则表达式操作的函数
