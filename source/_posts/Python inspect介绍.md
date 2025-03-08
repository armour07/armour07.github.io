---
title: Python inspect介绍
date: 2024-04-01 10:35:00
tags:
- Python
- inspect
categories:
- Python
- module
---

## 简介
`inspect`模块提供了许多有用的函数来帮助我们获取活动对象的信息，如模块、类、方法、函数、回溯等。它对于调试、元编程以及需要动态分析代码的场景非常有用。

## 核心功能

### 获取源代码信息
```python
import inspect

def my_function():
    pass

print(inspect.getsource(my_function))
```

### 获取类或函数的参数信息
```python
import inspect

def greet(name, greeting="Hello"):
    return f"{greeting}, {name}"

signature = inspect.signature(greet)
print(signature)
```

### 获取调用栈信息
```python
import inspect

def foo():
    caller_frame = inspect.currentframe().f_back
    print(f"Caller: {caller_frame.f_code.co_name}")

def bar():
    foo()

bar()
```

### 检查对象类型
```python
import inspect

class MyClass:
    pass

obj = MyClass()
print(inspect.isclass(obj))
print(inspect.ismethod(obj))
```