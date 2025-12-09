#python 
```python
```
Python 中的变量不需要声明。每个变量在使用前都必须赋值，变量赋值以后该变量才会被创建。
在 Python 中，变量就是变量，它没有类型，我们所说的"类型"是变量所指的内存中对象的类型。
等号（=）用来给变量赋值。
等号（=）运算符左边是一个变量名,等号（=）运算符右边是存储在变量中的值。例如：
```python
counter = 100          # 整型变量
miles   = 1000.0       # 浮点型变量
name    = "runoob"     # 字符串

print (counter)
print (miles)
print (name)
```
输出结果：
```
100
1000.0
runoob
```
# 多变量赋值
Python允许你同时为多个变量赋值。例如：
```python
a = b = c = 1
```
以上实例，创建一个整型对象，值为 1，从后向前赋值，三个变量被赋予相同的数值。
您也可以为多个对象指定多个变量。例如：
```python
a, b, c = 1, 2, "runoob"
```
以上实例，两个整型对象 1 和 2 的分配给变量 a 和 b，字符串对象 "runoob" 分配给变量 c。
# 标准数据类型
Python3 中常见的数据类型有：
- Number（数字）
- String（字符串）
- bool（布尔类型）
- List（列表）
- Tuple（元组）
- Set（集合）
- Dictionary（字典）
Python3 的六个标准数据类型中：
- **不可变数据（3 个）：** Number（数字）、String（字符串）、Tuple（元组）；
- **可变数据（3 个）：** List（列表）、Dictionary（字典）、Set（集合）。
此外还有一些高级的数据类型，如: 字节数组类型(bytes)。
## Number（数字）
Python3 支持 **int、float、bool、complex（复数）**。
在Python 3里，只有一种整数类型 int，表示为长整型，没有 python2 中的 Long。
像大多数语言一样，数值类型的赋值和计算都是很直观的。
内置的 type() 函数可以用来查询变量所指的对象类型。
```python
>>> a,b,c,d = 20,5.5,True,4+3j
>>> print(type(a),type(b),type(c),type(d))
<class'int'><class'float'><class'bool'><class'complex'>
```
此外还可以使用**isinstance**来判断：
```python
>>> a = 11
>>> isinstance(a,int)
True
```
isinstance 和 type 的区别在于：
- type()不会认为子类是一种父类类型。
- isinstance()会认为子类是一种父类类型。
```python
>>> class A:
...     pass
... 
>>> class B(A):
...     pass
... 
>>> isinstance(A(), A)
True
>>> type(A()) == A 
True
>>> isinstance(B(), A)
True
>>> type(B()) == A
False
```
**注意：**Python3 中，bool 是 int 的子类，True 和 False 可以和数字相加， True== 1、False == 0 会返回 **True**，但可以通过 is 来判断类型。
```python
>>> issubclass(bool, int) 
True
>>> True==1
True
>>> False==0
True
>>> True+1
2
>>> False+1
1
>>> 1 is True
False
>>> 0 is False
False
```
当你指定一个值时，Number对象就会被创建
```python
ver1 = 1
ver2 = 10
```
也可以使用del语句删除对象引用
del语句
```python
del ver1[,ver2[,ver3[....,varN]]]
```
您可以通过使用del语句删除单个或多个对象。例如：
```python
del var
del var_a, var_b
```
### 数值运算
```python
>>> 5 + 4  # 加法
9
>>> 4.3 - 2 # 减法
2.3
>>> 3 * 7  # 乘法
21
>>> 2 / 4  # 除法，得到一个浮点数
0.5
>>> 2 // 4 # 除法，得到一个整数
0
>>> 17 % 3 # 取余 
2
>>> 2 ** 5 # 乘方
32
```
**注意：**
- 1、Python可以同时为多个变量赋值，如a, b = 1, 2。
- 2、一个变量可以通过赋值指向不同类型的对象。
- 3、数值的除法包含两个运算符：`/ `返回一个浮点数，`//` 返回一个整数。
- 4、在混合计算时，Python会把整型转换成为浮点数。
### 数值类型实例
| int    | float      | complex    |
| ------ | ---------- | ---------- |
| 10     | 0.0        | 3.14j      |
| 100    | 15.20      | 45.j       |
| -786   | -21.9      | 9.322e-36j |
| 080    | 32.3e+18   | .876j      |
| -0490  | -90.       | -.6545+0J  |
| -0x260 | -32.54e100 | 3e+26J     |
| 0x69   | 70.2E-12   | 4.53e-7j   |
Python 还支持复数，复数由实数部分和虚数部分构成，可以用 `a + bj`，或者 `complex(a,b)` 表示， 复数的实部 **a** 和虚部 **b** 都是浮点型。
## 字符串
Python中的字符串用单引号 ' 或双引号 " 括起来，同时使用反斜杠 \ 转义特殊字符。
字符串的截取的语法格式如下：

>变量[头下标:尾下标]

索引值以 0 为开始值，-1 为从末尾的开始位置。
![[123456-20200923-1.svg]]
加号 + 是字符串的连接符， 星号 * 表示复制当前字符串，与之结合的数字为复制的次数。实例如下：
```python
#!/usr/bin/python3

str = 'Runoob'  # 定义一个字符串变量

print(str)           # 打印整个字符串
print(str[0:-1])     # 打印字符串第一个到倒数第二个字符（不包含倒数第一个字符）
print(str[0])        # 打印字符串的第一个字符
print(str[2:5])      # 打印字符串第三到第五个字符（包含第五个字符）
print(str[2:])       # 打印字符串从第三个字符开始到末尾
print(str * 2)       # 打印字符串两次
print(str + "TEST")  # 打印字符串和"TEST"拼接在一起
```
执行以上程序会输出如下结果：
```python
Runoob
Runoo
R
noo
noob
RunoobRunoob
RunoobTEST
```
Python 使用反斜杠 \ 转义特殊字符，如果你不想让反斜杠发生转义，可以在字符串前面添加一个 r，表示原始字符串：
```python
>>> print('Ru\noob')
Ru
oob
>>> print(r'Ru\noob')
Ru\noob
>>>
```
另外，反斜杠(\)可以作为续行符，表示下一行是上一行的延续。也可以使用 **"""..."""** 或者 **'''...'''** 跨越多行。
注意，Python 没有单独的字符类型，一个字符就是长度为1的字符串。
```python
>>> word = 'Python'
>>> print(word[0], word[5])
P n
>>> print(word[-1], word[-6])
n P
```
与 C 字符串不同的是，Python 字符串不能被改变。向一个索引位置赋值，比如 `word[0] = 'm' `会导致错误。
**注意：**
- 1、反斜杠可以用来转义，使用r可以让反斜杠不发生转义。
- 2、字符串可以用+运算符连接在一起，用*运算符重复。
- 3、Python中的字符串有两种索引方式，从左往右以0开始，从右往左以-1开始。
- 4、Python中的字符串不能改变。
## bool 布尔类型
布尔类型即 True 或 False。
在 Python 中，True 和 False 都是关键字，表示布尔值。
布尔类型可以用来控制程序的流程，比如判断某个条件是否成立，或者在某个条件满足时执行某段代码。
布尔类型特点：
- 布尔类型只有两个值：True 和 False。
- bool 是 int 的子类，因此布尔值可以被看作整数来使用，其中 True 等价于 1。
- 布尔类型可以和其他数据类型进行比较，比如数字、字符串等。在比较时，Python 会将 True 视为 1，False 视为 0。
- 布尔类型可以和逻辑运算符一起使用，包括 and、or 和 not。这些运算符可以用来组合多个布尔表达式，生成一个新的布尔值。
- 布尔类型也可以被转换成其他数据类型，比如整数、浮点数和字符串。在转换时，True 会被转换成 1，False 会被转换成 0。
- 可以使用 `bool()` 函数将其他类型的值转换为布尔值。以下值在转换为布尔值时为 `False`：`None`、`False`、零 (`0`、`0.0`、`0j`)、空序列（如 `''`、`()`、`[]`）和空映射（如 `{}`）。其他所有值转换为布尔值时均为 `True`。
```python
# 布尔类型的值和类型
a = True
b = False
print(type(a))  # <class 'bool'>
print(type(b))  # <class 'bool'>

# 布尔类型的整数表现
print(int(True))   # 1
print(int(False))  # 0

# 使用 bool() 函数进行转换
print(bool(0))         # False
print(bool(42))        # True
print(bool(''))        # False
print(bool('Python'))  # True
print(bool([]))        # False
print(bool([1, 2, 3])) # True

# 布尔逻辑运算
print(True and False)  # False
print(True or False)   # True
print(not True)        # False

# 布尔比较运算
print(5 > 3)  # True
print(2 == 2) # True
print(7 < 4)  # False

# 布尔值在控制流中的应用
if True:
    print("This will always print")
    
if not False:
    print("This will also always print")
    
x = 10
if x:
    print("x is non-zero and thus True in a boolean context")
```
**注意:** 在 Python 中，所有非零的数字和非空的字符串、列表、元组等数据类型都被视为 True，只有 **0、空字符串、空列表、空元组**等被视为 False。因此，在进行布尔类型转换时，需要注意数据类型的真假性。
## List(列表)
List（列表） 是 Python 中使用最频繁的数据类型。
列表可以完成大多数集合类的数据结构实现。列表中元素的类型可以不相同，它支持数字，字符串甚至可以包含列表（所谓嵌套）。
列表是写在方括号 [] 之间、用逗号分隔开的元素列表。
和字符串一样，列表同样可以被索引和截取，列表被截取后返回一个包含所需元素的新列表。
列表截取的语法格式如下：
```
变量[头下标:尾下标]
```
索引值以 0 为开始值，-1 为从末尾的开始位置。
![[Pasted image 20250110171211.png]]
加号 + 是列表连接运算符，星号 * 是重复操作。如下实例：
```python
list = [ 'abcd', 786 , 2.23, 'runoob', 70.2 ]  # 定义一个列表
tinylist = [123, 'runoob']

print (list)            # 打印整个列表
print (list[0])         # 打印列表的第一个元素
print (list[1:3])       # 打印列表第二到第四个元素（不包含第四个元素）
print (list[2:])        # 打印列表从第三个元素开始到末尾
print (tinylist * 2)    # 打印tinylist列表两次
print (list + tinylist)  # 打印两个列表拼接在一起的结果
```
以上实例输出结果：
```python
['abcd', 786, 2.23, 'runoob', 70.2]
abcd
[786, 2.23]
[2.23, 'runoob', 70.2]
[123, 'runoob', 123, 'runoob']
['abcd', 786, 2.23, 'runoob', 70.2, 123, 'runoob']
```


