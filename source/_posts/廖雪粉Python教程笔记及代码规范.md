---
title: 廖雪粉Python教程笔记及代码规范
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

# 廖雪峰python教程笔记

## 1 函数

- 如果有必要，可以先对参数的数据类型做检查；

- 函数体内部可以用return随时返回函数结果；
<!--more-->

- 函数可以同时返回多个值，但其实就是一个tuple

- 默认参数一定要用不可变对象，如果是可变对象，程序运行时会有逻辑错误！

- `*args`是可变参数，args接收的是一个tuple；

- `**kw`是关键字参数，kw接收的是一个dict。

- 调用函数时如何传入可变参数和关键字参数的语法：

  - 可变参数既可以直接传入：`func(1, 2, 3)`，又可以先组装list或tuple，再通过`*args`传入：`func(*(1, 2, 3))`
  - 关键字参数既可以直接传入：`func(a=1, b=2)`，又可以先组装dict，再通过`**kw`传入：`func(**{'a': 1, 'b': 2})`

- **使用`*args`和`**kw`是Python的习惯写法，当然也可以用其他参数名，但最好使用习惯用法。**

- 命名的关键字参数是为了限制调用者可以传入的参数名，同时可以提供默认值。

- 定义命名的关键字参数在没有可变参数的情况下不要忘了写分隔符*，否则定义的将是位置参数。

## 2 高级特性

### 2.1 切片

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

### 2.2 迭代

- 默认情况下，dict迭代的是key。如果要迭代value，可以用`for value in d.values()`，如果要同时迭代key和value，可以用`for k, v in d.items()`。

### 2.3 列表生成器

把一个list（包含各种数据类型）中所有的字符串变成小写：

```python
# -*- coding: utf-8 -*-
L1 = ['Hello', 'World', 18, 'Apple', None]
L2 = [s.lower() for s in L1 if isinstance(s,str)]
```

### 2.4 生成器（generator）

要创建一个generator，有很多种方法。

- 第一种方法很简单，只要把一个列表生成式的[]改成()，就创建了一个generator
  - 调用方法1: `next()` # 很少用
  - 调用方法2: `for i in generator:`

- 另一种方法。如果一个函数定义中包含`yield`关键字，那么这个函数就不再是一个普通函数，而是一个generator
  - 但是用for循环调用generator时，发现拿不到generator的return语句的返回值。如果想要拿到返回值，必须捕获StopIteration错误，返回值包含在StopIteration的valuez中

### 2.5 迭代器

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

#### 文档字符串

- Python有一种独一无二的的注释方式: 使用文档字符串. 文档字符串是包, 模块, 类或函数里的第一个语句. 这些字符串可以通过对象的__doc__成员被自动提取, 并且被pydoc所用. (你可以在你的模块上运行pydoc试一把, 看看它长什么样). 我们对文档字符串的惯例是使用三重双引号"""( PEP-257 ).

- 一个文档字符串应该这样组织: 首先是一行以句号, 问号或惊叹号结尾的概述(或者该文档字符串单纯只有一行). 接着是一个空行. 接着是文档字符串剩下的部分, 它应该与文档字符串的第一行的第一个引号对齐. 下面有更多文档字符串的格式化规范.

#### 模块注释

每个文件应该包含一个许可样板. 根据项目使用的许可(例如, Apache 2.0, BSD, LGPL, GPL), 选择合适的样板.

#### 函数和方法

下文所指的函数,包括函数, 方法, 以及生成器.

一个函数必须要有文档字符串, 除非它满足以下条件:

外部不可见

非常短小

简单明了

文档字符串应该包含函数做什么, 以及输入和输出的详细描述. 通常, 不应该描述"怎么做", 除非是一些复杂的算法. 文档字符串应该提供足够的信息, 当别人编写代码调用该函数时, 他不需要看一行代码, 只要看文档字符串就可以了. 对于复杂的代码, 在代码旁边加注释会比使用文档字符串更有意义.

关于函数的几个方面应该在特定的小节中进行描述记录， 这几个方面如下文所述. 每节应该以一个标题行开始. 标题行以冒号结尾. 除标题行外, 节的其他内容应被缩进2个空格.

- Args:

  列出每个参数的名字, 并在名字后使用一个冒号和一个空格, 分隔对该参数的描述.如果描述太长超过了单行80字符,使用2或者4个空格的悬挂缩进(与文件其他部分保持一致). 描述应该包括所需的类型和含义. 如果一个函数接受*foo(可变长度参数列表)或者**bar (任意关键字参数), 应该详细列出*foo和**bar.

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

#### 类的注释

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

#### 块注释和行注释

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
- Python之父Guido推荐的规范

Type | Public | Internal
---|---|---
Modules | lower_with_under | _lower_with_under
Packages | lower_with_under |
Classes | CapWords | _CapWords
Exceptions | CapWords
Functions | lower_with_under() | _lower_with_under()
Global/Class Constants | CAPS_WITH_UNDER | _CAPS_WITH_UNDER
Global/Class Variables | lower_with_under | _lower_with_under
Instance | Variables | lower_with_under | _lower_with_under (protected) or __lower_with_under (private)
Method Names | lower_with_under() | _lower_with_under() (protected) or __lower_with_under() (private)
Function/Method Parameters | lower_with_under
Local Variables | lower_with_under

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
