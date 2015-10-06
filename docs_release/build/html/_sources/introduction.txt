.. _tut-informal:

**********************************
Python 简介
**********************************

下面的例子中，输入和输出分别由大于号和句号提示符（ ``>>>`` 和 ``...`` ）标注：如果想重现这些例子，就要在解释器的提示符后，输入（提示符后面的）那些不包含提示符的代码行。需要注意的是在练习中遇到的从属提示符表示你需要在最后多输入一个空行，解释器才能知道这是一个多行命令的结束。 

本手册中的很多示例——包括那些带有交互提示符的——都含有注释。Python 中的注释以 ``#`` 字符起始，直至实际的行尾（译注——这里原作者用了 physical line 以表示实际的换行而非编辑器的自动换行）。注释可以从行首开始，也可以在空白或代码之后，但是不出现在字符串中。文本字符串中的 ``#`` 字符仅仅表示 ``#`` 。代码中的注释不会被 Python 解释，录入示例的时候可以忽略它们。 

如下示例::

   # this is the first comment
   spam = 1  # and this is the second comment
             # ... and now a third!
   text = "# This is not a comment because it's inside quotes."


.. _tut-calculator:

将 Python 当做计算器
============================

我们来尝试一些简单的 Python 命令。启动解释器然后等待主提示符 ``>>>`` 出现（不需要很久）。


.. _tut-numbers:

数字
-------

解释器表现得就像一个简单的计算器：可以向其录入一些表达式，它会给出返回值。表达式语法很直白：运算符 ``+``，``-``，``*`` 和 ``/`` 与其它语言一样(例如：Pascal 或 C)；括号 (``()``) 用于分组。例如::

   >>> 2 + 2
   4
   >>> 50 - 5*6
   20
   >>> (50 - 5*6) / 4
   5.0
   >>> 8 / 5  # division always returns a floating point number
   1.6

整数(例如，``2``， ``4``， ``20`` )的类型是 `int`_，带有小数部分的数字(例如，``5.0``， ``1.6``)的类型是 `float`_。在本教程的后面我们会看到更多关于数字类型的内容。

除法(``/``)永远返回一个浮点数。如要使用 `floor 除法`_ 并且得到整数结果（丢掉任何小数部分），你可以使用 ``//`` 运算符；要计算余数你可以使用 ``%`` ::

   >>> 17 / 3  # classic division returns a float
   5.666666666666667
   >>>
   >>> 17 // 3  # floor division discards the fractional part
   5
   >>> 17 % 3  # the % operator returns the remainder of the division
   2
   >>> 5 * 3 + 2  # result * divisor + remainder
   17

通过 Python，还可以使用 ``**`` 运算符计算幂乘方 [#]_::

   >>> 5 ** 2  # 5 squared
   25
   >>> 2 ** 7  # 2 to the power of 7
   128

等号( ``'='`` )用于给变量赋值。赋值之后，在下一个提示符之前不会有任何结果显示::

   >>> width = 20
   >>> height = 5*9
   >>> width * height
   900

变量在使用前必须 "定义"(赋值)，否则会出错::

   >>> # try to access an undefined variable
   ... n
   Traceback (most recent call last):
     File "<stdin>", line 1, in <module>
   NameError: name 'n' is not defined

浮点数有完整的支持；整数和浮点数的混合计算中，整数会被转换为浮点数::

   >>> 3 * 3.75 / 1.5
   7.5
   >>> 7.0 / 2
   3.5

交互模式中，最近一个表达式的值赋给变量 ``_``。这样我们就可以把它当作一个桌面计算器，很方便的用于连续计算，例如::

   >>> tax = 12.5 / 100
   >>> price = 100.50
   >>> price * tax
   12.5625
   >>> price + _
   113.0625
   >>> round(_, 2)
   113.06

此变量对于用户是只读的。不要尝试给它赋值 —— 你只会创建一个独立的同名局部变量，它屏蔽了系统内置变量的魔术效果。

除了 `int`_ 和 `float`_，Python 还支持其它数字类型，例如 `Decimal`_ 和 `Fraction`_。Python 还内建支持 `复数`_ ，使用后缀 ``j`` 或 ``J`` 表示虚数部分（例如，``3+5j``）。


.. _tut-strings:

字符串
-------

相比数值，Python 也提供了可以通过几种不同方式表示的字符串。它们可以用单引号 (``'...'``) 或双引号 (``"..."``)  标识 [#]_。``\`` 可以用来转义引号::

   >>> 'spam eggs'  # single quotes
   'spam eggs'
   >>> 'doesn\'t'  # use \' to escape the single quote...
   "doesn't"
   >>> "doesn't"  # ...or use double quotes instead
   "doesn't"
   >>> '"Yes," he said.'
   '"Yes," he said.'
   >>> "\"Yes,\" he said."
   '"Yes," he said.'
   >>> '"Isn\'t," she said.'
   '"Isn\'t," she said.'

在交互式解释器中，输出的字符串会用引号引起来，特殊字符会用反斜杠转义。虽然可能和输入看上去不太一样，但是两个字符串是相等的。如果字符串中只有单引号而没有双引号，就用双引号引用，否则用单引号引用。`print()`_ 函数生成可读性更好的输出, 它会省去引号并且打印出转义后的特殊字符：

   >>> '"Isn\'t," she said.'
   '"Isn\'t," she said.'
   >>> print('"Isn\'t," she said.')
   "Isn't," she said.
   >>> s = 'First line.\nSecond line.'  # \n means newline
   >>> s  # without print(), \n is included in the output
   'First line.\nSecond line.'
   >>> print(s)  # with print(), \n produces a new line
   First line.
   Second line.

如果你前面带有 ``\`` 的字符被当作特殊字符，你可以使用 *原始字符串*，方法是在第一个引号前面加上一个 ``r``::

   >>> print('C:\some\name')  # here \n means newline!
   C:\some
   ame
   >>> print(r'C:\some\name')  # note the r before the quote
   C:\some\name

字符串文本能够分成多行。一种方法是使用三引号：``"""..."""`` 或者 ``'''...'''``。行尾换行符会被自动包含到字符串中，但是可以在行尾加上 ``\`` 来避免这个行为。下面的示例：
可以使用反斜杠为行结尾的连续字符串，它表示下一行在逻辑上是本行的后续内容::

   print("""\
   Usage: thingy [OPTIONS]
        -h                        Display this usage message
        -H hostname               Hostname to connect to
   """)

将生成以下输出（注意，没有开始的第一行）:

.. code-block:: text

   Usage: thingy [OPTIONS]
        -h                        Display this usage message
        -H hostname               Hostname to connect to

字符串可以由 ``+`` 操作符连接(粘到一起)，可以由 ``*`` 表示重复::

   >>> # 3 times 'un', followed by 'ium'
   >>> 3 * 'un' + 'ium'
   'unununium'

相邻的两个字符串文本自动连接在一起。::

   >>> 'Py' 'thon'
   'Python'

它只用于两个字符串文本，不能用于字符串表达式::

   >>> prefix = 'Py'
   >>> prefix 'thon'  # can't concatenate a variable and a string literal
     ...
   SyntaxError: invalid syntax
   >>> ('un' * 3) 'ium'
     ...
   SyntaxError: invalid syntax

如果你想连接多个变量或者连接一个变量和一个字符串文本，使用 ``+``::

   >>> prefix + 'thon'
   'Python'

这个功能在你想切分很长的字符串的时候特别有用::

   >>> text = ('Put several strings within parentheses '
               'to have them joined together.')
   >>> text
   'Put several strings within parentheses to have them joined together.'

字符串也可以被截取(检索)。类似于 C ，字符串的第一个字符索引为 0 。Python没有单独的字符类型；一个字符就是一个简单的长度为1的字符串。::

   >>> word = 'Python'
   >>> word[0]  # character in position 0
   'P'
   >>> word[5]  # character in position 5
   'n'

索引也可以是负数，这将导致从右边开始计算。例如::

   >>> word[-1]  # last character
   'n'
   >>> word[-2]  # second-last character
   'o'
   >>> word[-6]
   'P'

请注意 -0 实际上就是 0，所以它不会导致从右边开始计算。

除了索引，还支持 *切片*。索引用于获得单个字符，*切片* 让你获得一个子字符串::

   >>> word[0:2]  # characters from position 0 (included) to 2 (excluded)
   'Py'
   >>> word[2:5]  # characters from position 2 (included) to 5 (excluded)
   'tho'

注意，包含起始的字符，不包含末尾的字符。这使得 ``s[:i] + s[i:]`` 永远等于 ``s``::

   >>> word[:2] + word[2:]
   'Python'
   >>> word[:4] + word[4:]
   'Python'

切片的索引有非常有用的默认值；省略的第一个索引默认为零，省略的第二个索引默认为切片的字符串的大小。::

   >>> word[:2]  # character from the beginning to position 2 (excluded)
   'Py'
   >>> word[4:]  # characters from position 4 (included) to the end
   'on'
   >>> word[-2:] # characters from the second-last (included) to the end
   'on'

有个办法可以很容易地记住切片的工作方式：切片时的索引是在两个字符 *之间* 。左边第一个字符的索引为 0，而长度为 *n*  的字符串其最后一个字符的右界索引为 *n*。例如::

    +---+---+---+---+---+---+
    | P | y | t | h | o | n |
    +---+---+---+---+---+---+
    0   1   2   3   4   5   6
   -6  -5  -4  -3  -2  -1

文本中的第一行数字给出字符串中的索引点 0...6。第二行给出相应的负索引。切片是从 *i* 到 *j* 两个数值标示的边界之间的所有字符。 

对于非负索引，如果上下都在边界内，切片长度就是两个索引之差。例如，``word[1:3]`` 是 2 。 

试图使用太大的索引会导致错误::

   >>> word[42]  # the word only has 6 characters
   Traceback (most recent call last):
     File "<stdin>", line 1, in <module>
   IndexError: string index out of range

Python 能够优雅地处理那些没有意义的切片索引：一个过大的索引值(即下标值大于字符串实际长度)将被字符串实际长度所代替，当上边界比下边界大时(即切片左值大于右值)就返回空字符串::

   >>> word[4:42]
   'on'
   >>> word[42:]
   ''

Python字符串不可以被更改 --- 它们是 `不可变的`_ 。因此，赋值给字符串索引的位置会导致错误::

   >>> word[0] = 'J'
     ...
   TypeError: 'str' object does not support item assignment
   >>> word[2:] = 'py'
     ...
   TypeError: 'str' object does not support item assignment

如果你需要一个不同的字符串，你应该创建一个新的::

   >>> 'J' + word[1:]
   'Jython'
   >>> word[:2] + 'py'
   'Pypy'

内置函数 `len()`_ 返回字符串长度::

   >>> s = 'supercalifragilisticexpialidocious'
   >>> len(s)
   34


.. seealso::

   `Text Sequence Type — str`_
      字符串是 **序列类型** 的例子，它们支持这种类型共同的操作。

   `String Methods`_
      字符串和Unicode字符串都支持大量的方法用于基本的转换和查找。

   `String Formatting`_
      这里描述了使用 `str.format()`_ 进行字符串格式化的信息。

   `String Formatting Operations`_
      这里描述了旧式的字符串格式化操作，它们在字符串和Unicode字符串是 ``%`` 操作符的左操作数时调用。


.. _tut-lists:

列表
-----

Python 有几个 *复合* 数据类型，用于表示其它的值。最通用的是 *list* (列表) ，它可以写作中括号之间的一列逗号分隔的值。列表的元素不必是同一类型::

   >>> squares = [1, 4, 9, 16, 25]
   >>> squares
   [1, 4, 9, 16, 25]

就像字符串(以及其它所有内建的 `序列`_ 类型)一样，列表可以被索引和切片::

   >>> squares[0]  # indexing returns the item
   1
   >>> squares[-1]
   25
   >>> squares[-3:]  # slicing returns a new list
   [9, 16, 25]

所有的切片操作都会返回一个包含请求的元素的新列表。这意味着下面的切片操作返回列表一个新的（浅）拷贝副本::

   >>> squares[:]
   [1, 4, 9, 16, 25]

列表也支持连接这样的操作::

   >>> squares + [36, 49, 64, 81, 100]
   [1, 4, 9, 16, 25, 36, 49, 64, 81, 100]

不像 *不可变的* 字符串，列表是 *可变的*，它允许修改元素::

    >>> cubes = [1, 8, 27, 65, 125]  # something's wrong here
    >>> 4 ** 3  # the cube of 4 is 64, not 65!
    64
    >>> cubes[3] = 64  # replace the wrong value
    >>> cubes
    [1, 8, 27, 64, 125]

你还可以使用 ``append()`` *方法* （后面我们会看到更多关于列表的方法的内容）在列表的末尾添加新的元素::

   >>> cubes.append(216)  # add the cube of 6
   >>> cubes.append(7 ** 3)  # and the cube of 7
   >>> cubes
   [1, 8, 27, 64, 125, 216, 343]

也可以对切片赋值，此操作可以改变列表的尺寸，或清空它::

   >>> letters = ['a', 'b', 'c', 'd', 'e', 'f', 'g']
   >>> letters
   ['a', 'b', 'c', 'd', 'e', 'f', 'g']
   >>> # replace some values
   >>> letters[2:5] = ['C', 'D', 'E']
   >>> letters
   ['a', 'b', 'C', 'D', 'E', 'f', 'g']
   >>> # now remove them
   >>> letters[2:5] = []
   >>> letters
   ['a', 'b', 'f', 'g']
   >>> # clear the list by replacing all the elements with an empty list
   >>> letters[:] = []
   >>> letters
   []

内置函数 `len()`_ 同样适用于列表::

   >>> letters = ['a', 'b', 'c', 'd']
   >>> len(letters)
   4

允许嵌套列表(创建一个包含其它列表的列表)，例如::

   >>> a = ['a', 'b', 'c']
   >>> n = [1, 2, 3]
   >>> x = [a, n]
   >>> x
   [['a', 'b', 'c'], [1, 2, 3]]
   >>> x[0]
   ['a', 'b', 'c']
   >>> x[0][1]
   'b'


.. _tut-firststeps:

编程的第一步
===============================

当然，我们可以使用 Python 完成比二加二更复杂的任务。例如，我们可以写一个生成 *菲波那契* 子序列的程序，如下所示::

   >>> # Fibonacci series:
   ... # the sum of two elements defines the next
   ... a, b = 0, 1
   >>> while b < 10:
   ...     print(b)
   ...     a, b = b, a+b
   ...
   1
   1
   2
   3
   5
   8

这个例子介绍了几个新功能。

* 第一行包括了一个 *多重赋值*：变量 ``a`` 和 ``b`` 同时获得了新的值 0 和 1 最后一行又使用了一次。
  
  在这个演示中，变量赋值前，右边首先完成计算。右边的表达式从左到右计算。

* 条件（这里是 ``b < 10`` ）为 true 时， `while`_ 循环执行。在 Python 中，类似于 C，任何非零整数都是 true；0 是 false 条件也可以是字符串或列表，实际上可以是任何序列；
  
  所有长度不为零的是 true，空序列是 false。示例中的测试是一个简单的比较。标准比较操作符与 C 相同： ``<`` ， ``>`` ， ``==`` ， ``<=``， ``>=`` 和 ``!=``。

* 循环 *体* 是 *缩进* 的：缩进是 Python 是 Python 组织語句的方法。Python (还)不提供集成的行编辑功能，所以你要为每一个缩进行输入 TAB 或空格。
  
  实践中建议你找个文本编辑来录入复杂的 Python 程序，大多数文本编辑器提供自动缩进。交互式录入复合语句时，必须在最后输入一个空行来标识结束（因为解释器没办法猜测你输入的哪一行是最后一行），需要 注意的是同一个语句块中的语句块必须缩进同样数量的空白。

* 关键字 `print()`_ 语句输出给定表达式的值。它控制多个表达式和字符串输出为你想要字符串（就像我们在前面计算器的例子中那样）。
  
  字符串打印时不用引号包围，每两个子项之间插入空间，所以你可以把格式弄得很漂亮，像这样::

     >>> i = 256*256
     >>> print('The value of i is', i)
     The value of i is 65536

  用一个逗号结尾就可以禁止输出换行::

     >>> a, b = 0, 1
     >>> while b < 1000:
     ...     print(b, end=',')
     ...     a, b = b, a+b
     ...
     1,1,2,3,5,8,13,21,34,55,89,144,233,377,610,987,


.. rubric:: Footnotes

.. [#] 因为 ``**`` 的优先级高于 ``-``，所以 ``-3**2`` 将解释为 ``-(3**2)`` 且结果为 ``-9``。为了避免这点并得到 ``9``，你可以使用 ``(-3)**2``。

.. [#] 与其它语言不同，特殊字符例如 ``\n`` 在单引号(``'...'``)和双引号(``"..."``)中具有相同的含义。两者唯一的区别是在单引号中，你不需要转义 ``"`` （但你必须转义 ``\'`` )，反之亦然。


.. _int: https://docs.python.org/3/library/functions.html#int
.. _float: https://docs.python.org/3/library/functions.html#float
.. _floor 除法: https://docs.python.org/3/glossary.html#term-floor-division
.. _Decimal: https://docs.python.org/3/library/decimal.html#decimal.Decimal
.. _Fraction: https://docs.python.org/3/library/fractions.html#fractions.Fraction
.. _复数: https://docs.python.org/3/library/stdtypes.html#typesnumeric
.. _print(): https://docs.python.org/3/library/functions.html#print
.. _不可变的: https://docs.python.org/3/glossary.html#term-immutable
.. _len(): https://docs.python.org/3/library/functions.html#len
.. _Text Sequence Type — str: https://docs.python.org/3/library/stdtypes.html#textseq
.. _String Methods: https://docs.python.org/3/library/stdtypes.html#string-methods
.. _String Formatting: https://docs.python.org/3/library/string.html#string-formatting
.. _String Formatting Operations: https://docs.python.org/3/library/stdtypes.html#old-string-formatting
.. _str.format(): https://docs.python.org/3/library/stdtypes.html#str.format
.. _序列: https://docs.python.org/3/glossary.html#term-sequence
.. _可变的: https://docs.python.org/3/glossary.html#term-mutable
.. _while: https://docs.python.org/3/reference/compound_stmts.html#while


