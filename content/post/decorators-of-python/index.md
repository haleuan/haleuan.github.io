+++
title = 'A Brief Introduction to Python Decorators'
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
+++

## Basic Usage

First, keep in mind that Python functions have the same properties as ordinary variables: **they can be passed around and returned**. Let's look at a concrete example:

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

The output is:

```
Calling `greet`
Hello, Alice
`greet` called successfully
```

Decorator syntax makes the code shorter:

```python
@logging_decorator
def greet(name: str) -> None:
    print(f"Hello, {name}")
greet("Alice")
```

These two forms are equivalent.

One easy-to-miss but important pitfall is that after decoration, `greet.__name__` becomes `wrapper`, and `greet.__doc__` is lost. The name `greet` is now bound to the `wrapper` object. This can cause problems if your code relies on a function's name for logging, registration, or reflection. The fix is to use `functools.wraps` from the standard library. It is itself a decorator that copies the original function's metadata to `wrapper`:

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

## Decorators That Take Arguments

What if a decorator needs to accept arguments? It needs one more layer of functions. First, let's review a decorator without arguments:

```python
def decorator(func):
    def wrapper(*args, **kwargs):
        return func(*args, **kwargs)
    return wrapper

@decorator
def say_hi():
    print("hi")
```

There are two stages:

First, **when the function is defined** (when Python parses the lines containing `@decorator`, before `say_hi()` is called), `decorator(say_hi)` runs once. It returns `wrapper`, and the name `say_hi` is rebound to `wrapper`.

Second, **when the function is called** (when you write `say_hi()`), Python actually runs `wrapper()`.

The key point is that the `@decorator` syntax does one thing: **it passes the function below it as the sole argument to `decorator`, then replaces the original function name with the return value**. The syntax is always `func_name = decorator(func_name)`. You cannot add more arguments after `decorator` unless evaluating `decorator` itself produces a function.

A closure makes the idea easier to understand. We want the decorated function to receive arguments, or the decorator function to receive some values; those values only need to be accessible to the decorated function. Here's a simple example:

```python
num = 3

def func():
    return num + 3

func()
```

We can use the same idea to pass arguments to a decorator:

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

This makes the pattern easier to understand.

There is one issue to keep in mind: this closure lets us read a variable, but without extra handling it does not let us modify that variable. Here's the problem:

```python
def repeat(times):
    def decorator(func):
        def wrapper(*args, **kwargs):
            times = times - 1  # Try to update external value
            return func(*args, **kwargs)
        return wrapper
    return decorator
```

This code raises `UnboundLocalError: local variable 'times' referenced before assignment`.

Python sees the assignment `times = times - 1` and treats `times` as a local variable in `wrapper`. But the right-hand side tries to read `times` before that local variable has been initialized, so an error is raised.

By default, **if a function assigns to a variable name anywhere in its body, that name is treated as local throughout the function's scope**. This applies even if you only intended to read an outer variable before assigning to it.

Use the `nonlocal` keyword to tell Python, "I want to modify the variable in the enclosing scope, not create a new local variable":

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

**Another common alternative** is to use a mutable object (such as a list or dictionary) as a container. Mutating its contents (for example, calling `list.append()` or assigning `dict[key] = value`) is not an assignment to the variable name, so it does not trigger the local-variable rule:

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

This avoids `nonlocal`, though the code is a little less direct. Choose based on personal and team preference: `nonlocal` is more straightforward, while a mutable container is a little more functional because it avoids rebinding a variable.

**One easy-to-miss point:** in this example, `times` changes each time the decorated function is called, so **the decorator is stateful**. If multiple threads call the same decorated function at once, a race condition can occur because updates to `times` are not thread-safe. This is why stateful decorators are often implemented as classes in practice: storing state in `self` makes it easier to protect with a lock than storing it in a closure variable.

> **Translation note:** This English version was translated with the assistance of an LLM.
