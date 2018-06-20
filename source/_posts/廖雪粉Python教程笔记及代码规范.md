---
title: 廖雪峰Python教程笔记及代码规范（一）
date: 2018-04-22 17:11:51
id: 0422-171151
tags: 
    - Python
    - 代码规范
    - 笔记
categories:
    - Python编程笔记
copyright: true
---

> 学习廖雪峰python教程后的笔记，以及部分课后习题代码

<!-- more -->

- [廖雪峰Python教程笔记及代码规范（一）](#python)
    - [函数](#)
    - [高级特性](#)
        - [切片](#)
        - [迭代](#)
        - [列表生成器](#)
        - [生成器（generator）](#generator)
        - [迭代器](#)
    - [函数式编程](#)
        - [高级函数](#)
            - [map/reduce](#map-reduce)
            - [filter](#filter)
            - [sorted()](#sorted)
        - [返回函数](#)
        - [Decorator](#decorator)
        - [偏函数](#)
    - [模块](#)
        - [创建模块](#)
    - [注释规范](#)
        - [注释](#)
        - [文档字符串](#)
        - [模块注释](#)
        - [函数和方法](#)
        - [类的注释](#)
        - [块注释和行注释](#)
    - [类](#)
        - [类和实例](#)
        - [访问限制](#)
        - [继承和多态](#)
        - [获取对象的信息](#)
        - [实例属性和类属性](#)
    - [面向对象高级编程](#)
        - [__slot__()属性](#slot)
        - [使用类装饰器@property](#property)
        - [多重继承](#)
        - [定制类](#)
        - [枚举类](#)
    - [错误，调试和测试](#)
        - [错误控制](#)
        - [记录错误](#)
        - [抛出错误](#)
        - [调试](#)
        - [单元测试](#)

# 廖雪峰Python教程笔记及代码规范（一）

## 函数

- 如果有必要，可以先对参数的数据类型做检查；

- 函数体内部可以用return随时返回函数结果；

- 函数可以同时返回多个值，但其实就是一个tuple

- 默认参数一定要用不可变对象，如果是可变对象，程序运行时会有逻辑错误！

- `*args`是可变参数，args接收的是一个tuple；

- `**kw`是关键字参数，kw接收的是一个dict。

- 调用函数时如何传入可变参数和关键字参数的语法：

  - 可变参数既可以直接传入：`func(1, 2, 3)`，又可以先组装list或tuple，再通过`*args`传入：`func(*(1, 2, 3))`
  - 关键字参数既可以直接传入：`func(a=1, b=2)`，又可以先组装dict，再通过`**kw`传入：`func(**{'a': 1, 'b': 2})`

- 使用`*args`和`**kw`是Python的习惯写法，当然也可以用其他参数名，但最好使用习惯用法。

- 命名的关键字参数是为了限制调用者可以传入的参数名，同时可以提供默认值。

- 定义命名的关键字参数在没有可变参数的情况下不要忘了写分隔符*，否则定义的将是位置参数。

## 高级特性

### 切片

- 注意s[n]与s[n:m]的区别，一个是元素，一个是切片。
- 技巧
  - 逆序

  ```python
  s[::-1]
  ```

  - 间隔n

  ```python
  s[::n]
  ```

  - 前10个数间隔n
  - 后10个数间隔n

    ```python
    s[:10:n]
    s[-10::n]
    ```

- 当切片越界时，会返回一个`''`

```python
if s == '':
    # 取首位
    s[0] # error: out of index
    s[:1] # correct
    # 取末位
    s[-1] # error: out of index
    s[-1:] # correct
```

### 迭代

- 默认情况下，dict迭代的是key。如果要迭代value，可以用`for value in d.values()`，如果要同时迭代key和value，可以用`for k, v in d.items()`。

### 列表生成器

把一个list（包含各种数据类型）中所有的字符串变成小写：

```python
# -*- coding: utf-8 -*-
L1 = ['Hello', 'World', 18, 'Apple', None]
L2 = [s.lower() for s in L1 if isinstance(s,str)]
```

### 生成器（generator）

要创建一个generator，有很多种方法。

- 第一种方法很简单，只要把一个列表生成式的[]改成()，就创建了一个generator
  - 调用方法1: `next()` # 很少用
  - 调用方法2: `for i in generator:`

- 另一种方法。如果一个函数定义中包含`yield`关键字，那么这个函数就不再是一个普通函数，而是一个generator
  - 但是用for循环调用generator时，发现拿不到generator的return语句的返回值。如果想要拿到返回值，必须捕获StopIteration错误，返回值包含在StopIteration的valuez中

### 迭代器

- 凡是可作用于for循环的对象都是Iterable类型；

- 凡是可作用于next()函数的对象都是Iterator类型，它们表示一个惰性计算的序列；

- 集合数据类型如list、dict、str等是Iterable但不是Iterator，不过可以通过iter()函数获得一个Iterator对象。

- Python的for循环本质上就是通过不断调用next()函数实现的

## 函数式编程

### 高级函数

#### map/reduce

- `map()`函数接收两个参数，第一个参数是`f`，即函数对象本身，一个是`Iterable`，map将传入的函数依次作用到序列的每个元素，并把结果作为新的`Iterator`返回。
- `reduce`把一个函数作用在一个序列`[x1, x2, x3, ...]`上，这个函数必须接收两个参数，reduce把结果继续和序列的下一个元素做累积计算，其效果就是：

```python
reduce(f, [x1, x2, x3, x4]) = f(f(f(x1, x2), x3), x4)
```

Eg:

```python
# string to int
from functools import reduce
DIGITS = {'0': 0, '1': 1, '2': 2, '3': 3, '4': 4, '5': 5, '6': 6, '7': 7, '8': 8, '9': 9}

def str2int(s):

    def char2num(s):
        return(DIGITS(s))

    return reduce(lambda x, y : x * 10 + y, map(char2num,s))
```

```python
# string to float
list(map(lambda s : int(s.split('.')[0]) + int(s.split('.')[1]) * pow(10, -len(s.split('.')[1])), ['123.456', '234.567']))
```

#### filter

Python内建的`filter()`函数用于过滤序列。

- 和`map()`类似，`filter()`也接收一个函数和一个序列。和`map()`不同的是，`filter()`把传入的函数依次作用于每个元素，然后根据返回值是`True`还是`False`决定保留还是丢弃该元素
- 返回值也是一个**Iterator**

```python
# 用filter() 求素数
def f():
    i = 2
    while True:
        yield i
        i += 1

def s(n):
    return lambda x : x % n != 0

def sushu():
    fun = f()
    while True:
        n = next(fun)
        fun = filter(s(n), fun)
        yield n
for n in sushu():
    if n < 1000:
        print(n)
    else: break
```

#### sorted()

语法：
`sort(list,key,reverse)`

### 返回函数

- 返回闭包时牢记一点：返回函数不要引用任何循环变量，或者后续会发生变化的变量。

- 如果一定要引用循环变量怎么办？方法是再创建一个函数，用该函数的参数绑定循环变量当前的值，无论该循环变量后续如何更改，已绑定到函数参数的值不变：

```python
def count():
    def f(j):
        def g():
            return j*j
        return g
    fs = []
    for i in range(1, 4):
        fs.append(f(i)) # f(i)立刻被执行，因此i的当前值被传入f()
    return fs
```

### Decorator

由于函数也是一个对象，而且函数对象可以被赋值给变量，所以，通过变量也能调用该函数。

函数对象有一个__name__属性，可以拿到函数的名字：

现在，假设我们要增强now()函数的功能，比如，在函数调用前后自动打印日志，但又不希望修改now()函数的定义，这种在代码运行期间动态增加功能的方式，称之为“装饰器”（Decorator）。

本质上，decorator就是一个返回函数的高阶函数。所以，我们要定义一个能打印日志的decorator，可以定义如下：

```python
def log(func):
    def wrapper(*args, **kw):
        print('call %s():' % func.__name__)
        return func(*args, **kw)
    return wrapper
```

观察上面的log，因为它是一个decorator，所以接受一个函数作为参数，并返回一个函数。我们要借助Python的@语法，把decorator置于函数的定义处：

```python
@log
def now():
    print('2015-3-25')
```

调用now()函数，不仅会运行now()函数本身，还会在运行now()函数前打印一行日志：

```python
>>> now()
call now():
2015-3-25
```

把@log放到now()函数的定义处，相当于执行了语句：`now = log(now)`

由于log()是一个decorator，返回一个函数，所以，原来的now()函数仍然存在，只是现在同名的now变量指向了新的函数，于是调用now()将执行新函数，即在log()函数中返回的wrapper()函数。

wrapper()函数的参数定义是(*args, **kw)，因此，wrapper()函数可以接受任意参数的调用。在wrapper()函数内，首先打印日志，再紧接着调用原始函数。

如果decorator本身需要传入参数，那就需要编写一个返回decorator的高阶函数，写出来会更复杂。比如，要自定义log的文本：

```python
def log(text):
    def decorator(func):
        def wrapper(*args, **kw):
            print('%s %s():' % (text, func.__name__))
            return func(*args, **kw)
        return wrapper
    return decorator
```

这个3层嵌套的decorator用法如下：

```python
@log('execute')
def now():
    print('2015-3-25')
```

执行结果如下：

```shell
>>> now()
execute now():
2015-3-25
```

和两层嵌套的decorator相比，3层嵌套的效果是这样的：`>>> now = log('execute')(now)`

我们来剖析上面的语句，首先执行log('execute')，返回的是decorator函数，再调用返回的函数，参数是now函数，返回值最终是wrapper函数。

以上两种decorator的定义都没有问题，但还差最后一步。因为我们讲了函数也是对象，它有__name__等属性，但你去看经过decorator装饰之后的函数，它们的__name__已经从原来的'now'变成了'wrapper'：

```shell
>>> now.__name__
'wrapper'
```

因为返回的那个wrapper()函数名字就是'wrapper'，所以，需要把原始函数的__name__等属性复制到wrapper()函数中，否则，有些依赖函数签名的代码执行就会出错。

不需要编写`wrapper.__name__ = func.__name__`这样的代码，Python内置的`functools.wraps`就是干这个事的，所以，一个完整的decorator的写法如下：

```python
import functools

def log(func):
    @functools.wraps(func)
    def wrapper(*args, **kw):
        print('call %s():' % func.__name__)
        return func(*args, **kw)
    return wrapper
```

或者针对带参数的decorator：

```python
import functools

def log(text):
    def decorator(func):
        @functools.wraps(func)
        def wrapper(*args, **kw):
            print('%s %s():' % (text, func.__name__))
            return func(*args, **kw)
        return wrapper
    return decorator
```

### 偏函数

`functools.partial`的作用就是，把一个函数的某些参数给固定住（也就是设置默认值），返回一个新的函数，调用这个新函数会更简单

```python
# 自己实现一个partial(),用装饰器的思路
import functools
def partial(func, *arg1, **kw1):
    @functools.wraps(func)
    def wrapper(*arg,**kw):
        return func(*(arg1+arg),**dict(kw1,**kw))
    return wrapper

def func(x,y):
    print (x,y)

fn2 = partial(func,y=2)
fn2(1)
```

## 模块

### 创建模块

```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-

' a test module '

__author__ = 'Michael Liao'

import sys

def test():
    args = sys.argv
    if len(args)==1:
        print('Hello, world!')
    elif len(args)==2:
        print('Hello, %s!' % args[1])
    else:
        print('Too many arguments!')

if __name__=='__main__':
    test()
```

- 第1行和第2行是标准注释，第1行注释可以让这个hello.py文件直接在Unix/Linux/Mac上运行，第2行注释表示.py文件本身使用标准UTF-8编码；

- 第4行是一个字符串，表示模块的文档注释，任何模块代码的第一个字符串都被视为模块的文档注释；

- 第6行使用__author__变量把作者写进去，这样当你公开源代码后别人就可以瞻仰你的大名；

- sys模块有一个argv变量，用list存储了命令行的所有参数。argv至少有一个元素，因为第一个参数永远是该.py文件的名称
- 当我们在命令行运行hello模块文件时，Python解释器把一个特殊变量__name__置为__main__，而如果在其他地方导入该hello模块时，if判断将失败，因此，这种if测试可以让一个模块通过命令行运行时执行一些额外的代码，最常见的就是运行测试
- 类似_xxx和__xxx这样的函数或变量就是非公开的（private），不应该被直接引用，比如_abc，__abc等

## 注释规范

### 注释

确保对模块, 函数, 方法和行内注释使用正确的风格

### 文档字符串

- Python有一种独一无二的的注释方式: 使用文档字符串. 文档字符串是包, 模块, 类或函数里的第一个语句. 这些字符串可以通过对象的__doc__成员被自动提取, 并且被pydoc所用. (你可以在你的模块上运行pydoc试一把, 看看它长什么样). 我们对文档字符串的惯例是使用三重双引号"""( PEP-257 ).

- 一个文档字符串应该这样组织: 首先是一行以句号, 问号或惊叹号结尾的概述(或者该文档字符串单纯只有一行). 接着是一个空行. 接着是文档字符串剩下的部分, 它应该与文档字符串的第一行的第一个引号对齐. 下面有更多文档字符串的格式化规范.

### 模块注释

每个文件应该包含一个许可样板. 根据项目使用的许可(例如, Apache 2.0, BSD, LGPL, GPL), 选择合适的样板.

### 函数和方法

下文所指的函数,包括函数, 方法, 以及生成器.

一个函数必须要有文档字符串, 除非它满足以下条件:

外部不可见

非常短小

简单明了

文档字符串应该包含函数做什么, 以及输入和输出的详细描述. 通常, 不应该描述"怎么做", 除非是一些复杂的算法. 文档字符串应该提供足够的信息, 当别人编写代码调用该函数时, 他不需要看一行代码, 只要看文档字符串就可以了. 对于复杂的代码, 在代码旁边加注释会比使用文档字符串更有意义.

关于函数的几个方面应该在特定的小节中进行描述记录， 这几个方面如下文所述. 每节应该以一个标题行开始. 标题行以冒号结尾. 除标题行外, 节的其他内容应被缩进2个空格.

- Args:

  列出每个参数的名字, 并在名字后使用一个冒号和一个空格, 分隔对该参数的描述.如果描述太长超过了单行80字符,使用2或者4个空格的悬挂缩进(与文件其他部分保持一致). 描述应该包括所需的类型和含义. 如果一个函数接受\*foo(可变长度参数列表)或者\*\*bar (任意关键字参数), 应该详细列出\*foo和\*\*bar.

- Returns: (或者 Yields: 用于生成器)

  描述返回值的类型和语义. 如果函数返回None, 这一部分可以省略.

- Raises:

  列出与接口有关的所有异常.

```python
def fetch_bigtable_rows(big_table, keys, other_silly_variable=None):
    """Fetches rows from a Bigtable.

    Retrieves rows pertaining to the given keys from the Table instance
    represented by big_table.  Silly things may happen if
    other_silly_variable is not None.

    Args:
        big_table: An open Bigtable Table instance.
        keys: A sequence of strings representing the key of each table row
            to fetch.
        other_silly_variable: Another optional variable, that has a much
            longer name than the other args, and which does nothing.

    Returns:
        A dict mapping keys to the corresponding table row data
        fetched. Each row is represented as a tuple of strings. For
        example:

        {'Serak': ('Rigel VII', 'Preparer'),
         'Zim': ('Irk', 'Invader'),
         'Lrrr': ('Omicron Persei 8', 'Emperor')}

        If a key from the keys argument is missing from the dictionary,
        then that row was not found in the table.

    Raises:
        IOError: An error occurred accessing the bigtable.Table object.
    """
    pass
```

### 类的注释

类应该在其定义下有一个用于描述该类的文档字符串. 如果你的类有公共属性(Attributes), 那么文档中应该有一个属性(Attributes)段. 并且应该遵守和函数参数相同的格式.

```python
class SampleClass(object):
    """Summary of class here.

    Longer class information....
    Longer class information....

    Attributes:
        likes_spam: A boolean indicating if we like SPAM or not.
        eggs: An integer count of the eggs we have laid.
    """

    def __init__(self, likes_spam=False):
        """Inits SampleClass with blah."""
        self.likes_spam = likes_spam
        self.eggs = 0

    def public_method(self):
        """Performs operation blah."""
```

### 块注释和行注释

最需要写注释的是代码中那些技巧性的部分. 如果你在下次 代码审查 的时候必须解释一下, 那么你应该现在就给它写注释. 对于复杂的操作, 应该在其操作开始前写上若干行注释. 对于不是一目了然的代码, 应在其行尾添加注释.

```python
# We use a weighted dictionary search to find out where i is in
# the array.  We extrapolate position based on the largest num
# in the array and the array size and then do binary search to
# get the exact number.

if i & (i-1) == 0:        # true iff i is a power of 2
```

为了提高可读性, 注释应该至少离开代码2个空格.

另一方面, 绝不要描述代码. 假设阅读代码的人比你更懂Python, 他只是不知道你的代码要做什么.

```python
# BAD COMMENT: Now go through the b array and make sure whenever i occurs
# the next element is i+1
```

## 类

### 类和实例

由于类可以起到模板的作用，因此，可以在创建实例的时候，把一些我们认为必须绑定的属性强制填写进去。通过定义一个特殊的`__init__`方法，在创建实例的时候，就把name，score等属性绑上去。

注意到`__init__`方法的第一个参数永远是`self`，表示创建的实例本身，因此，在`__init__`方法内部，就可以把各种属性绑定到`self`，因为`self`就指向创建的实例本身。

和静态语言不同，Python允许对实例变量绑定任何数据，也就是说，对于两个实例变量，虽然它们都是同一个类的不同实例，但拥有的变量名称都可能不同。

```python
class Student(object):
    def __init__(self, name, score, *args, **kwargs):
        self.name = name
        self.score = score
    pass
leoch = Student('leoch', 100)
xiaotong = Student('xiaotong', 99)
leoch.gender = 'MALE'

print(leoch.gender) # MALE
print(xiaotong.gender) # Error
```

### 访问限制

- `__name`：私有变量，外部无法访问（其实是编译器把其解析成另外的变量名）。如果在外部强行赋值其实是新声明了一个变量（不建议）。
- `_name`: 约定俗成的保护变量，可以访问但是不建议，`import * from `时不会包含。
- 如果要访问私有变量，可以添加函数
    ```python
    def get_name(self):
        return self.__name
    ```
- 如果要修改私有变量，可以添加函数
    ```python
    def set_name(self, name):
        self.__name = name
    ```
- Python之父Guido推荐的

| Type                       | Public             | Internal                                                          |
| -------------------------- | ------------------ | ----------------------------------------------------------------- |
| Modules                    | lower_with_under   | _lower_with_under                                                 |
| Packages                   | lower_with_under   |
| Classes                    | CapWords           | _CapWords                                                         |
| Exceptions                 | CapWords           |
| Functions                  | lower_with_under() | _lower_with_under()                                               |
| Global/Class Constants     | CAPS_WITH_UNDER    | _CAPS_WITH_UNDER                                                  |
| Global/Class Variables     | lower_with_under   | _lower_with_under                                                 |
| Instance Variables         | lower_with_under   | _lower_with_under (protected) or __lower_with_under (private)     |
| Method Names               | lower_with_under() | _lower_with_under() (protected) or __lower_with_under() (private) |
| Function/Method Parameters | lower_with_under   |
| Local Variables            | lower_with_under   |

- 如果一个类不继承自其它类, 就显式的从object继承. 嵌套类也一样。
- 练习
    ```python
    class Student(object):
    def __init__(self, __name, __gender, *args, **kwargs):
        self.__name = __name
        self.__gender = __gender
        pass
    def get_gender(self):
        print (self.__gender)
    def set_gender(self,gender):
        if gender in ['Male', 'male', 'M', 'F', 'Female', 'female']:
            self.__gender = gender
        else:
            print("Error")
    ```

### 继承和多态

- 继承可以把父类的所有功能都直接拿过来，这样就不必重零做起，子类只需要新增自己特有的方法，也可以把父类不适合的方法覆盖重写。

- 对于静态语言（例如Java）来说，如果需要传入Animal类型，则传入的对象必须是Animal类型或者它的子类，否则，将无法调用run()方法。对于Python这样的动态语言来说，则不一定需要传入Animal类型。我们只需要保证传入的对象有一个run()方法就可以了。这就是动态语言的“鸭子类型”，它并不要求严格的继承体系，一个对象只要“看起来像鸭子，走起路来像鸭子”，那它就可以被看做是鸭子。

### 获取对象的信息

- 使用type()

    ```python
    >>>type(123)
    <class 'int'>
    ```

- isinstance()

    ```python
    isinstance(变量，类型)
    ```

- dir()

    ```python
    >>>dir('ABC')
    ['__add__', '__class__',..., '__subclasshook__', 'capitalize', 'casefold',..., 'zfill']
    #列出一个类型的所有属性
    ```

- getattr()、setattr()、hasattr()

    判断类型中是否有某个参数，获取和设置

### 实例属性和类属性

- 实例属性属于各个实例所有，互不干扰；

- 类属性属于类所有，所有实例共享一个属性；

- 不要对实例属性和类属性使用相同的名字，否则将产生难以发现的错误。

- 练习

```python
# 为了统计学生人数，可以给Student类增加一个类属性，每创建一个实例，该属性自动增加
# -*- coding: utf-8 -*-
class Student(object):
    count = 0

    def __init__(self, name):
        self.name = name
        '''
        notes:
        # init方法是绑定实例的属性用的，不能调用类属性count
        # 每次实例化对象的时候，只需init来为其绑定属性，不会执行类属性部分，即从类属性之后的部分开始运行的
        # 写"self.count = self.count + 1"实质上是绑定了一个新的实例属性count,已经与类中原先声明的变量“count”不是同一个了.等号右边的self.count指向的是Student.count,左边的指向新的实例属性self.count,无论创建多少个实例，实例属性count都为1
        '''
        #self.count += 1
        Student.count += 1
        #count += 1

# 测试:
if Student.count != 0:
    print('测试失败!')
else:
    bart = Student('Bart')
    if Student.count != 1:
        print('测试失败!')
    else:
        lisa = Student('Bart')
        if Student.count != 2:
            print('测试失败!')
        else:
            print('Students:', Student.count)
            print('测试通过!')
```

## 面向对象高级编程

### __slot__()属性

```python
>>> class test():
...     __slots__ = ('a','b')
...     c = 5
...     def __init__(self):
...             pass
...
>>> aa = test()
>>> aa.a = 3
>>> aa.b = 4
>>> test.a = 5
 aa.a  #此时该值变成5，因为aa.a的实例属性已被删除，且'a'从slots运行名单中删除
 aa.a =3  #此时会报错，'a'已被限制，此时只能访问b
 del test.a  #删除 a 类属性
 aa.a=4  #依然报错，虽然slots值包含'a'，但允许名单中已不存在'a'
 test.b=7
 aa.b = 4  #报错，同理，此时slots的允许名单中的2个名称都被禁用了，所以test对象不能创建任何实例属性了
 test.slots=('q','p')
 bb=test()
 bb.q=3  #报错，更改slots是不会改变允许名单的
 bb.a=3  #报错，test对象已不能创建任何实例属性了
```

### 使用类装饰器@property

- 在绑定属性时，如果我们直接把属性暴露出去，虽然写起来很简单，但是，没办法检查参数，导致可以把成绩随便改

- 可以通过一个set_score()方法来设置成绩，再通过一个get_score()来获取成绩，这样，在set_score()方法里，就可以检查参数, 但是，上面的调用方法又略显复杂，没有直接用属性这么直接简单

- 把一个getter方法变成属性，只需要加上@property就可以了，此时，@property本身又创建了另一个装饰器@score.setter，负责把一个setter方法变成属性赋值，于是，我们就拥有一个可控的属性操作

- 还可以定义只读属性，只定义getter方法，不定义setter方法就是一个只读属性

```python
# 请利用@property给一个Screen对象加上width和height属性，以及一个只读属性resolution：
# -*- coding: utf-8 -*-
class Screen(object):
    @property
    def width(self):
        return self._width
    @width.setter
    def width(self,value):
        self._width = value
    @property
    def height(self):
        return self._height
    @height.setter
    def height(self,value):
        self._height = value

    @property
    def resolution(self):
        return self._width * self._height   
    pass
# 测试:
s = Screen()
s.width = 1024
s.height = 768
print('resolution =', s.resolution)
if s.resolution == 786432:
    print('测试通过!')
else:
    print('测试失败!')
```

### 多重继承

- 通过多重继承(MixIn)，一个子类就可以同时获得多个父类的所有功能

    ```python
    class Bat(Mammal, Flyable):
        pass
    ```

- MixIn的目的就是给一个类增加多个功能，这样，在设计类的时候，我们优先考虑通过多重继承来组合多个MixIn的功能，而不是设计多层次的复杂的继承关系

- 什么是拓扑排序：

  - 从DAG途中选择一个没有前驱(即入度为0)的顶点并输出
  - 从图中删除该顶点和所有以它为起点的有向边。
  - 重复1和2直到当前DAG图为空或当前途中不存在无前驱的顶点为止。后一种情况说明有向图中必然存在环。

- python多重继承：

  - 把继承关系先构成一张图
  - 利用拓扑排序的方法输出拓扑顺序，并列关系时遵循取最左原则
  - python继承顺序遵循C3算法，只要在一个地方找到了所需的内容，就不再继续查找

### 定制类

- __str__：类自定义打印
- __repr__：实例自定义打印
- __iter__：如果一个类想被用于for ... in循环，类似list或tuple那样，就必须实现一个__iter__()方法，该方法返回一个迭代对象，然后，Python的for循环就会不断调用该迭代对象的__next__()方法拿到循环的下一个值，直到遇到StopIteration错误时退出循环。
- __getitem__：要表现得像list那样按照下标取出元素，需要实现__getitem__()方法
- __getattr__：动态返回一个属性，当调用不存在的属性时，比如score，Python解释器会试图调用__getattr__(self, 'score')来尝试获得属性，这样，我们就有机会返回score的值。
  - 返回函数也是完全可以的，只是调用方式要变为s.age()
  - 注意，只有在没有找到属性的情况下，才调用__getattr__，已有的属性，比如name，不会在__getattr__中查找
  - 默认返回None
- __call__：任何类，只需要定义一个__call__()方法，就可以直接对实例进行调用

```python
# 练习
class Chain(object):

    def __init__(self, path = ''):
        print('__init__' + path)
        self._path = path
        print(self._path)

    def __getattr__(self, path):
        print('__getattr__' + path)
        return Chain('%s/%s' % (self._path,path))
        print(self._path)

    def __call__(self, path):
        print('__call__' + path)
        return Chain('%s/%s' % (self._path,path))
        print(self._path)

    def __str__(self):
        return "my path is" + self._path

    __repr__ = __str__

path = Chain().a.b.user('leoch').file('image')
print(path)
```

### 枚举类

枚举类型定义一个class类型，然后，每个常量都是class的一个唯一实例。Python提供了`Enum`类来实现这个功能：

```python
from enum import Enum

Month = Enum('Month', ('Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'))
```

这样我们就获得了`Month`类型的枚举类，可以直接使用`Month.Jan`来引用一个常量，或者枚举它的所有成员：

```python
for name, member in Month.__members__.items():
    print(name, '=>', member, ',', member.value)
```

value属性则是自动赋给成员的`int`常量，默认从1开始计数。

如果需要更精确地控制枚举类型，可以从`Enum`派生出自定义类：

```python
from enum import Enum, unique

@unique
class Weekday(Enum):
    Sun = 0 # Sun的value被设定为0
    Mon = 1
    Tue = 2
    Wed = 3
    Thu = 4
    Fri = 5
    Sat = 6
```

`@unique`装饰器可以帮助我们检查保证没有重复值。

## 错误，调试和测试

### 错误控制

```python
try:
    print('try...')
    r = 10 / 0
    print('result:', r)
except ZeroDivisionError as e1:
    print('except:', e1)
except ValueError as e2:
    print('except:', e2)
else:
    print('no error!')
finally:
    print('finally...')
print('END')
```

### 记录错误

Python内置的`logging`模块可以非常容易地记录错误信息：

```python
import logging
try:

except error  as e1:
    print('except:', e1)
    logging.exception(e1)
```

### 抛出错误

如果要抛出错误，首先根据需要，可以定义一个错误的class，选择好继承关系，然后，用`raise`语句抛出一个错误的实例：

```python
# err_reraise.py

class FooError(ValueError):
    pass

def foo(s):
    n = int(s)
    if n==0:
        raise ValueError('invalid value: %s' % s)
    return 10 / n

def bar():
    try:
        foo('0')
    except ValueError as e:
        print('ValueError!')
        raise

bar()
```

### 调试

主要有`print`, `assert`, `logging`, IDE 断点几种方法，主要记录logging

把`print()`替换为`logging`是第3种方式，和`assert`比，`logging`不会抛出错误，而且可以输出到文件：

```python
import logging

s = '0'
n = int(s)
logging.info('n = %d' % n)
print(10 / n)
```

`logging.info()`就可以输出一段文本。运行，发现除了`ZeroDivisionError`，没有任何信息。怎么回事？

别急，在`import logging`之后添加一行配置再试试：

```python
import logging
logging.basicConfig(level=logging.INFO)
```

看到输出了：

```bash
$ python err.py
INFO:root:n = 0
Traceback (most recent call last):
  File "err.py", line 8, in <module>
    print(10 / n)
ZeroDivisionError: division by zero
```

这就是`logging`的好处，它允许你指定记录信息的级别，有`debug，info，warning，error`等几个级别，当我们指定`level=INFO`时，`logging.debug`就不起作用了。同理，指定`level=WARNING`后，`debug`和`info`就不起作用了。这样一来，你可以放心地输出不同级别的信息，也不用删除，最后统一控制输出哪个级别的信息。

`logging`的另一个好处是通过简单的配置，一条语句可以同时输出到不同的地方，比如console和文件。

### 单元测试

编写单元测试时，我们需要编写一个测试类，从`unittest.TestCase`继承。

以`test`开头的方法就是测试方法，不以`test`开头的方法不被认为是测试方法，测试的时候不会被执行。

对每一类测试都需要编写一个`test_xxx()`方法。由于`unittest.TestCase`提供了很多内置的条件判断，我们只需要调用这些方法就可以断言输出是否是我们所期望的。最常用的断言就是`assertEqual()：`和`assertRaises(AttributeError)`

## I/O编程

由于CPU和内存的速度远远高于外设的速度，所以，在IO编程中，就存在速度严重不匹配的问题。举个例子来说，比如要把100M的数据写入磁盘，CPU输出100M的数据只需要0.01秒，可是磁盘要接收这100M数据可能需要10秒，怎么办呢？有两种办法：

第一种是CPU等着，也就是程序暂停执行后续代码，等100M的数据在10秒后写入磁盘，再接着往下执行，这种模式称为**同步IO**；

另一种方法是CPU不等待，只是告诉磁盘，“您老慢慢写，不着急，我接着干别的事去了”，于是，后续代码可以立刻接着执行，这种模式称为**异步IO**。

很明显，使用异步IO来编写程序性能会远远高于同步IO，但是异步IO的缺点是编程模型复杂，通知你的方法也各不相同。如果是服务员跑过来找到你，这是回调模式，如果服务员发短信通知你，你就得不停地检查手机，这是轮询模式。总之，异步IO的复杂度远远高于同步IO。

### 文件读写

二进制文件
前面讲的默认都是读取文本文件，并且是UTF-8编码的文本文件。要读取二进制文件，比如图片、视频等等，用'rb'模式打开文件即可：

```shell
>>> f = open('/Users/michael/test.jpg', 'rb')
>>> f.read()
b'\xff\xd8\xff\xe1\x00\x18Exif\x00\x00...' # 十六进制表示的字节
字符编码
```

要读取非UTF-8编码的文本文件，需要给`open()`函数传入`encoding`参数，例如，读取GBK编码的文件：

```shell
>>> f = open('/Users/michael/gbk.txt', 'r', encoding='gbk')
>>> f.read()
'测试'
```

遇到有些编码不规范的文件，你可能会遇到`UnicodeDecodeError`，因为在文本文件中可能夹杂了一些非法编码的字符。遇到这种情况，`open()`函数还接收一个`errors`参数，表示如果遇到编码错误后如何处理。最简单的方式是直接忽略：

```shell
>>> f = open('/Users/michael/gbk.txt', 'r', encoding='gbk', errors='ignore')
```

### 操作文件和目录

操作文件和目录
操作文件和目录的函数一部分放在os模块中，一部分放在os.path模块中，这一点要注意一下。查看、创建和删除目录可以这么调用：

```python
# 查看当前目录的绝对路径:
>>> os.path.abspath('.')
'/Users/michael'
# 在某个目录下创建一个新目录，首先把新目录的完整路径表示出来:
>>> os.path.join('/Users/michael', 'testdir')
'/Users/michael/testdir'
# 然后创建一个目录:
>>> os.mkdir('/Users/michael/testdir')
# 删掉一个目录:
>>> os.rmdir('/Users/michael/testdir')
```

把两个路径合成一个时，不要直接拼字符串，而要通过`os.path.join()`函数，这样可以正确处理不同操作系统的路径分隔符。在Linux/Unix/Mac下，`os.path.join()`返回这样的字符串：
`part-1/part-2`
而Windows下会返回这样的字符串：`part-1\part-2`
同样的道理，要拆分路径时，也不要直接去拆字符串，而要通过`os.path.split()`函数，这样可以把一个路径拆分为两部分，后一部分总是最后级别的目录或文件名：

```python
>>> os.path.split('/Users/michael/testdir/file.txt')
('/Users/michael/testdir', 'file.txt')
os.path.splitext()可以直接让你得到文件扩展名，很多时候非常方便：

>>> os.path.splitext('/path/to/file.txt')
('/path/to/file', '.txt')
```

这些合并、拆分路径的函数并不要求目录和文件要真实存在，它们只对字符串进行操作。

文件操作使用下面的函数。假定当前目录下有一个test.txt文件：

```python
# 对文件重命名:
>>> os.rename('test.txt', 'test.py')
# 删掉文件:
>>> os.remove('test.py')
```

但是**复制文件的函数居然在os模块中不存在**！原因是复制文件并非由操作系统提供的系统调用。理论上讲，我们通过上一节的读写文件可以完成文件复制，只不过要多写很多代码。

幸运的是`shutil`模块提供了`copyfile()`的函数，你还可以在`shutil`模块中找到很多实用函数，它们可以看做是os模块的补充。

最后看看如何利用Python的特性来过滤文件。比如我们要列出当前目录下的所有目录，只需要一行代码：

```python
>>> [x for x in os.listdir('.') if os.path.isdir(x)]
['.lein', '.local', '.m2', '.npm', '.ssh', '.Trash', '.vim', 'Applications', 'Desktop', ...]
要列出所有的.py文件，也只需一行代码：

>>> [x for x in os.listdir('.') if os.path.isfile(x) and os.path.splitext(x)[1]=='.py']
['apis.py', 'config.py', 'models.py', 'pymonitor.py', 'test_db.py', 'urls.py', 'wsgiapp.py']
```

## 进程和线程

### 多进程

Unix/Linux操作系统提供了一个`fork()`系统调用，它非常特殊。普通的函数调用，调用一次，返回一次，但是`fork()`调用一次，返回两次，因为操作系统自动把当前进程（称为父进程）复制了一份（称为子进程），然后，分别在父进程和子进程内返回。

子进程永远返回0，而父进程返回子进程的ID。这样做的理由是，一个父进程可以fork出很多子进程，所以，父进程要记下每个子进程的ID，而子进程只需要调用`getppid()`就可以拿到父进程的ID。

Python的os模块封装了常见的系统调用，其中就包括fork，可以在Python程序中轻松创建子进程：

```python
import os

print('Process (%s) start...' % os.getpid())
# Only works on Unix/Linux/Mac:
pid = os.fork()
if pid == 0:
    print('I am child process (%s) and my parent is %s.' % (os.getpid(), os.getppid()))
else:
    print('I (%s) just created a child process (%s).' % (os.getpid(), pid))
```
> 注意：windows系统使用multiprocessing.Process

### 多线程

[略](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/00143192823818768cd506abbc94eb5916192364506fa5d000)

### ThreadLocal

[略](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/001431928972981094a382e5584413fa040b46d46cce48e000)

### 进程 vs 线程

[略](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/0014319292979766bd3285c9d6b4942a8ea9b4e9cfb48d8000)

### 分布式进程

`master.py`:

```python
import random, time
from multiprocessing import Queue
from multiprocessing.managers import BaseManager

class SpiderManager(BaseManager):
    pass

task_sender = Queue()
task_reciever = Queue()

SpiderManager.register('task_sender',callable=lambda: task_sender)
SpiderManager.register('task_reciever',callable=lambda: task_reciever)

manager = SpiderManager(address=('', 10003), authkey=b'abc')
manager.start()

task = manager.task_sender()
result = manager.task_reciever()

for i in range(10):
    n = random.randint(0, 10000)
    print('Put task %d...' % n)
    task.put(n)
# 从result队列读取结果:
print('Try get results...')
for i in range(10):
    r = result.get(timeout=100)
    print('Result: %s' % r)
# 关闭:
manager.shutdown()
print('master exit.')
```

`worker.py`:

```python
# task_worker.py

import time, sys, queue
from multiprocessing.managers import BaseManager

# 创建类似的QueueManager:
class QueueManager(BaseManager):
    pass

# 由于这个QueueManager只从网络上获取Queue，所以注册时只提供名字:
QueueManager.register('task_sender')
QueueManager.register('task_reciever')

# 连接到服务器，也就是运行task_master.py的机器:
server_addr = '127.0.0.1'
print('Connect to server %s...' % server_addr)
# 端口和验证码注意保持与task_master.py设置的完全一致:
m = QueueManager(address=(server_addr, 10003), authkey=b'abc')
# 从网络连接:
m.connect()
# 获取Queue的对象:
task = m.task_sender()
result = m.task_reciever()
# 从task队列取任务,并把结果写入result队列:
for i in range(10):
    try:
        n = task.get(timeout=1)
        print('run task %d * %d...' % (n, n))
        r = '%d * %d = %d' % (n, n, n*n)
        time.sleep(1)
        result.put(r)
    except Queue.Empty:
        print('task queue is empty.')
# 处理结束:
print('worker exit.')
```