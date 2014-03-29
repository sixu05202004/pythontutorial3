.. _tut-informal:

**********************************
Python 简介
**********************************

下面的例子中，输入和输出分别由大于号和句号提示符（ ``>>>`` 和 ``...`` ）标注：如果想重现这些例子，就要在解释器的提示符后，输入（提示符后面的）那些不包含提示符的代码行。需要注意的是在练习中遇到的从属提示符表示你需要在最后多输入一个空行，解释器才能知道这是一个多行命令的结束。 

本手册中的很多示例——包括那些带有交互提示符的——都含有注释。Python 中的注释以 ``#`` 字符起始，直至实际的行尾（译注——这里原作者用了 physical line 以表示实际的换行而非编辑器的自动换行）。注释可以从行首开始，也可以在空白或代码之后，但是不出现在字符串中。文本字符串中的 ``#`` 字符仅仅表示 ``#`` 。代码中的注释不会被 Python 解释，录入示例的时候可以忽略它们。 

如下示例::

   # this is the first comment
   SPAM = 1                 # and this is the second comment
                            # ... and now a third!
   STRING = "# This is not a comment."


.. _tut-calculator:

将 Python 当做计算器
============================

我们来尝试一些简单的 Python 命令。启动解释器然后等待主提示符 ``>>>`` 出现。（不需要很久。）


.. _tut-numbers:

数字
-------

解释器的表示就像一个简单的计算器：可以向其录入一些表达式，它会给出返回值。表达式语法很直白：运算符 ``+`` ， ``-`` ， ``*`` 和 ``/`` 与其它语言一样（例如： Pascal 或 C）；括号用于分组。例如::

   >>> 2+2
   4
   >>> # This is a comment
   ... 2+2
   4
   >>> 2+2  # and a comment on the same line as code
   4
   >>> (50-5*6)/4
   5.0
   >>> 8/5 # Fractions aren't lost when dividing integers
   1.6

注意：有时你可能会得到不同的结果；浮点数在不同机器上的运算结果可能是不同的。 后面我们将对控制浮点数输出的显示结果做更多说明；这里我们看到的仅是有效的显示，并非我们能得到的可读性更好的结果。

对整数做除法运算并想去除小数部分取得整数结果时，可以使用另外一个运算符， ``//``::

   >>> # Integer division returns the floor:
   ... 7//3
   2
   >>> 7//-3
   -3

等号（ ``'='`` ）用于给变量赋值::

   >>> width = 20
   >>> height = 5*9
   >>> width * height
   900

一个值可以同时赋给几个变量::

   >>> x = y = z = 0  # Zero x, y and z
   >>> x
   0
   >>> y
   0
   >>> z
   0

变量在使用前必须“定义”（赋值），否则会出错::

   >>> # try to access an undefined variable
   ... n
   Traceback (most recent call last):
     File "<stdin>", line 1, in <module>
   NameError: name 'n' is not defined

浮点数有完整的支持；与整型混合计算时会自动转为浮点数::

   >>> 3 * 3.75 / 1.5
   7.5
   >>> 7.0 / 2
   3.5

复数也得到支持；带有后缀 ``j`` 或 ``J`` 就被视为虚数。带有非零实部的复数写为 ``(real+imagj)`` ，或者可以用 ``complex(real, imag)`` 函数创建。
::

   >>> 1j * 1J
   (-1+0j)
   >>> 1j * complex(0, 1)
   (-1+0j)
   >>> 3+1j*3
   (3+3j)
   >>> (3+1j)*3
   (9+3j)
   >>> (1+2j)/(1+1j)
   (1.5+0.5j)

复数的实部和虚部总是记为两个浮点数。要从复数 z 中提取实部和虚部，使用 ``z.real`` 和 ``z.imag`` 。   ::

   >>> a=1.5+0.5j
   >>> a.real
   1.5
   >>> a.imag
   0.5

浮点数和整数之间的转换函数（ :func:`float` 和 :func:`int` 以及 :func:`long` ） 不能用于复数。没有什么正确方法可以把一个复数转成一个实数。函数 ``abs(z)`` 用于获取其模（浮点数）或 ``z.real``  获取其实部::

   >>> a=3.0+4.0j
   >>> float(a)
   Traceback (most recent call last):
     File "<stdin>", line 1, in ?
   TypeError: can't convert complex to float; use abs(z)
   >>> a.real
   3.0
   >>> a.imag
   4.0
   >>> abs(a)  # sqrt(a.real**2 + a.imag**2)
   5.0

交互模式中，最近一个表达式的值赋给变量 ``_`` 。这样我们就可以把它当作一个桌面计算器，很方便的用于连续计算，例如::

   >>> tax = 12.5 / 100
   >>> price = 100.50
   >>> price * tax
   12.5625
   >>> price + _
   113.0625
   >>> round(_, 2)
   113.06

此变量对于用户是只读的。不要尝试给它赋值 —— 你只会创建一个独立的同名局部变量，它屏蔽了系统内置变量的魔术效果.


.. _tut-strings:

字符串
-------

相比数值，Python 也提供了可以通过几种不同方式传递的字符串。它们可以用单引号或双引号标识::

   >>> 'spam eggs'
   'spam eggs'
   >>> 'doesn\'t'
   "doesn't"
   >>> "doesn't"
   "doesn't"
   >>> '"Yes," he said.'
   '"Yes," he said.'
   >>> "\"Yes,\" he said."
   '"Yes," he said.'
   >>> '"Isn\'t," she said.'
   '"Isn\'t," she said.'

Python 解释器按照字符串被输入的方式打印字符串结果：为了显示准确的值，字符串包含在成对的引号中，引号和其他特殊字符要用反斜线（ \\ ）转译。 如果字符串只包含单引号（ ' ）而没有双引号（ " ）就可以用双引号（ " ）包围，反之用单引号（ ' ）包围。 再强调一下， :keyword:`print` 函数可以生成可读性更好的输出。

字符串文本有几种方法分行。可以使用反斜杠为行结尾的连续字符串，它表示下一行在逻辑上是本行的后续内容::

   hello = "This is a rather long string containing\n\
   several lines of text just as you would do in C.\n\
       Note that whitespace at the beginning of the line is\
    significant."

   print(hello)

<<<<<<< HEAD:source/introduction.rst
需要注意地是，还是需要在字符串中写入 n ——结尾的反斜杠会被忽略。前例会打印为如下形式:
=======
需要注意的是，还是需要在字符串中写入 ``\n`` ——结尾的反斜杠会被忽略。前例会打印为如下形式:
>>>>>>> FETCH_HEAD:source/introduction.rst

.. code-block:: text

   This is a rather long string containing
   several lines of text just as you would do in C.
       Note that whitespace at the beginning of the line is significant.

另外，字符串可以标识在一对儿三引号中： ``"""`` 或 ``'''`` 。三引号中，不需要行属转义，它们已经包含在字符串中::

   print("""\
   Usage: thingy [OPTIONS]
        -h                        Display this usage message
        -H hostname               Hostname to connect to
   """)

得到如下输出:

.. code-block:: text

   Usage: thingy [OPTIONS]
        -h                        Display this usage message
        -H hostname               Hostname to connect to

如果我们生成一个“原始”字符串， ``\n`` 序列不会被转义，而且行尾的反斜杠，源码中的换行符，都成为字符串中的一部分数据，因此下例::

   hello = r"This is a rather long string containing\n\
   several lines of text much as you would do in C."

   print(hello)

会打印:

.. code-block:: text

   This is a rather long string containing\n\
   several lines of text much as you would do in C.

字符串可以由 ``+`` 操作符连接（粘到一起），可以由 ``*`` 重复::

   >>> word = 'Help' + 'A'
   >>> word
   'HelpA'
   >>> '<' + word*5 + '>'
   '<HelpAHelpAHelpAHelpAHelpA>'

<<<<<<< HEAD:source/introduction.rst
相邻的两个字符串文本自动连接在一起，前面那行代码也可以写为 ``word ='Help' 'A'`` ;它只用于两个字符串文本，不能用于字符串表达式::
=======
相邻的两个字符串文本自动连接在一起，前面那行代码也可以写为 ``word ='Help' 'A'``；它只用于两个字符串文本，不能用于字符串表达式::
>>>>>>> FETCH_HEAD:source/introduction.rst

   >>> 'str' 'ing'                   #  <-  This is ok
   'string'
   >>> 'str'.strip() + 'ing'   #  <-  This is ok
   'string'
   >>> 'str'.strip() 'ing'     #  <-  This is invalid
     File "<stdin>", line 1, in ?
       'str'.strip() 'ing'
                         ^
   SyntaxError: invalid syntax

字符串也可以被截取（检索）。类似于 C ，字符串的第一个字符索引为 0 。没有独立的字符类型，字符就是长度为 1 的字符串。类似 Icon ，可以用 *切片标注* 法截取字符串：由两个索引分割的复本。
::

   >>> word[4]
   'A'
   >>> word[0:2]
   'He'
   >>> word[2:4]
   'lp'

索引切片可以有默认值，切片时，忽略第一个索引的话，默认为0，忽略第二个索引，默认为字符串的长度::

   >>> word[:2]    # The first two characters
   'He'
   >>> word[2:]    # Everything except the first two characters
   'lpA'

不同于 C 字符串，Python 字符串不可变。向字符串文本的某一个索引赋值会引发错误::

   >>> word[0] = 'x'
   Traceback (most recent call last):
     File "<stdin>", line 1, in ?
   TypeError: 'str' object does not support item assignment
   >>> word[:1] = 'Splat'
   Traceback (most recent call last):
     File "<stdin>", line 1, in ?
   TypeError: 'str' object does not support slice assignment

不过，组合文本内容生成一个新文本简单而高效::

   >>> 'x' + word[1:]
   'xelpA'
   >>> 'Splat' + word[4]
   'SplatA'

切片操作有个有用的不变性： ``s[:i] + s[i:]`` 等于 ``s``::

   >>> word[:2] + word[2:]
   'HelpA'
   >>> word[:3] + word[3:]
   'HelpA'

<<<<<<< HEAD:source/introduction.rst
Python 能够优雅地处理那些没有意义的切片索引：一个过大的索引值（即下标值大于字符串实际长度）将被字符串实际长度所代替，当上边界比下边界大时（即切片左值大于右值）就返回空字符串。 ::
=======
Python 能够优雅的处理那些没有意义的切片索引：一个过大的索引值（即下标值大于字符串实际长度）将被字符串实际长度所代替，当上边界比下边界大时（即切片左值大于右值）就返回空字符串::
>>>>>>> FETCH_HEAD:source/introduction.rst

   >>> word[1:100]
   'elpA'
   >>> word[10:]
   ''
   >>> word[2:1]
   ''

索引也可以是负数，这将导致从右边开始计算。 例如::

   >>> word[-1]     # The last character
   'A'
   >>> word[-2]     # The last-but-one character
   'p'
   >>> word[-2:]    # The last two characters
   'pA'
   >>> word[:-2]    # Everything except the last two characters
   'Hel'

请注意 -0 实际上就是 0 ，所以它不会导致从右边开始计算！
::

   >>> word[-0]     # (since -0 equals 0)
   'H'

负索引切片越界会被截断，不要尝试将它用于单元素（非切片）检索::

   >>> word[-100:]
   'HelpA'
   >>> word[-10]    # error
   Traceback (most recent call last):
     File "<stdin>", line 1, in ?
   IndexError: string index out of range

有个办法可以很容易地记住切片的工作方式：切片时的索引是在两个字符 *之间* 。左边第一个字符的索引为0，，而长度为 *n*  的字符串其最后一个字符的右界索引为 *n* 。例如::

    +---+---+---+---+---+
    | H | e | l | p | A |
    +---+---+---+---+---+
    0   1   2   3   4   5
   -5  -4  -3  -2  -1

文本中的第一行数字给出字符串中的索引点 0...5 。第二行给出相应的负索引。切片是从 *i* 到 *j* 两个数值标示的边界之间的所有字符。 

对于非负索引，如果上下都在边界内，切片长度与索引不同。例如， ``word[1:3]`` 是 2。 

内置函数 :func:`len` 返回字符串长度::

   >>> s = 'supercalifragilisticexpialidocious'
   >>> len(s)
   34


.. _tut-unicodestrings:

关于 Unicode
-------------

.. sectionauthor:: Marc-André Lemburg <mal@lemburg.com>


从 Python 3.0 开始所有的字符串都支持 Unicode（参考 http://www.unicode.org ）。

Unicode 的先进之处在于为每一种现代或古代使用的文字系统中出现的每一个字符都提供了统一的序列号。之前，文字系统中的字符只能有 256 种可能的顺序。通过代码页分界映射。文本绑定到映射文字系统的代码页。这在软件国际化的时候尤其麻烦 （通常写作 ``i18n`` —— ``'i'`` + 18 个字符 + ``'n'`` ）。Unicode 解决了为所有的文字系统设置一个独立代码页的难题。

如果想在字符串中包含特殊字符，你可以使用Python的 *Unicode_Escape* 编码方式。 下面的例子展示了如何这样做::

   >>> 'Hello\u0020World !'
   'Hello World !'

转码序列 ``\u0020`` 表示在指定位置插入编码为 0x0020 的 Unicode 字符（空格）。

其他字符就像 Unicode 编码一样被直接解释为对应的编码值。 如果你有在许多西方国家使用的标准 Latin-1 编码的字符串，你会发现编码小于 256 的 Unicode 字符和在 Latin-1 编码中的一样。

除了这些标准编码，Python 还提供了一整套基于其他已知编码创建 Unicode 字符串的方法。

<<<<<<< HEAD:source/introduction.rst
字符串对象提供了一个 :func:`encode` 方法用以将字符串转换成特定编码的字节序列，它接收一个小写的编码名称作为参数。 ::
=======
字符串对象提供了一个 :func:`encode` 方法用以将字符串转换成特定编码的字节序列，它接收一个小写的编码名称作为参数::
>>>>>>> FETCH_HEAD:source/introduction.rst

   >>> "Äpfel".encode('utf-8')
   b'\xc3\x84pfel'

.. _tut-lists:

列表
-----

Python 有几个 *复合* 数据类型，用于表示其它的值。最通用的是 *list* (列表) ，它可以写作中括号之间的一列逗号分隔的值。列表的元素不必是同一类型。 ::

   >>> a = ['spam', 'eggs', 100, 1234]
   >>> a
   ['spam', 'eggs', 100, 1234]

就像字符串索引，列表从 0 开始检索。列表可以被切片和连接::

   >>> a[0]
   'spam'
   >>> a[3]
   1234
   >>> a[-2]
   100
   >>> a[1:-1]
   ['eggs', 100]
   >>> a[:2] + ['bacon', 2*2]
   ['spam', 'eggs', 'bacon', 4]
   >>> 3*a[:3] + ['Boo!']
   ['spam', 'eggs', 100, 'spam', 'eggs', 100, 'spam', 'eggs', 100, 'Boo!']

所有的切片操作都会返回新的列表，包含求得的元素。这意味着以下的切片操作返回列表 *a* 的一个浅拷贝的副本::

   >>> a[:]
   ['spam', 'eggs', 100, 1234]

不像 *不可变的* 字符串，列表允许修改元素::

   >>> a
   ['spam', 'eggs', 100, 1234]
   >>> a[2] = a[2] + 23
   >>> a
   ['spam', 'eggs', 123, 1234]

也可以对切片赋值，此操作可以改变列表的尺寸，或清空它::

   >>> # Replace some items:
   ... a[0:2] = [1, 12]
   >>> a
   [1, 12, 123, 1234]
   >>> # Remove some:
   ... a[0:2] = []
   >>> a
   [123, 1234]
   >>> # Insert some:
   ... a[1:1] = ['bletch', 'xyzzy']
   >>> a
   [123, 'bletch', 'xyzzy', 1234]
   >>> # Insert (a copy of) itself at the beginning
   >>> a[:0] = a
   >>> a
   [123, 'bletch', 'xyzzy', 1234, 123, 'bletch', 'xyzzy', 1234]
   >>> # Clear the list: replace all items with an empty list
   >>> a[:] = []
   >>> a
   []

内置函数 :func:`len` 同样适用于列表::

   >>> a = ['a', 'b', 'c', 'd']
   >>> len(a)
   4

允许嵌套列表（创建一个包含其它列表的列表），例如::

   >>> q = [2, 3]
   >>> p = [1, q, 4]
   >>> len(p)
   3
   >>> p[1]
   [2, 3]
   >>> p[1][0]
   2

你可以在列表末尾添加内容::

   >>> p[1].append('xtra')
   >>> p
   [1, [2, 3, 'xtra'], 4]
   >>> q
   [2, 3, 'xtra']

注意最后一个例子中， ``p[1]`` 和 ``q`` 实际上指向同一个对象！ 我们会在后面的 *object semantics* 中继续讨论。


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

* 第一行包括了一个 *多重赋值* ：变量 ``a`` 和 ``b`` 同时获得了新的值 0 和 1 最后一行又使用了一次。在这个演示中，变量赋值前，右边首先完成计算。右边的表达式从左到右计算。

* 条件（这里是 ``b < 10`` ）为 true 时， :keyword:`while` 循环执行。在 Python 中，类似于 C ，任何非零整数都是 true；0 是 false 条件也可以是字符串或列表，实际上可以是任何序列；所有长度不为零的是 true ，空序列是 false。示例中的测试是一个简单的比较。标准比较操作符与 C 相同： ``<`` （小于）， ``>`` （大于）， ``==`` （等于）， ``<=`` （小于等于）， ``>=`` （大于等于）和 ``!=`` （不等于）。

* 循环 *体* 是 *缩进* 的：缩进是 Python 是 Python 组织語句的方法。 Python (还) 不提供集成的行编辑功能，所以你要为每一个缩进行输入 TAB 或空格。实践中建议你找个文本编辑来录入复杂的 Python 程序，大多数文本编辑器提供自动缩进。交互式录入复合语句时，必须在最后输入一个空行来标识结束（因为解释器没办法猜测你输入的哪一行是最后一行），需要 注意的是同一个语句块中的语句块必须缩进同样数量的空白。

* 关键字 :keyword:`print` 语句输出给定表达式的值。它控制多个表达式和字符串输出为你想要字符串（就像我们在前面计算器的例子中那样）。字符串打印时不用引号包围，每两个子项之间插入空间，所以你可以把格式弄得很漂亮，像这样::

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
