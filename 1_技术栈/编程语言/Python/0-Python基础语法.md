#菜鸟教程 #python
***
# 简介

Python 是一个高层次的结合了解释性、编译性、互动性和面向对象的脚本语言。
Python 的设计具有很强的可读性，相比其他语言经常使用英文关键字，其他语言的一些标点符号，它具有比其他语言更有特色语法结构。
- **Python 是一种解释型语言：** 这意味着开发过程中没有了编译这个环节。类似于PHP和Perl语言。
- **Python 是交互式语言：** 这意味着，您可以在一个 Python 提示符 >>> 后直接执行代码。
- **Python 是面向对象语言:** 这意味着Python支持面向对象的风格或代码封装在对象的编程技术。
- **Python 是初学者的语言：** Python 对初级程序员而言，是一种伟大的语言，它支持广泛的应用程序开发，从简单的文字处理到 WWW 浏览器再到游戏。
***
# 发展历史

Python 是由 Guido van Rossum 在八十年代末和九十年代初，在荷兰国家数学和计算机科学研究所设计出来的。
Python 本身也是由诸多其他语言发展而来的,这包括 ABC、Modula-3、C、C++、Algol-68、SmallTalk、Unix shell 和其他的脚本语言等等。
像 Perl 语言一样，Python 源代码同样遵循 GPL(GNU General Public License)协议。
现在 Python 是由一个核心开发团队在维护，Guido van Rossum 仍然占据着至关重要的作用，指导其进展。
Python 2.0 于 2000 年 10 月 16 日发布，增加了实现完整的垃圾回收，并且支持 Unicode。
Python 3.0 于 2008 年 12 月 3 日发布，此版不完全兼容之前的 Python 源代码。不过，很多新特性后来也被移植到旧的Python 2.6/2.7版本。
Python 3.0 版本，常被称为 Python 3000，或简称 Py3k。相对于 Python 的早期版本，这是一个较大的升级。
Python 2.7 被确定为最后一个 Python 2.x 版本，它除了支持 Python 2.x 语法外，还支持部分 Python 3.1 语法。
***
# 特点
- **1.易于学习：** Python有相对较少的关键字，结构简单，和一个明确定义的语法，学习起来更加简单。
- **2.易于阅读：** Python代码定义的更清晰。
- **3.易于维护：** Python的成功在于它的源代码是相当容易维护的。
- **4.一个广泛的标准库：** Python的最大的优势之一是丰富的库，跨平台的，在UNIX，Windows和Macintosh兼容很好。
- **5.互动模式：** 互动模式的支持，您可以从终端输入执行代码并获得结果的语言，互动的测试和调试代码片断。
- **6.可移植：** 基于其开放源代码的特性，Python已经被移植（也就是使其工作）到许多平台。
- **7.可扩展：** 如果你需要一段运行很快的关键代码，或者是想要编写一些不愿开放的算法，你可以使用C或C++完成那部分程序，然后从你的Python程序中调用。
- **8.数据库：** Python提供所有主要的商业数据库的接口。
- **9.GUI编程：** Python支持GUI可以创建和移植到许多系统调用。
- **10.可嵌入:** 你可以将Python嵌入到C/C++程序，让你的程序的用户获得"脚本化"的能力。
***
# 基础语法
## 编码
默认情况下，Python 3 源码文件以 **UTF-8** 编码，所有字符串都是 unicode 字符串。 当然你也可以为源码文件指定不同的编码：
```python
-*- coding: cp-1252 -*-
```
上述定义允许在源文件中使用 Windows-1252 字符集中的字符编码，对应适合语言为保加利亚语、白俄罗斯语、马其顿语、俄语、塞尔维亚语。
## 标识符
- 第一个字符必须是字母表中字母或下划线 _ 。
- 标识符的其他的部分由字母、数字和下划线组成。
- 标识符对大小写敏感。
在 Python 3 中，可以用中文作为变量名，非 ASCII 标识符也是允许的了。
## Python保留字
保留字即关键字，我们不能把它们用作任何标识符名称。Python 的标准库提供了一个 keyword 模块，可以输出当前版本的所有关键字：
```python
>>> import keyword
>>> keyword.kwlist
['False', 'None', 'True', 'and', 'as', 'assert', 'break', 'class', 'continue', 'def', 'del', 'elif', 'else', 'except', 'finally', 'for', 'from', 'global', 'if', 'import', 'in', 'is', 'lambda', 'nonlocal', 'not', 'or', 'pass', 'raise', 'return', 'try', 'while', 'with', 'yield']
```
## 注释
Python中单行注释以 **#** 开头，实例如下：
```python
# 第一个注释 
print ("Hello, Python!") # 第二个注释
```
多行注释可以用多个 # 号，还有 ''' 和 """：
```python
# 第一个注释
# 第二个注释
 
'''
第三注释
第四注释
'''
 
"""
第五注释
第六注释
"""
print ("Hello, Python!")
```
## 行与缩进
python最具特色的就是使用**缩进**来表示代码块，不需要使用大括号 {} 。
缩进的空格数是可变的，但是同一个代码块的语句必须包含相同的缩进空格数。实例如下：
```python
if True:
    print ("True")
else:
    print ("False")
```
## 多行语句
Python 通常是一行写完一条语句，但如果语句很长，我们可以使用反斜杠 \来实现多行语句，例如：
```python
total = item_one + \
        item_two + \
        item_three
```
在 [], {}, 或 () 中的多行语句，不需要使用反斜杠 \，例如：
```python
total = ['item_one', 'item_two', 'item_three',
        'item_four', 'item_five']
```
## 数字类型
python中数字有四种类型：整数、布尔型、浮点数和复数。
- **int** (整数), 如 1, 只有一种整数类型 int，表示为长整型，没有 python2 中的 Long。
- **bool** (布尔), 如 True。
- **float** (浮点数), 如 1.23、3E-2
- **complex** (复数) - 复数由实部和虚部组成，形式为 a + bj，其中 a 是实部，b 是虚部，j 表示虚数单位。如 1 + 2j、 1.1 + 2.2j
## 字符串
- Python 中单引号 `'`和双引号` " `使用完全相同。
- 使用三引号(''' 或 """)可以指定一个多行字符串。
- 转义符 \。
- 反斜杠可以用来转义，使用 r 可以让反斜杠不发生转义。 如 **r"this is a line with \n"** 则 \n 会显示，并不是换行。
- 按字面意义级联字符串，如 **"this " "is " "string"** 会被自动转换为 **this is string**。
- 字符串可以用 + 运算符连接在一起，用 * 运算符重复。
- Python 中的字符串有两种索引方式，从左往右以 0 开始，从右往左以 -1 开始。
- Python 中的字符串不能改变。
- Python 没有单独的字符类型，一个字符就是长度为 1 的字符串。
- 字符串切片` str[start:end]`,其中 start（包含）是切片开始的索引，end（不包含）是切片结束的索引。
- 字符串的切片可以加上步长参数 step，语法格式如下：`str[start:end:step]`
```python
word = '字符串'
sentence = "这是一个句子。"
paragraph = """这是一个段落，
可以由多行组成"""
```
- 字符串内容前加`r`表示raw，即 raw string，会自动将反斜杠转义，例如：
```python
>>> print('\n')       # 输出空行

>>> print(r'\n')      # 输出 \n
\n
>>>
```
## 空行
函数之间或类的方法之间用空行分隔，表示一段新的代码的开始。类和函数入口之间也用一行空行分隔，以突出函数入口的开始。
空行与代码缩进不同，空行并不是 Python 语法的一部分。书写时不插入空行，Python 解释器运行也不会出错。但是空行的作用在于分隔两段不同功能或含义的代码，便于日后代码的维护或重构。
**记住：**空行也是程序代码的一部分。
## 等待用户输入

执行下面的程序在按回车键后就会等待用户输入：
```python
input("\n\n按下 enter 键后退出。")
```
以上代码中 ，`\n\n` 在结果输出前会输出两个新的空行。一旦用户按下**enter**键时，程序将退出。
## 同一行显示多条语句
Python 可以在同一行中使用多条语句，语句之间使用分号 ; 分割，以下是一个简单的实例：
```python
import sys; x = 'runoob'; sys.stdout.write(x + '\n')
```
使用脚本执行代码输出结果为`runoob`
使用交互式命令行执行，输出结果为：
`runoob`
`7`
此处的 7 表示字符数，**runoob** 有 6 个字符，**\n** 表示一个字符，加起来 **7** 个字符。
## 多语句构成代码组
缩进相同的一组语句构成一个代码块，我们称之代码组。
像`if、while、def和class`这样的复合语句，首行以关键字开始，以冒号( `:`)结束，该行之后的一行或多行代码构成代码组。
我们将首行及后面的代码组称为一个子句(clause)。
如下实例
```python
if expression : 
   suite
elif expression : 
   suite 
else : 
   suite
```
## print输出
**print** 默认输出是换行的，如果要实现不换行需要在变量末尾加上 `end=""`：
```python
x="a"
y="b"
# 换行输出
print( x )
print( y )
print('---------')
# 不换行输出
print( x, end=" " )
print( y, end=" " )
print()
```
执行结果为：
```
a
b
---------
a b
```
## import 与from.....import 导入模块

在 python 用` import `或者` from...import `来导入相应的模块。
将整个模块(somemodule)导入，格式为：` import somemodule`
从某个模块中导入某个函数,格式为：` from somemodule import somefunction`
从某个模块中导入多个函数,格式为：` from somemodule import firstfunc, secondfunc, thirdfunc`
将某个模块中的全部函数导入，格式为：` from somemodule import *`
### 导入sys模块
```python
import sys
print('================Python import mode==========================')
print ('命令行参数为:')
for i in sys.argv:
    print (i)
print ('\n python 路径为',sys.path)
```
### 导入sys模块的argv，path成员
```python
from sys import argv,path  #  导入特定的成员
 
print('================python from import===================================')
print('path:',path) # 因为已经导入path成员，所以此处引用时不需要加sys.path
```

## 命令行参数
很多程序可以执行一些操作来查看一些基本信息，Python可以使用-h参数查看各参数帮助信息：
```
$ python -h
usage: python [option] ... [-c cmd | -m mod | file | -] [arg] ...
Options and arguments (and corresponding environment variables):
-c cmd : program passed in as string (terminates option list)
-d     : debug output from parser (also PYTHONDEBUG=x)
-E     : ignore environment variables (such as PYTHONPATH)
-h     : print this help message and exit

[ etc. ]
```
***
