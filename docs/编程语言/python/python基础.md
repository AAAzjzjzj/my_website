# Python 基础

Python 是一种高级、解释型、面向对象的编程语言，以其简洁易读的语法而闻名。

## 安装 Python

### Windows

1. 访问 https://www.python.org/downloads/
2. 下载最新版本
3. 运行安装程序，勾选"Add Python to PATH"

### 验证安装

```bash
python --version
pip --version
```

## 基本语法

### 变量和数据类型

```python
# 整数
age = 25

# 浮点数
price = 19.99

# 字符串
name = "张三"

# 布尔值
is_student = True

# 列表
fruits = ["apple", "banana", "orange"]

# 元组（不可变）
coordinates = (10, 20)

# 字典
person = {"name": "张三", "age": 25}

# 集合
numbers = {1, 2, 3, 4, 5}
```

### 输入输出

```python
# 输出
print("Hello, World!")
print(f"我的名字是 {name}")

# 输入
name = input("请输入你的名字: ")
age = int(input("请输入你的年龄: "))
```

### 运算符

```python
# 算术运算符
a = 10 + 5   # 加法
b = 10 - 5   # 减法
c = 10 * 5   # 乘法
d = 10 / 5   # 除法
e = 10 // 3  # 整除
f = 10 % 3   # 取余
g = 2 ** 3   # 幂运算

# 比较运算符
x == y  # 等于
x != y  # 不等于
x > y   # 大于
x < y   # 小于
x >= y  # 大于等于
x <= y  # 小于等于

# 逻辑运算符
x and y  # 与
x or y   # 或
not x    # 非
```

## 控制流

### 条件语句

```python
# if-elif-else
age = 18

if age < 18:
    print("未成年")
elif age < 60:
    print("成年人")
else:
    print("老年人")

# 三元运算符
status = "成年" if age >= 18 else "未成年"
```

### 循环

```python
# for 循环
for i in range(5):
    print(i)

# 遍历列表
fruits = ["apple", "banana", "orange"]
for fruit in fruits:
    print(fruit)

# 遍历字典
person = {"name": "张三", "age": 25}
for key, value in person.items():
    print(f"{key}: {value}")

# while 循环
count = 0
while count < 5:
    print(count)
    count += 1

# break 和 continue
for i in range(10):
    if i == 3:
        continue  # 跳过本次循环
    if i == 7:
        break     # 终止循环
    print(i)
```

## 函数

### 定义函数

```python
# 基本函数
def greet(name):
    return f"Hello, {name}!"

# 默认参数
def greet(name="World"):
    return f"Hello, {name}!"

# 多个返回值
def get_user_info():
    return "张三", 25

name, age = get_user_info()

# 可变参数
def sum_all(*args):
    return sum(args)

result = sum_all(1, 2, 3, 4, 5)

# 关键字参数
def print_info(**kwargs):
    for key, value in kwargs.items():
        print(f"{key}: {value}")

print_info(name="张三", age=25)
```

### Lambda 函数

```python
# 匿名函数
square = lambda x: x ** 2
print(square(5))  # 25

# 在 map 中使用
numbers = [1, 2, 3, 4, 5]
squared = list(map(lambda x: x ** 2, numbers))
```

## 列表操作

### 列表方法

```python
fruits = ["apple", "banana"]

# 添加元素
fruits.append("orange")      # 末尾添加
fruits.insert(1, "grape")    # 指定位置插加
fruits.extend(["mango", "pear"])  # 添加多个

# 删除元素
fruits.remove("banana")      # 删除指定元素
popped = fruits.pop()        # 删除并返回最后一个
fruits.pop(0)                # 删除指定索引
del fruits[0]                # 删除指定索引
fruits.clear()               # 清空列表

# 其他操作
fruits.sort()                # 排序
fruits.reverse()             # 反转
count = fruits.count("apple")  # 计数
index = fruits.index("apple")  # 查找索引
```

### 列表推导式

```python
# 基本形式
squares = [x ** 2 for x in range(10)]

# 带条件
even_squares = [x ** 2 for x in range(10) if x % 2 == 0]

# 嵌套
matrix = [[i * j for j in range(3)] for i in range(3)]
```

## 字符串操作

```python
text = "Hello, World!"

# 常用方法
text.upper()           # 转大写
text.lower()           # 转小写
text.strip()           # 去除首尾空格
text.replace("Hello", "Hi")  # 替换
text.split(",")        # 分割
"-".join(["a", "b", "c"])  # 连接

# 字符串格式化
name = "张三"
age = 25

# f-string（推荐）
message = f"我叫{name}，今年{age}岁"

# format 方法
message = "我叫{}，今年{}岁".format(name, age)

# % 格式化
message = "我叫%s，今年%d岁" % (name, age)
```

## 文件操作

### 读写文件

```python
# 写文件
with open("file.txt", "w", encoding="utf-8") as f:
    f.write("Hello, World!\n")
    f.write("第二行")

# 读文件
with open("file.txt", "r", encoding="utf-8") as f:
    content = f.read()  # 读取全部
    
# 逐行读取
with open("file.txt", "r", encoding="utf-8") as f:
    for line in f:
        print(line.strip())

# 追加内容
with open("file.txt", "a", encoding="utf-8") as f:
    f.write("\n新的一行")
```

### JSON 操作

```python
import json

# 写入 JSON
data = {"name": "张三", "age": 25}
with open("data.json", "w", encoding="utf-8") as f:
    json.dump(data, f, ensure_ascii=False, indent=2)

# 读取 JSON
with open("data.json", "r", encoding="utf-8") as f:
    data = json.load(f)

# 字符串转换
json_str = json.dumps(data, ensure_ascii=False)
data = json.loads(json_str)
```

## 异常处理

```python
# try-except
try:
    result = 10 / 0
except ZeroDivisionError:
    print("除数不能为零")
except Exception as e:
    print(f"发生错误: {e}")
else:
    print("没有发生错误")
finally:
    print("无论如何都会执行")

# 抛出异常
def divide(a, b):
    if b == 0:
        raise ValueError("除数不能为零")
    return a / b
```

## 模块和包

### 导入模块

```python
# 导入整个模块
import math
print(math.sqrt(16))

# 导入特定函数
from math import sqrt, pi
print(sqrt(16))

# 导入并重命名
import numpy as np
import pandas as pd

# 导入所有（不推荐）
from math import *
```

### 创建模块

```python
# mymodule.py
def greet(name):
    return f"Hello, {name}!"

PI = 3.14159

# 使用模块
import mymodule
print(mymodule.greet("张三"))
print(mymodule.PI)
```

## 常用内置函数

```python
# 类型转换
int("10")      # 转整数
float("3.14")  # 转浮点数
str(100)       # 转字符串
list("abc")    # 转列表

# 数学函数
abs(-5)        # 绝对值
max(1, 2, 3)   # 最大值
min(1, 2, 3)   # 最小值
sum([1, 2, 3]) # 求和
round(3.14159, 2)  # 四舍五入

# 序列函数
len([1, 2, 3])     # 长度
sorted([3, 1, 2])  # 排序
reversed([1, 2, 3])  # 反转
enumerate(["a", "b", "c"])  # 枚举
zip([1, 2], ["a", "b"])     # 打包

# 其他
type(x)        # 获取类型
isinstance(x, int)  # 类型检查
id(x)          # 获取对象 ID
```

## 应用场景

- Web 开发
- 数据分析
- 机器学习
- 自动化脚本
- 爬虫
- 游戏开发
