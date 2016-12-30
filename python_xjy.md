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
a.extend([1]) # ok
b = [1, 2, 3]
c = b
c.pop()  #b == [1, 2]
d = b[:]
d.pop()  #b == [1, 2]
b *= 3

``` 
## 1.a tuple
```python
a = 1, 2, 3
type(a)  # tuple
b = (1)
type(b)  # int
c = 1,
type(c)   # tuple

```

## 1.b 字符串格式化
```python
#位置参数
s = '{0} love {1} {2}'.format('I', 'you', 'forever')
#关键字参数
s = '{a} love {b} {c}'.format(a = 'I', b = 'you', c = 'forever')
#浮点格式
'{0:.1f}{1}'.format(27.658, 'GB')   # 27.7GB
```

## 1.c 序列
list(a) 要求a是一个可爹爹带对象
tuple(a)

```python
s= 'i love you'
s = list(s)
s = tuple(s)
a = '1234567890'
print(min(a))
print(max(s))
sorted(a)
reversed(a)
enumerate(a)
zip(a,s)
```
## 1.d 函数文档
```python
def test():
	'test用来测试函数文档'
	pass

test.__doc__
help(test)
```

## 1.e 关键字参数与默认参数
关键字参数是在调用的时候使用形参指定哪个实参与之对应
默认参数是在定义函数时。。。

## 1.f global与全局变量
在函数内部访问全局变量若需要修改，则必须用global声明一下，若不修改可直接使用
```python
g = 10
def test():
	g = 5
	print(g)
print(g)
def test1():
	global g
	g = 5
	print(g)
print(g)
```
## 1.g 内部函数和闭包
python函数可以嵌套，内部函数仅在函数内部可见
与全局变量相似，内部函数若要对其上层变量进行修改也会被屏蔽，在python2中需要用一个容器间接修改，python3 中使用nonlocal 关键字可以实现修改
闭包：如果在一个内部函数里对外层作用域的变量进行了引用，那么该内部函数就被认为时一个闭包
```python
def funx(x):
	def funy(y):   # funy是一个闭包
		nonlocal x
		x += 1
		return x * y
	return funy
i = funx(5)
i(8)
funx(5)(8)
```
## 1.h lambda
lambda使代码更加精简，不用考虑命名问题，简化代码可读性
```python
g = lambda x, y : x + y
g(2,4)
# 在filter中使用
l = list(filter(lambda x : x % 2, range(10)))  
l = list(map(lambda x : x * 2, range(10)))
```
## 1.i 递归
python3 默认最大递归层数为1000，可以设置最大递归层数
```python
import sys
sys.setrecursionlimit(100000) 
```
汉诺塔问题
```python
#首先要定义函数，首先想到的是使用一个参数，来表示汉诺塔的层数
def hano0(depth):
	pass
但是这样无法递归，若要使用递归，必须增加一些参数，所有的递归问题都是如此。我们需要将柱子信息引入，盘子原来放在x柱子，目的地是y柱子
def hano_r0(depth, x, y):
	pass
这样依然无法写出递归函数，我们必须再增加一些参数，即中间柱子z，这样就可以了
def hano_r(depth, x, y, z):  
	hano_r(depth - 1, x, z, y)
	print('x --> y')
	hano_r(depth - 1, z, x, y)
	
def hano(depth):
	hano_r(depth, x, y, z)
```

## 1.j 字典
```python
d.get(i)
d[i]
d.fromkeys()  # dict.fromkeys(seq,val=None)
a = {1:'one', 2:'two', 3:'three'}
a.pop(2)
a.popitem() # 随机弹出
a.setdefualt('小白')  # 小白是key
```
## 1.k集合
```python
a = {1, 3, 4, 5, 6}
b = set([1, 3, 4])
c = frozonset([1,2,3])  #不可变集合
```

## 1.l 深拷贝，浅拷贝，赋值
```python
a = [1, 2, 3, 4]
b = a.copy()
c = a
id(a)
id(b)
id(c)
```

## 1.m 文件
打开方式
```python
r w a b x t + U
r w a x  #x:create if not exist
b t
U
```
## 1.n 异常处理

```python

```

## 1.o else
else可以配合if，while，for，try等语句

else和while，for配合时，如果正常退出循环，则执行else中的语句，如果break退出循环则不执行else中的语句
```python
def showMaxFactor(num):
	count = num // 2
	while count > 1 :
		if num % count == 0:
			print('%d的最大因子是%d' % (num, count))
			break
		count -= 1
	else:
		print(%d是一个素数‘ % num)	
```
用于try也差不多
```python

try:
	int('abc')
except ValueError as reason:
	print('出错啦', + str(reason))
else:
	print('没有任何异常）

```

## 1.p 类的魔法方法
带有双下滑线的方法
1__init__(self[, ...]) 构造函数
2__new__(cls[, ...]) 对象实例化时被调用的第一个方法
```python
class CapStr(str):     # 由于str类无法修改，所以需要在__new__方法中将其大写
	def __new__(cls, string):
		string = string.upper()
		return str.__new(cls, string)
```
3__del__(self) 析构函数，GC在收回内存时才会调用此方法
4__add__  __sub__等


## 1.q 私有属性
python的私有时伪私有课通过对象._类__属性 访问
```python
class A:
	__name = 'aha'	
a = A()
a.name
a.__name
a._A__name
```

## 1.r property方法
```python
class C:
	def __init__(self, size = 10):
		self.size = size
	def getSize(self):
		return self.size
	def setSize(self, value):
		self.size = value
	def delSize(self):
		del self.size
	x = property(getSize, setSize, delSize)
c1 = C()
c1.x
c1.x = 19
del c1.x
```

## 1.xx 其他
```python 
print(1, 2, 3, 4, sep = '@', end = '$')
range(1, 10, 3)
```    