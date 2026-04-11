# Python 进阶

本文介绍 Python 的进阶特性和高级用法。

## 面向对象编程

### 类和对象

```python
class Person:
    # 类属性
    species = "Human"
    
    # 构造函数
    def __init__(self, name, age):
        self.name = name  # 实例属性
        self.age = age
    
    # 实例方法
    def greet(self):
        return f"Hello, I'm {self.name}"
    
    # 类方法
    @classmethod
    def from_birth_year(cls, name, birth_year):
        age = 2024 - birth_year
        return cls(name, age)
    
    # 静态方法
    @staticmethod
    def is_adult(age):
        return age >= 18

# 创建对象
person = Person("张三", 25)
print(person.greet())

# 使用类方法
person2 = Person.from_birth_year("李四", 1995)

# 使用静态方法
print(Person.is_adult(20))
```

### 继承

```python
# 父类
class Animal:
    def __init__(self, name):
        self.name = name
    
    def speak(self):
        pass

# 子类
class Dog(Animal):
    def speak(self):
        return f"{self.name} says Woof!"

class Cat(Animal):
    def speak(self):
        return f"{self.name} says Meow!"

# 使用
dog = Dog("旺财")
print(dog.speak())
```

### 多重继承

```python
class A:
    def method_a(self):
        print("Method A")

class B:
    def method_b(self):
        print("Method B")

class C(A, B):
    def method_c(self):
        print("Method C")

c = C()
c.method_a()
c.method_b()
c.method_c()
```

### 魔法方法

```python
class Vector:
    def __init__(self, x, y):
        self.x = x
        self.y = y
    
    # 字符串表示
    def __str__(self):
        return f"Vector({self.x}, {self.y})"
    
    def __repr__(self):
        return f"Vector({self.x}, {self.y})"
    
    # 运算符重载
    def __add__(self, other):
        return Vector(self.x + other.x, self.y + other.y)
    
    def __eq__(self, other):
        return self.x == other.x and self.y == other.y
    
    # 长度
    def __len__(self):
        return int((self.x ** 2 + self.y ** 2) ** 0.5)
    
    # 索引访问
    def __getitem__(self, index):
        if index == 0:
            return self.x
        elif index == 1:
            return self.y
        raise IndexError("Index out of range")

v1 = Vector(1, 2)
v2 = Vector(3, 4)
v3 = v1 + v2
print(v3)
```

## 装饰器

### 函数装饰器

```python
# 简单装饰器
def timer(func):
    import time
    def wrapper(*args, **kwargs):
        start = time.time()
        result = func(*args, **kwargs)
        end = time.time()
        print(f"{func.__name__} took {end - start:.2f} seconds")
        return result
    return wrapper

@timer
def slow_function():
    import time
    time.sleep(1)
    return "Done"

# 带参数的装饰器
def repeat(times):
    def decorator(func):
        def wrapper(*args, **kwargs):
            for _ in range(times):
                result = func(*args, **kwargs)
            return result
        return wrapper
    return decorator

@repeat(3)
def greet(name):
    print(f"Hello, {name}!")
```

### 类装饰器

```python
class CountCalls:
    def __init__(self, func):
        self.func = func
        self.count = 0
    
    def __call__(self, *args, **kwargs):
        self.count += 1
        print(f"Call {self.count} of {self.func.__name__}")
        return self.func(*args, **kwargs)

@CountCalls
def say_hello():
    print("Hello!")

say_hello()
say_hello()
```

## 生成器

### 生成器函数

```python
# 使用 yield
def fibonacci(n):
    a, b = 0, 1
    for _ in range(n):
        yield a
        a, b = b, a + b

for num in fibonacci(10):
    print(num)

# 生成器表达式
squares = (x ** 2 for x in range(10))
print(next(squares))
```

### 生成器应用

```python
# 读取大文件
def read_large_file(file_path):
    with open(file_path, 'r') as f:
        for line in f:
            yield line.strip()

# 无限序列
def infinite_sequence():
    num = 0
    while True:
        yield num
        num += 1
```

## 上下文管理器

### 使用 with 语句

```python
# 文件操作
with open('file.txt', 'r') as f:
    content = f.read()

# 自定义上下文管理器
class FileManager:
    def __init__(self, filename, mode):
        self.filename = filename
        self.mode = mode
        self.file = None
    
    def __enter__(self):
        self.file = open(self.filename, self.mode)
        return self.file
    
    def __exit__(self, exc_type, exc_val, exc_tb):
        if self.file:
            self.file.close()

with FileManager('file.txt', 'r') as f:
    content = f.read()
```

### contextlib 模块

```python
from contextlib import contextmanager

@contextmanager
def file_manager(filename, mode):
    f = open(filename, mode)
    try:
        yield f
    finally:
        f.close()

with file_manager('file.txt', 'r') as f:
    content = f.read()
```

## 迭代器

```python
class Counter:
    def __init__(self, start, end):
        self.current = start
        self.end = end
    
    def __iter__(self):
        return self
    
    def __next__(self):
        if self.current > self.end:
            raise StopIteration
        self.current += 1
        return self.current - 1

for num in Counter(1, 5):
    print(num)
```

## 多线程和多进程

### 多线程

```python
import threading
import time

def worker(name):
    print(f"Worker {name} starting")
    time.sleep(2)
    print(f"Worker {name} finished")

# 创建线程
threads = []
for i in range(5):
    t = threading.Thread(target=worker, args=(i,))
    threads.append(t)
    t.start()

# 等待所有线程完成
for t in threads:
    t.join()
```

### 多进程

```python
from multiprocessing import Process

def worker(name):
    print(f"Worker {name} starting")
    time.sleep(2)
    print(f"Worker {name} finished")

if __name__ == '__main__':
    processes = []
    for i in range(5):
        p = Process(target=worker, args=(i,))
        processes.append(p)
        p.start()
    
    for p in processes:
        p.join()
```

### 线程池

```python
from concurrent.futures import ThreadPoolExecutor

def task(n):
    return n * n

with ThreadPoolExecutor(max_workers=5) as executor:
    results = executor.map(task, range(10))
    for result in results:
        print(result)
```

## 正则表达式

```python
import re

text = "My email is test@example.com"

# 搜索
match = re.search(r'\w+@\w+\.\w+', text)
if match:
    print(match.group())

# 查找所有
emails = re.findall(r'\w+@\w+\.\w+', text)

# 替换
new_text = re.sub(r'\w+@\w+\.\w+', 'EMAIL', text)

# 分割
parts = re.split(r'\s+', text)

# 编译正则表达式
pattern = re.compile(r'\w+@\w+\.\w+')
match = pattern.search(text)
```

## 类型提示

```python
from typing import List, Dict, Optional, Union

def greet(name: str) -> str:
    return f"Hello, {name}!"

def process_items(items: List[int]) -> int:
    return sum(items)

def get_user(user_id: int) -> Optional[Dict[str, str]]:
    if user_id > 0:
        return {"name": "张三", "email": "test@example.com"}
    return None

def process_value(value: Union[int, str]) -> str:
    return str(value)
```

## 数据类

```python
from dataclasses import dataclass

@dataclass
class Person:
    name: str
    age: int
    email: str = ""
    
    def greet(self):
        return f"Hello, I'm {self.name}"

person = Person("张三", 25)
print(person)
print(person.greet())
```

## 枚举

```python
from enum import Enum

class Color(Enum):
    RED = 1
    GREEN = 2
    BLUE = 3

print(Color.RED)
print(Color.RED.value)
print(Color.RED.name)

# 遍历
for color in Color:
    print(color)
```

## 属性装饰器

```python
class Circle:
    def __init__(self, radius):
        self._radius = radius
    
    @property
    def radius(self):
        return self._radius
    
    @radius.setter
    def radius(self, value):
        if value < 0:
            raise ValueError("Radius cannot be negative")
        self._radius = value
    
    @property
    def area(self):
        return 3.14159 * self._radius ** 2

circle = Circle(5)
print(circle.radius)
print(circle.area)
circle.radius = 10
```

## 应用场景

- 大型项目开发
- 框架设计
- 性能优化
- 并发编程
- 元编程
- API 设计
