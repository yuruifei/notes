---
title: python_xjy
tags: python, 笔记
grammar_cjkRuby: true
---
# 1 基础知识杂记
## 1.1 原始字符串
避免使用转义字符
```python
print(r'C:\windows\system32')
```

## 1.2 整型与溢出
v2.2 （2.x）以后，python支持不会溢出的 long 型。
v3.0后，确切的讲， int 型（依赖运行环境C编译器中long型的精度）消失了，long型替代 int 型，成为新的、不依赖运行环境的、无精度限制的（只要内存装得下）int型。

## 1.3 布尔型
其实是整数
```python
a = True + True  # a == 2 
```

## 1.4 确定数据类型
type()  和 isinstacne()
```python
a = 520
b = '520'
type(a)
type(b)
isinstance(a, int)
isinstance(b, str)
```

## 1.5 算数运算
```python
9 / 2  #  4.5
9 // 2  # 4
9.0 // 2 # 4.0
3 ** 2  # 9
-3 ** 2  # -9
(-3) ** 2 # 9
```

## 1.6 比较操作
```python
3 < 4 < 5   # True  3 < 4 and 4 < 5
```
## 1.7 悬挂else
在c中else就近匹配，很容以形成悬挂else，但是在python中缩进控制语句块，不容易产生悬挂else

## 1.8 三目运算
```python
small = x if x < y else y
```
## 1.9 列表
```python
a = [1, 3, 4,]
a.pop()
a.pop(1)
a.extend((1,)) 
a.extend((1,2))
a.extend((1))  # err
a.extend(1)  # err 

```

## 1.xx 其他
```python
print(1, 2, 3, 4, sep = '@', end = '$')
range(1, 10, 3)
```