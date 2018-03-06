title: 'Pythonhttp://localhost:4000/admin/#/'
id: 1517817251663
author: 不识
tags:
  - python
categories:
  - python
date: 2018-02-05 15:54:00
---
***
# 概览
***
<!-- more -->

***
# 数据类型
***

***
# 流程控制语句
***

***
# 数据结构
***

##  List
***
List是使用方括号（[]）括起来，并使用逗号分隔的元素集，List可以包含不同数据类型的元素，但通常元素都是相同类型的
```python
>>> squares = [1, 4, 9, 16, 25]
>>> squares
[1, 4, 9, 16, 25]
```
同string一样（包括所有的内置sequence类型），list也可以被索引（index）和切片（slice），所有的切片操作都会返回一个包含所请求项的新的list：
```python
>>> squares[0]  # 索引返回项
1
>>> squares[-1]
25
>>> squares[-3:]  # 切片返回一个新的list
[9, 16, 25]
>>> squares[:]
[1, 4, 9, 16, 25]
```
list还支持拼接等操作：
```python
>>> squares + [36, 49, 64, 81, 100]
[1, 4, 9, 16, 25, 36, 49, 64, 81, 100]
```
**不像string是一个不可变类型，list是一个可变类型**，也就是list的内容可以更改。此时对切​​片的重新赋值也是可能的，这甚至可以改变list的大小或完全清除它：
```python
>>> letters = ['a', 'b', 'c', 'd', 'e', 'f', 'g']
>>> letters
['a', 'b', 'c', 'd', 'e', 'f', 'g']
>>> # 替换一些值
>>> letters[2:5] = ['C', 'D', 'E']
>>> letters
['a', 'b', 'C', 'D', 'E', 'f', 'g']
>>> # 现在移除他们
>>> letters[2:5] = []
>>> letters
['a', 'b', 'f', 'g']
>>> # 通过使用一个空list替换掉所有元素来清空list
>>> letters[:] = []
>>> letters
[]
```

### list方法

|方法名|描述|
|----------|-------|
|**append(x)**|再list尾部添加一个元素，等同 a[len(a):] = [x]|
|**extend(iterable)**|通过附加iterable中的所有项来扩展list。等同于[len(a):] =iterable。|
|**insert(i,x)**|在给定位置插入一个元素|
|**remove(x)**|从list中删除值为x的第一个元素。如果没有这样的元素，会抛出错误。|
|**pop([i])**|删除list中给定位置的元素，并返回它。如果没有指定索引，则a.pop()将弹出list中的最后一个元素。|
|**clear()**|从list中删除所有元素。相当于del a [:]。|
|**index(x[,start[,end]])**|返回list中第一个值为x的元素的基于0的索引。如果没有这样的元素会抛出一个ValueError|
|**count(x)**|返回x出现在list中的次数。|
|**sort(key=None,reverse=False)**|对list中的元素进行排序（参数可用于自定义排序，请参阅sorted（）以获取其解释）。|
|**reverse(x)**|反转list中的元素。|
|**copy(x)**|返回list的浅拷贝。等同于a[:]|

### list推导
list推导提供了一种简明的创建list的方式。通常应用创建一个新的list，该list中每个元素都是应用于另一个序列的每个成员或迭代的某些操作的结果，或者创建满足特定条件的那些元素的子序列。
例如，假设我们创建一个list
```python
>>> squares = []
>>> for x in range(10):
...     squares.append(x**2)
...
>>> squares
[0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
```
注意，这样创建（或覆盖）一个名为x的变量在循环结束后依然存在，我们可以使用以下方法计算无任何副作用的list：
```python
squares = list(map(lambda x: x**2, range(10)))
```
或者等同于
```python
squares = [x**2 for x in range(10)]
```
这样更加简明易读。
list推导由一个方括号组成，括号中包含一个表达式，后跟一个for子句，然后零或更多的for或if子句。结果将成为一个新的list，通过评估后面的for和if子句的内容中产生。例如下面的list推导组合了两个list不相同的元素：
```python 
>>> [(x, y) for x in [1,2,3] for y in [3,1,4] if x != y]
[(1, 3), (1, 4), (2, 3), (2, 1), (2, 4), (3, 1), (3, 4)]
```
它等同于
```python
>>> combs = []
>>> for x in [1,2,3]:
...     for y in [3,1,4]:
...         if x != y:
...             combs.append((x, y))
...
>>> combs
[(1, 3), (1, 4), (2, 3), (2, 1), (2, 4), (3, 1), (3, 4)]
```
请注意在这两个代码片段中for和if语句的顺序是相同的。
如果表达式是一个元组（例如前面例子中的（x，y）），它必须加上括号。
```python
>>> vec = [-4, -2, 0, 2, 4]
>>> # 使用值*2创建新的list
>>> [x*2 for x in vec]
[-8, -4, 0, 4, 8]
>>> # 过滤list排除掉负数
>>> [x for x in vec if x >= 0]
[0, 2, 4]
>>> # 对每个元素应用函数
>>> [abs(x) for x in vec]
[4, 2, 0, 2, 4]
>>> # 在每个元素上调用方法
>>> freshfruit = ['  banana', '  loganberry ', 'passion fruit  ']
>>> [weapon.strip() for weapon in freshfruit]
['banana', 'loganberry', 'passion fruit']
>>> # 创建一个双元组的列表，如（数字，平方）
>>> [(x, x**2) for x in range(6)]
[(0, 0), (1, 1), (2, 4), (3, 9), (4, 16), (5, 25)]
>>> # 元组必须加括号，否则会引发错误
>>> [x, x**2 for x in range(6)]
  File "<stdin>", line 1, in <module>
    [x, x**2 for x in range(6)]
               ^
SyntaxError: invalid syntax
>>> # 使用两个'for'使用listcomp展开list
>>> vec = [[1,2,3], [4,5,6], [7,8,9]]
>>> [num for elem in vec for num in elem]
[1, 2, 3, 4, 5, 6, 7, 8, 9]
```
### 嵌套list推导
list推导中的初始表达式可以是任意的表达式，包括另一个list推导。
考虑以下3个4长度为4的list的实例：
```python
>>> matrix = [
...     [1, 2, 3, 4],
...     [5, 6, 7, 8],
...     [9, 10, 11, 12],
... ]
```
以下list推导将转置行和列：
```python
>>> [[row[i] for row in matrix] for i in range(4)]
[[1, 5, 9], [2, 6, 10], [3, 7, 11], [4, 8, 12]]
```
正如我们在前面的章节中看到的那样，嵌套的listcomp是在它后面的for的上下文中计算的，所以这个例子相当于：
```python
>>> transposed = []
>>> for i in range(4):
...     transposed.append([row[i] for row in matrix])
...
>>> transposed
[[1, 5, 9], [2, 6, 10], [3, 7, 11], [4, 8, 12]]
```
这反过来又是一样的：
```python
>>> transposed = []
>>> for i in range(4):
...     # 下面三行实现了嵌套的listcomp
...     transposed_row = []
...     for row in matrix:
...         transposed_row.append(row[i])
...     transposed.append(transposed_row)
...
>>> transposed
[[1, 5, 9], [2, 6, 10], [3, 7, 11], [4, 8, 12]]
```
在实际工作中，你应该更喜欢内置函数来处理复杂的流程语句。对于这个用例，**zip()**函数可以做很好的工作：
```python
>>> list(zip(*matrix))
[(1, 5, 9), (2, 6, 10), (3, 7, 11), (4, 8, 12)]
```
## del语句
***

## 元组和序列
***
我们看到列表和字符串有许多共同的属性，如索引和切片操作。它们是**序列（sequence）**数据类型的两个示例（请参见序列类型 - 列表，元组，范围）。由于Python是一种不断发展的语言，因此可能会添加其他序列数据类型。还有另一种标准的序列数据类型：**元组（tuple）**。
元组由多个用逗号分隔的值组成，例如：
```python
>>> t = 12345, 54321, 'hello!'
>>> t[0]
12345
>>> t
(12345, 54321, 'hello!')
>>> # 元组可能是嵌套的:
... u = t, (1, 2, 3, 4, 5)
>>> u
((12345, 54321, 'hello!'), (1, 2, 3, 4, 5))
>>> # 元组是不可变的:
... t[0] = 88888
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: 'tuple' object does not support item assignment
>>> # 但是它可以包含可变对象:
... v = ([1, 2, 3], [3, 2, 1])
>>> v
([1, 2, 3], [3, 2, 1])
```
正如你所看到的，在输出中，元组总是被包含在圆括号中，以便嵌套元组被正确解释;它们可能输入时带有或不带括号，尽管通常括号是必要的（如果元组是更大表达式的一部分）。无法给元组的单个项赋值，但可以创建一个包含可变对象（如list）的元组。

## Set
***

***
# 模块
***
python可以将一些函数定义在一个文件中，以便在脚本或解释器中调用，这样的文件python称为**模块**(module)。模块中的定义可以被导入到其他模块或者主模块中。

模块(module)是一个包含Python定义和语句的文件。文件名位模块名加上 **.py** 后缀。在模块中，模块名（一个字符串）可以通过全局变量__name__获取。我们可以创建一个模块文件*fibo.py*，
```python
# Fibonacci numbers module

def fib(n):    # write Fibonacci series up to n
    a, b = 0, 1
    while b < n:
        print(b, end=' ')
        a, b = b, a+b
    print()

def fib2(n):   # return Fibonacci series up to n
    result = []
    a, b = 0, 1
    while b < n:
        result.append(b)
        a, b = b, a+b
    return result
```

要想在解释器或其他模块中使用fibo模块中的定义，需要先使用如下语句导入该模块。
```python
import fibo
```
之后就可以通过模块名fibo引用该模块中的定义。
## 模块
***
模块中也可以包含像在函数中一样的可执行语句，不过这些语句一般用来做初始化模块的功能。模块中定义的语句，只会在模块被import时执行一次。
每个模块都有自己私有的符号表（symbol table），它被该模块定义的所有函数用作全局符号表。因此在一个模块中可以使用该模块全局变量，而不用担心与其他模块全局变量冲突。

模块可以导入其他模块，习惯但不强制要求需要把**import**语句放在模块的开始位置。被导入的模块名称会被存在进行导入的模块的全局变量中。

**import**语句还有一种变体
```python
>>> from fibo import fib, fib2
>>> fib(500)
1 1 2 3 5 8 13 21 34 55 89 144 233 377
```
这种写法不会将模块名称引入执行import模块的局部符号表中（如这个例子中，fibo是未定义的）

还可以使用通配符将模块中所有定义名称导入：
```python 
>>> from fibo import *
>>> fib(500)
1 1 2 3 5 8 13 21 34 55 89 144 233 377
```
这种导入会将模块中除了以下划线（ _ ）开头的之外所有的名称导入。多数情况下开发人员不会使用这种方式，因为它容易隐藏自己已定义的名称，导致糟糕的代码易读性。

> **Note**:基于效率考虑，每个模块在每次解释器会话中只会导入一次。因此，如果你更改了模块，则必须重启解释器，或者只有一个测试交互的模块的话，可以使用  importlib.reload(), 也就是 
import importlib; 
importlib.reload(modulename).

### 将模块作为脚本执行

当如下运行Python模块时
```python
python fibo.py <arguments>
```
就如同导入该模块一样，模块中的代码都会执行，除了模块的"\_\_name\_\_"会被设置为"\_\_main\_\_",这意味着你可以在模块中加入如下代码
```python
if __name__ == "__main__":
    import sys
    fib(int(sys.argv[1]))
```
这样你可以使该文件就如一个可导入模块一样作为脚本使用，因为这些代码只会在该模块作为"main"文件执行时解析命令行。
```python
$ python fibo.py 50
1 1 2 3 5 8 13 21 34
```
如果导入该模块，这些代码则不会执行。这种写法通常用来提供一个便捷的用户接口，或者用于测试目的（将模块作为脚本运行以执行单元测试）。

### 模块搜索路径
当一个名为**spam**的模块被导入，解释器首先搜索该名称的内置模块。如果没有找到，则在变量**sys.path**给定的目录列表中搜索名为**spam.py**的文件。**sys.path**从以下位置初始化：
- 包含输入脚本的目录（或者当没有文件制定时为当前目录）
- PYTHONPATH(一个目录名称列表，它与shell变量PATH有着相同的语义)
- 默认安装目录

初始化之后，Python程序可以修改sys.path。运行脚本所在的目录在搜索路径开头，位于标准库路径之前。这意味着如果标准库目录下有与该目录下的相同名字的脚本，实际执行的是该目录下脚本。

### "编译"Python文件
为了加速模块加载，Python在**\_\_pycache\_\_**目录下缓存每个模块的编译版本，并存储在名为**module.version.pyc**文件中。其中version对文件编码，他通常包含Python的版本号。这个命名约定允许来自不同版本和不同版本的Python的编译模块共存。
Python根据编译后的版本检查源代码的修改日期，看它是否过期并需要重新编译。这是一个完全自动的过程。另外，编译后的模块是独立于平台的，因此可以在不同架构的系统之间共享相同的库。
在两种情况下，Python不会检查缓存。第一种，从命令行直接加载的模块总是重新编译，并且不存储模块的结果。第二种，如果没有源模块，则不会检查缓存。要支持非源代码（仅编译）分发，编译的模块必须位于源代码目录中，并且不得有源模块。

## 标准模块
***
Python带有一个标准模块库，在一个单独的文档中描述，即Python库参考 Python Library Reference （以下简称“库参考”）。一些模块是内置在解释器中的；它们提供了一些访问不属于该语言核心部分的操作，比如无论是为了提高效率还是对操作系统原函数的访问操作。这些模块的集是一个配置选项，它也取决于底层平台。例如，**winreg** 模块仅在Windows系统上提供。一个特定的模块**sys**值得注意：它被内置进了每个Python解释器。变量**sys.ps1** 和**sys.ps2** 定义用作主要和次要提示的字符串：
```python
>>> import sys
>>> sys.ps1
'>>> '
>>> sys.ps2
'... '
>>> sys.ps1 = 'C> '
C> print('Yuck!')
Yuck!
C>
```
这两个变量只在解释器处于交互模式时才被定义。
变量**sys.path**是一个字符串列表，它决定了解释器的模块搜索路径。它被初始化为从环境变量**PYTHONPATH**获取的默认路径，或者如果未设置**PYTHONPATH**则从内置默认路径初始化。你可以使用标准的list操作修改它
```python
>>> import sys
>>> sys.path.append('/ufs/guido/lib/python')
```

## 包
***
**包（Package）**是通过使用“点分割模块名称”来构造Python模块命名空间的一种方式。例如，模块名称A.B在名为A的包中指定名为B的子模块。假设你想设计一个模块集（一个“包”）来统一处理声音文件和声音数据。一下是可能的包结构：
```
sound/                          顶层的包
      __init__.py              初始化sound包
      formats/                  用于文件格式转换的子包
              __init__.py
              wavread.py
              wavwrite.py
              aiffread.py
              aiffwrite.py
              auread.py
              auwrite.py
              ...
      effects/                  用作声音效果的子包
              __init__.py
              echo.py
              surround.py
              reverse.py
              ...
      filters/                  用作过滤器的子包装
              __init__.py
              equalizer.py
              vocoder.py
              karaoke.py
              ...
```
当导入包时，Python将搜索**sys.path**上的目录，查找包子目录。
如果想要Python将一个目标视为包，则该目录下必须有**__init__.py**文件。这是为了防止具有通用名称（例如String）的目录无意中隐藏稍后在模块搜索路径中查找到的有效模块。在最简单的情况下，**\_\_init\_\_.py**可以只是一个空文件，但它也可以执行包的初始化代码或设置**\_\_all\_\_**变量。
包的使用者可以从包中导入单个模块，例如：
```python
import sound.effects.echo
```
这会加载子模块**sound.effects.echo**。它必须以全名引用。
```python
sound.effects.echo.echofilter(input, output, delay=0.7, atten=4)
```
导入子模块的另一种方法是：
```python
from sound.effects import echo
```
这也加载了子模块echo，并让它可以在不包含它的包前缀的情况下可用，因此可以如下使用它：
```python
echo.echofilter(input, output, delay=0.7, atten=4)
```
另一种变体是直接导入所需的函数或变量：
```python
from sound.effects.echo import echofilter
```
再次，这加载了子模块echo，但它的函数echofilter()可以直接使用：
```python
echofilter(input, output, delay=0.7, atten=4)
```
请注意，当使用**from package import item**，item可以是包的子模块（或子包），也可以是包中定义的其他名称，如函数，类或变量。**import**语句首先测试item是否定义在包中;如果不是，它假定它是一个模块并尝试加载它。如果找不到它，则会引发**ImportError**异常。

### import * from package
import语句使用以下约定：如果一个包的**\_\_init\_\_.py**代码定义了一个名为**\_\_all\_\_**的list，它被认为是执行from package import \*  时应该导入的模块名称的列表。如果未定义**\_\_all\_\_**，则该语句不会导入该包下的任何模块到当前命名空间，它只确保包本身（可能在**\_\_init\_\_.py**文件中的初始化代码）被导入，然后导入包中定义的任何名称。这包括**\_\_init\_\_.py**定义的任何名称（以及明确加载的子模块）。它还包括由前面的**import**语句显式加载的包的任何子模块。考虑如下代码：
```python
import sound.effects.echo
import sound.effects.surround
from sound.effects import *
```
echo和surround模块等同于使用from...import导入形式。

### 多目录中的包
包还支持一个更特殊的属性**\_\_path\_\_**。在执行该文件中的代码之前，它被初始化为一个包含了所有包含**\_\_init\_\_.py**文件的目录名称的列表。这个变量可以修改;但这样做会影响将来对包中包含的模块和子包的搜索。

虽然通常不需要此功能，但它可用于扩展程序包中找到的一组模块。
***
# 错误与异常
***

***
# 类
***
## 类定义
***

## 类成员
***

## 继承
***