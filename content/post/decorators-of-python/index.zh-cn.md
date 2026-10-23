+++
title = 'Python 中的装饰器浅谈'
date = '2026-10-23T21:24:03+08:00'
draft = false
categories = []
tags = []

comments = false

ShowToc = true
TocOpen = false
hideMeta = false
disableShare = false
hideSummary = false
searchHidden = false
ShowReadingTime = true
ShowBreadCrumbs = true
ShowPostNavLinks = true
ShowWordCount = true
ShowRssButtonInSectionTermList = true
UseHugoToc = true

# Hugo core front matter fields (enable as needed).
# description = ''
# summary = ''
# slug = ''
# linkTitle = ''
# aliases = []
# keywords = []
# weight = 0
# lastmod = '2026-10-23T21:24:03+08:00'
# publishDate = '2026-10-23T21:24:03+08:00'
# expiryDate = ''
# translationKey = ''

# PaperMod and site-specific options (enable as needed).
# canonicalURL = ''
# ShowCanonicalLink = false
# disableAnchoredHeadings = false
# robotsNoIndex = false
# author = ''

# [cover]
# image = ''
# alt = ''
# caption = ''
# relative = false
# hidden = false
# hiddenInSingle = false
# hiddenInList = false
# responsiveImages = true

# [editPost]
# URL = 'https://github.com/haleuan/haleuan.github.io/tree/main/content'
# Text = 'Suggest Changes'
# appendFilePath = true
# disabled = false
+++


## 基础用法

首先明晰一点，Python 中的函数具有和普通变量一样的特性：**可以被传递和返回**。接下来用一个具体的例子来看：

```python
def logging_decorator(func):
    def wrapper(*args, **kwargs):
        print(f"Calling `{func.__name__}`")
        result = func(*args, **kwargs)
        print(f"`{func.__name__}` called successfully")
        return result
    return wrapper

def greet(name: str) -> None:
    print(f"Hello, {name}")

greet = logging_decorator(greet)  # decorate the greet function manually
greet("Alice")
```

对应的输出为：

```
Calling `greet`
Hello, Alice
`greet` called successfully
```

使用装饰器语法糖后，代码可以简化为：

```python
@logging_decorator
def greet(name: str) -> None:
    print(f"Hello, {name}")
greet("Alice")
```

这两种写法完全等价。

一个容易被忽略但很重要的坑：装饰之后，`greet.__name__` 会变成 `wrapper`，`greet.__doc__` 也会丢失，因为现在 `greet` 这个名字实际上绑定的是 `wrapper` 这个对象。如果你的代码依赖函数名字做日志、注册、反射，这会出问题。解决办法是用标准库里的 `functools.wraps`，它本身也是一个装饰器，作用是把原函数的元信息拷贝到 `wrapper` 上

```python
import functools

def logging_decorator(func):
    @functools.wraps(func)
    def wrapper(*args, **kwargs):
        print(f"Calling {func.__name__}")
        return func(*args, **kwargs)

    return wrapper
```

---

## 装饰器接收参数

那么如果装饰器需要接收参数呢？这就需要再套一层函数了。先回顾一下没有参数的装饰器：

```python
def decorator(func):
    def wrapper(*args, **kwargs):
        return func(*args, **kwargs)
    return wrapper

@decorator
def say_hi():
    print("hi")
```

这里发生了什么事，分两个阶段：

第一阶段，**定义时**（也就是 Python 解析到 `@decorator` 这几行代码的时候，还没调用 `say_hi()`）：`decorator(say_hi)` 被执行一次，它返回 `wrapper`，然后 `say_hi` 这个名字被重新绑定到 `wrapper` 上。

第二阶段，**调用时**（写 `say_hi()`）：实际执行的是 `wrapper()`。
关键点：`@decorator` 语法糖只干一件事：**把它下面那个函数作为唯一参数，传给 `decorator`，然后用返回值替换掉原来的函数名**。语法上永远是 `func_name = decorator(func_name)`，`decorator` 后面不能再多带别的参数，除非 `decorator` 这个词本身求值后是一个函数。

其实用一个闭包（closure）的例子就很好解释了：我们希望被装饰函数能够接收一些参数，或者是装饰器函数能接收一些函数，那么只要这些变量是被装饰函数能够读取到即可，来看看下面这个小例子：

```python
num = 3

def func():
    return num + 3

func()
```

所以可以根据这个思想来构建需要传入的参数：

```python
def repeat(times):
    def decorator(func):
        def wrapper(*args, **kwargs):
            for _ in range(times):
                result = func(*args, **kwargs)
            return result
        return wrapper
    return decorator

@repeat(times=3)
def say_hi():
    print("hi")
```

这样就很好理解了。

但同时，有一个需要注意的问题，这样子的闭包会造成只能读取变量，在不作额外处理的情况下不能修改变量的值；重现一下问题：

```python
def repeat(times):
    def decorator(func):
        def wrapper(*args, **kwargs):
            times = times - 1  # Try to update external value
            return func(*args, **kwargs)
        return wrapper
    return decorator
```

这段代码会直接报错：`UnboundLocalError: local variable 'times' referenced before assignment`。

原因是 Python 看到 `times = times - 1` 这一行，发现你在给 `times` 赋值，就认为 `times` 是 `wrapper` 的局部变量，但右边 `times - 1` 又要先读取 `times` 的值，而局部变量此时还没被初始化，所以报错。

Python 默认规则：**如果一个函数内部对某个变量名有赋值操作，那这个名字就被当成局部变量，整个函数作用域内都是局部变量，哪怕你只是想在赋值之前先读一下外层的同名变量也不行**。

解决办法是用 `nonlocal` 关键字，明确告诉 Python："我要修改的是外层作用域的那个变量，不是创建新的局部变量"：

```python
def repeat(times):
    def decorator(func):
        def wrapper(*args, **kwargs):
            nonlocal times  # key point
            times = times - 1
            print(f"Remaining: {times}")
            return func(*args, **kwargs)
        return wrapper
    return decorator

@repeat(times=3)
def say_hi():
    print("hi")

say_hi()
say_hi()
say_hi()
```

**另一种常见的替代方案**：用可变对象（比如列表、字典）作为"容器"，避免直接给变量名赋值。因为修改可变对象的内容（比如 `list.append()` 或字典的 `dict[key] = value`）不算"赋值"，不会触发"创建局部变量"的逻辑：

```python
def repeat(times):
    state = {'remaining': times}
    def decorator(func):
        def wrapper(*args, **kwargs):
            state['remaining'] -= 1  # Update dictionary's value without nonlocal keyword
            print(f"Remaining: {state['remaining']}")
            return func(*args, **kwargs)
        return wrapper
    return decorator
```

这种写法不需要 `nonlocal`，但代码稍微绕一点。选哪种看个人喜好和团队习惯——`nonlocal` 更直接，可变容器的方式更"函数式"一些（避免直接修改变量绑定）。

**容易忽略的点**：如果这样写，每次调用被装饰的函数，`times` 的值都会改变，意味着**这个装饰器是有状态的**。多线程环境下如果多个线程同时调用同一个被装饰的函数，可能会有竞争条件（race condition），`times` 的修改不是线程安全的。这也是为什么在实践中，带状态的装饰器更常用类来实现（把状态存在 `self` 上），而不是闭包变量——类的实例属性更容易加锁保护。