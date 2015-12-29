.. _tut-morecontrol:

***********************
深入 Python 流程控制
***********************

除了前面介绍的 `while`_ 语句，Python 还从其它语言借鉴了一些流程控制功能，并有所改变。


.. _tut-if:

`if`_ 语句
========================

也许最有名的是 `if`_ 语句。例如::

   >>> x = int(input("Please enter an integer: "))
   Please enter an integer: 42
   >>> if x < 0:
   ...      x = 0
   ...      print('Negative changed to zero')
   ... elif x == 0:
   ...      print('Zero')
   ... elif x == 1:
   ...      print('Single')
   ... else:
   ...      print('More')
   ...
   More

可能会有零到多个 `elif`_ 部分，`else`_ 是可选的。关键字 '`elif`_' 是 ’else if’ 的缩写，这个可以有效地避免过深的缩进。`if`_ ... `elif`_ ... `elif`_ ... 序列用于替代其它语言中的 ``switch`` 或 ``case`` 语句。


.. _tut-for:

`for`_ 语句
=========================

.. index::
   statement: for

Python 中的 `for`_ 语句和 C 或 Pascal 中的略有不同。通常的循环可能会依据一个等差数值步进过程（如 Pascal），或由用户来定义迭代步骤和中止条件（如 C ），Python 的 `for`_  语句依据任意序列（链表或字符串）中的子项，按它们在序列中的顺序来进行迭代。例如（没有暗指）：

.. One suggestion was to give a real C example here, but that may only serve to
   confuse non-C programmers.

::

   >>> # Measure some strings:
   ... words = ['cat', 'window', 'defenestrate']
   >>> for w in words:
   ...     print(w, len(w))
   ...
   cat 3
   window 6
   defenestrate 12

在迭代过程中修改迭代序列不安全（只有在使用链表这样的可变序列时才会有这样的情况）。如果你想要修改你迭代的序列（例如，复制选择项），你可以迭代它的复本。使用切割标识就可以很方便的做到这一点::

   >>> for w in words[:]:  # Loop over a slice copy of the entire list.
   ...     if len(w) > 6:
   ...         words.insert(0, w)
   ...
   >>> words
   ['defenestrate', 'cat', 'window', 'defenestrate']



.. _tut-range:

`range()`_ 函数
==========================

如果你需要一个数值序列，内置函数 `range()`_ 会很方便，它生成一个等差级数链表::

    >>> for i in range(5):
    ...     print(i)
    ...
    0
    1
    2
    3
    4

``range(10)`` 生成了一个包含 10 个值的链表，它用链表的索引值填充了这个长度为 10 的列表，所生成的链表中不包括范围中的结束值。也可以让 `range()`_ 操作从另一个数值开始，或者可以指定一个不同的步进值（甚至是负数，有时这也被称为 “步长”）::

    range(5, 10)
       5 through 9

    range(0, 10, 3)
       0, 3, 6, 9

    range(-10, -100, -30)
      -10, -40, -70

需要迭代链表索引的话，如下所示结合使 用 `range()`_ 和 `len()`_ ::

   >>> a = ['Mary', 'had', 'a', 'little', 'lamb']
   >>> for i in range(len(a)):
   ...     print(i, a[i])
   ...
   0 Mary
   1 had
   2 a
   3 little
   4 lamb

不过，这种场合可以方便的使用 `enumerate()`_，请参见 :ref:`tut-loopidioms`。

如果你只是打印一个序列的话会发生奇怪的事情::

   >>> print(range(10))
   range(0, 10)

在不同方面 `range()`_ 函数返回的对象表现为它是一个列表，但事实上它并不是。当你迭代它时，它是一个能够像期望的序列返回连续项的对象；但为了节省空间，它并不真正构造列表。

我们称此类对象是 *可迭代的*，即适合作为那些期望从某些东西中获得连续项直到结束的函数或结构的一个目标（参数）。我们已经见过的 `for`_ 语句就是这样一个迭代器。`list()`_ 函数是另外一个（ *迭代器* ），它从可迭代（对象）中创建列表::


   >>> list(range(5))
   [0, 1, 2, 3, 4]

稍后我们会看到更多返回可迭代（对象）和以可迭代（对象）作为参数的函数。


.. _tut-break:

`break`_ 和 `continue`_ 语句, 以及循环中的 `else`_ 子句
=========================================================================================

`break`_ 语句和 C 中的类似，用于跳出最近的一级 `for`_ 或 `while`_ 循环。 


循环可以有一个 ``else`` 子句；它在循环迭代完整个列表（对于 `for`_ ）或执行条件为 false （对于 `while`_ ）时执行，但循环被 `break`_ 中止的情况下不会执行。以下搜索素数的示例程序演示了这个子句::

   >>> for n in range(2, 10):
   ...     for x in range(2, n):
   ...         if n % x == 0:
   ...             print(n, 'equals', x, '*', n//x)
   ...             break
   ...     else:
   ...         # loop fell through without finding a factor
   ...         print(n, 'is a prime number')
   ...
   2 is a prime number
   3 is a prime number
   4 equals 2 * 2
   5 is a prime number
   6 equals 2 * 3
   7 is a prime number
   8 equals 2 * 4
   9 equals 3 * 3

(Yes, 这是正确的代码。看仔细：``else`` 语句是属于 `for`_ 循环之中， **不是**  `if`_ 语句。)

与循环一起使用时，``else`` 子句与 `try`_ 语句的 ``else`` 子句比与 `if`_ 语句的具有更多的共同点：`try`_ 语句的 ``else`` 子句在未出现异常时运行，循环的 ``else`` 子句在未出现 ``break`` 时运行。更多关于 `try`_ 语句和异常的内容，请参见 :ref:`tut-handling`。

`continue`_ 语句是从 C 中借鉴来的，它表示循环继续执行下一次迭代::

    >>> for num in range(2, 10):
    ...     if num % 2 == 0:
    ...         print("Found an even number", num)
    ...         continue
    ...     print("Found a number", num)
    Found an even number 2
    Found a number 3
    Found an even number 4
    Found a number 5
    Found an even number 6
    Found a number 7
    Found an even number 8
    Found a number 9

.. _tut-pass:

`pass`_ 语句
==========================

`pass`_ 语句什么也不做。它用于那些语法上必须要有什么语句，但程序什么也不做的场合，例如::

   >>> while True:
   ...     pass  # Busy-wait for keyboard interrupt (Ctrl+C)
   ...

这通常用于创建最小结构的类::

   >>> class MyEmptyClass:
   ...     pass
   ...

另一方面，`pass`_ 可以在创建新代码时用来做函数或控制体的占位符。可以让你在更抽象的级别上思考。`pass`_ 可以默默的被忽视::

   >>> def initlog(*args):
   ...     pass   # Remember to implement this!
   ...

.. _tut-functions:

定义函数
==================

我们可以创建一个用来生成指定边界的斐波那契数列的函数::

   >>> def fib(n):    # write Fibonacci series up to n
   ...     """Print a Fibonacci series up to n."""
   ...     a, b = 0, 1
   ...     while a < n:
   ...         print(a, end=' ')
   ...         a, b = b, a+b
   ...     print()
   ...
   >>> # Now call the function we just defined:
   ... fib(2000)
   0 1 1 2 3 5 8 13 21 34 55 89 144 233 377 610 987 1597

.. index::
   single: documentation strings
   single: docstrings
   single: strings, documentation

关键字 `def`_ 引入了一个函数 *定义*。在其后必须跟有函数名和包括形式参数的圆括号。函数体语句从下一行开始，必须是缩进的。 

函数体的第一行语句可以是可选的字符串文本，这个字符串是函数的文档字符串，或者称为 :dfn:`docstring`。（更多关于 docstrings 的信息请参考 :ref:`tut-docstrings`） 有些工具通过 docstrings 自动生成在线的或可打印的文档，或者让用户通过代码交互浏览；在你的代码中包含 docstrings 是一个好的实践，让它成为习惯吧。

函数 *调用* 会为函数局部变量生成一个新的符号表。确切的说，所有函数中的变量赋值都是将值存储在局部符号表。变量引用首先在局部符号表中查找，然后是包含函数的局部符号表，然后是全局符号表，最后是内置名字表。因此，全局变量不能在函数中直接赋值（除非用 `global`_ 语句命名），尽管他们可以被引用。 

函数引用的实际参数在函数调用时引入局部符号表，因此，实参总是 *传值调用* （这里的 *值* 总是一个对象 引用 ，而不是该对象的值）。[#]_  一个函数被另一个函数调用时，一个新的局部符号表在调用过程中被创建。 


一个函数定义会在当前符号表内引入函数名。函数名指代的值（即函数体）有一个被 Python 解释器认定为 *用户自定义函数* 的类型。 这个值可以赋予其他的名字（即变量名），然后它也可以被当做函数使用。这可以作为通用的重命名机制::


   >>> fib
   <function fib at 10042ed0>
   >>> f = fib
   >>> f(100)
   0 1 1 2 3 5 8 13 21 34 55 89

如果你使用过其他语言，你可能会反对说：``fib`` 不是一个函数，而是一个方法，因为它并不返回任何值。事实上，没有 `return`_ 语句的函数确实会返回一个值，虽然是一个相当令人厌烦的值（指 None ）。这个值被称为 ``None`` （这是一个内建名称）。如果 ``None`` 值是唯一被书写的值，那么在写的时候通常会被解释器忽略（即不输出任何内容）。如果你确实想看到这个值的输出内容，请使用 `print()`_ 函数::

   >>> fib(0)
   >>> print(fib(0))
   None

定义一个返回斐波那契数列数字列表的函数，而不是打印它，是很简单的::

   >>> def fib2(n): # return Fibonacci series up to n
   ...     """Return a list containing the Fibonacci series up to n."""
   ...     result = []
   ...     a, b = 0, 1
   ...     while a < n:
   ...         result.append(a)    # see below
   ...         a, b = b, a+b
   ...     return result
   ...
   >>> f100 = fib2(100)    # call it
   >>> f100                # write the result
   [0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89]

和以前一样，这个例子演示了一些新的 Python 功能：

* `return`_ 语句从函数中返回一个值，不带表达式的 `return`_ 返回 ``None``。
  
  过程结束后也会返回 ``None``。

* 语句 ``result.append(b)`` 称为链表对象 ``result`` 的一个 *方法*。方法是一个“属于”某个对象的函数，它被命名为 ``obj.methodename``，这里的 ``obj`` 是某个对象（可能是一个表达式）， ``methodename`` 是某个在该对象类型定义中的方法的命名。
  
  不同的类型定义不同的方法。不同类型可能有同样名字的方法，但不会混淆。（当你定义自己的对象类型和方法时，可能会出现这种情况，*class* 的定义方法详见 :ref:`tut-classes` ）。示例中演示的 :meth:`append` 方法由链表对象定义，它向链表中加入一个新元素。在示例中它等同于 ``result = result + [a]``，不过效率更高。


.. _tut-defining:

深入 Python 函数定义 
==========================

在 Python 中，你也可以定义包含若干参数的函数。这里有三种可用的形式，也可以混合使用。 


.. _tut-defaultargs:

默认参数值 
-----------------------

最常用的一种形式是为一个或多个参数指定默认值。这会创建一个可以使用比定义时允许的参数更少的参数调用的函数，例如::

   def ask_ok(prompt, retries=4, complaint='Yes or no, please!'):
       while True:
           ok = input(prompt)
           if ok in ('y', 'ye', 'yes'):
               return True
           if ok in ('n', 'no', 'nop', 'nope'):
               return False
           retries = retries - 1
           if retries < 0:
               raise OSError('uncooperative user')
           print(complaint)

这个函数可以通过几种不同的方式调用:

* 只给出必要的参数:
  
  ``ask_ok('Do you really want to quit?')``

* 给出一个可选的参数:
  
  ``ask_ok('OK to overwrite the file?', 2)``

* 或者给出所有的参数:
  
  ``ask_ok('OK to overwrite the file?', 2, 'Come on, only yes or no!')``

这个例子还介绍了 `in`_ 关键字。它测定序列中是否包含某个确定的值。 

默认值在函数 *定义* 作用域被解析，如下所示::

   i = 5

   def f(arg=i):
       print(arg)

   i = 6
   f()

将会输出 ``5``。

**重要警告:**  默认值只被赋值一次。这使得当默认值是可变对象时会有所不同，比如列表、字典或者大多数类的实例。例如，下面的函数在后续调用过程中会累积（前面）传给它的参数::

   def f(a, L=[]):
       L.append(a)
       return L

   print(f(1))
   print(f(2))
   print(f(3))

这将输出::

   [1]
   [1, 2]
   [1, 2, 3]

如果你不想让默认值在后续调用中累积，你可以像下面一样定义函数::

   def f(a, L=None):
       if L is None:
           L = []
       L.append(a)
       return L


.. _tut-keywordargs:

关键字参数 
-----------------

函数可以通过 `关键字参数 <keyword argument>`_ 的形式来调用，形如 ``keyword = value``。例如，以下的函数::

   def parrot(voltage, state='a stiff', action='voom', type='Norwegian Blue'):
       print("-- This parrot wouldn't", action, end=' ')
       print("if you put", voltage, "volts through it.")
       print("-- Lovely plumage, the", type)
       print("-- It's", state, "!")

接受一个必选参数 (``voltage``) 以及三个可选参数
(``state``, ``action``, 和 ``type``)。可以用以下的任一方法调用::

   parrot(1000)                                          # 1 positional argument
   parrot(voltage=1000)                                  # 1 keyword argument
   parrot(voltage=1000000, action='VOOOOOM')             # 2 keyword arguments
   parrot(action='VOOOOOM', voltage=1000000)             # 2 keyword arguments
   parrot('a million', 'bereft of life', 'jump')         # 3 positional arguments
   parrot('a thousand', state='pushing up the daisies')  # 1 positional, 1 keyword

不过以下几种调用是无效的::

   parrot()                     # required argument missing
   parrot(voltage=5.0, 'dead')  # non-keyword argument after a keyword argument
   parrot(110, voltage=220)     # duplicate value for the same argument
   parrot(actor='John Cleese')  # unknown keyword argument

在函数调用中，关键字的参数必须跟随在位置参数的后面。传递的所有关键字参数必须与函数接受的某个参数相匹配 （例如 ``actor`` 不是 ``parrot`` 函数的有效参数），它们的顺序并不重要。这也包括非可选参数（例如 ``parrot(voltage=1000)`` 也是有效的）。任何参数都不可以多次赋值。下面的示例由于这种限制将失败::

   >>> def function(a):
   ...     pass
   ...
   >>> function(0, a=0)
   Traceback (most recent call last):
     File "<stdin>", line 1, in ?
   TypeError: function() got multiple values for keyword argument 'a'

引入一个形如 ``**name`` 的参数时，它接收一个字典（参见 `Mapping Types — dict`_ ），该字典包含了所有未出现在形式参数列表中的关键字参数。这里可能还会组合使用一个形如 ``*name`` （下一小节详细介绍） 的形式参数，它接收一个元组（下一节中会详细介绍），包含了所有没有出现在形式参数列表中的参数值（ ``*name`` 必须在 ``**name`` 之前出现）。 例如，我们这样定义一个函数::

   def cheeseshop(kind, *arguments, **keywords):
       print("-- Do you have any", kind, "?")
       print("-- I'm sorry, we're all out of", kind)
       for arg in arguments:
           print(arg)
       print("-" * 40)
       keys = sorted(keywords.keys())
       for kw in keys:
           print(kw, ":", keywords[kw])

它可以像这样调用::

   cheeseshop("Limburger", "It's very runny, sir.",
              "It's really very, VERY runny, sir.",
              shopkeeper="Michael Palin",
              client="John Cleese",
              sketch="Cheese Shop Sketch")

当然它会按如下内容打印::

   -- Do you have any Limburger ?
   -- I'm sorry, we're all out of Limburger
   It's very runny, sir.
   It's really very, VERY runny, sir.
   ----------------------------------------
   client : John Cleese
   shopkeeper : Michael Palin
   sketch : Cheese Shop Sketch

注意在打印关键字参数之前，通过对关键字字典 ``keys()`` 方法的结果进行排序，生成了关键字参数名的列表；如果不这样做，打印出来的参数的顺序是未定义的。

.. _tut-arbitraryargs:

可变参数列表
------------------------

.. index::
  statement: *

最后，一个最不常用的选择是可以让函数调用可变个数的参数。这些参数被包装进一个元组（参见 :ref:`tut-tuples` ）。在这些可变个数的参数之前，可以有零到多个普通的参数::

   def write_multiple_items(file, separator, *args):
       file.write(separator.join(args))

通常，这些 ``可变`` 参数是参数列表中的最后一个，因为它们将把所有的剩余输入参数传递给函数。任何出现在 ``*args`` 后的参数是关键字参数，这意味着，他们只能被用作关键字，而不是位置参数::

   >>> def concat(*args, sep="/"):
   ...    return sep.join(args)
   ...
   >>> concat("earth", "mars", "venus")
   'earth/mars/venus'
   >>> concat("earth", "mars", "venus", sep=".")
   'earth.mars.venus'

.. _tut-unpacking-arguments:

参数列表的分拆
------------------------

另有一种相反的情况: 当你要传递的参数已经是一个列表，但要调用的函数却接受分开一个个的参数值。这时候你要把已有的列表拆开来。例如内建函数 `range()`_ 需要要独立的 *start*，*stop* 参数。你可以在调用函数时加一个 ``*`` 操作符来自动把参数列表拆开::

   >>> list(range(3, 6))            # normal call with separate arguments
   [3, 4, 5]
   >>> args = [3, 6]
   >>> list(range(*args))            # call with arguments unpacked from a list
   [3, 4, 5]

.. index::
  statement: **

以同样的方式，可以使用 ``**`` 操作符分拆关键字参数为字典::

   >>> def parrot(voltage, state='a stiff', action='voom'):
   ...     print("-- This parrot wouldn't", action, end=' ')
   ...     print("if you put", voltage, "volts through it.", end=' ')
   ...     print("E's", state, "!")
   ...
   >>> d = {"voltage": "four million", "state": "bleedin' demised", "action": "VOOM"}
   >>> parrot(**d)
   -- This parrot wouldn't VOOM if you put four million volts through it. E's bleedin' demised !


.. _tut-lambda:

Lambda 形式
------------

出于实际需要，有几种通常在函数式编程语言例如 Lisp 中出现的功能加入到了 Python。通过 `lambda`_  关键字，可以创建短小的匿名函数。这里有一个函数返回它的两个参数的和： ``lambda a, b: a+b``。 Lambda 形式可以用于任何需要的函数对象。出于语法限制，它们只能有一个单独的表达式。语义上讲，它们只是普通函数定义中的一个语法技巧。类似于嵌套函数定义，lambda 形式可以从外部作用域引用变量::

   >>> def make_incrementor(n):
   ...     return lambda x: x + n
   ...
   >>> f = make_incrementor(42)
   >>> f(0)
   42
   >>> f(1)
   43

上面的示例使用 lambda 表达式返回一个函数。另一个用途是将一个小函数作为参数传递::

   >>> pairs = [(1, 'one'), (2, 'two'), (3, 'three'), (4, 'four')]
   >>> pairs.sort(key=lambda pair: pair[1])
   >>> pairs
   [(4, 'four'), (1, 'one'), (3, 'three'), (2, 'two')]


.. _tut-docstrings:

文档字符串
---------------------

.. index::
   single: docstrings
   single: documentation strings
   single: strings, documentation

这里介绍的文档字符串的概念和格式。 

第一行应该是关于对象用途的简介。简短起见，不用明确的陈述对象名或类型，因为它们可以从别的途径了解到（除非这个名字碰巧就是描述这个函数操作的动词）。这一行应该以大写字母开头，以句号结尾。 

如果文档字符串有多行，第二行应该空出来，与接下来的详细描述明确分隔。接下来的文档应该有一或多段描述对象的调用约定、边界效应等。 

Python 的解释器不会从多行的文档字符串中去除缩进，所以必要的时候应当自己清除缩进。这符合通常的习惯。第一行之后的第一个非空行决定了整个文档的缩进格式。（我们不用第一行是因为它通常紧靠着起始的引号，缩进格式显示的不清楚。）留白“相当于”是字符串的起始缩进。每一行都不应该有缩进，如果有缩进的话，所有的留白都应该清除掉。留白的长度应当等于扩展制表符的宽度（通常是8个空格）。 

以下是一个多行文档字符串的示例::

   >>> def my_function():
   ...     """Do nothing, but document it.
   ...
   ...     No, really, it doesn't do anything.
   ...     """
   ...     pass
   ...
   >>> print(my_function.__doc__)
   Do nothing, but document it.

       No, really, it doesn't do anything.


.. _tut-annotations:

函数注解
--------------------

.. sectionauthor:: Zachary Ware <zachary.ware@gmail.com>
.. index::
   pair: function; annotations
   single: -> (return annotation assignment)

`函数注解`_ 是关于用户自定义的函数的完全可选的、随意的元数据信息。无论 Python 本身或者标准库中都没有使用函数注解；本节只是描述了语法。第三方的项目是自由地为文档，类型检查，以及其它用途选择函数注解。

注解是以字典形式存储在函数的 :attr:`__annotations__` 属性中，对函数的其它部分没有任何影响。参数注解（Parameter
annotations）是定义在参数名称的冒号后面，紧随着一个用来表示注解的值得表达式。返回注释（Return annotations）是定义在一个 ``->`` 后面，紧随着一个表达式，在冒号与 ``->`` 之间。下面的示例包含一个位置参数，一个关键字参数，和没有意义的返回值注释::

   >>> def f(ham: 42, eggs: int = 'spam') -> "Nothing to see here":
   ...     print("Annotations:", f.__annotations__)
   ...     print("Arguments:", ham, eggs)
   ...
   >>> f('wonderful')
   Annotations: {'eggs': <class 'int'>, 'return': 'Nothing to see here', 'ham': 42}
   Arguments: wonderful spam


.. _tut-codingstyle:

插曲：编码风格
========================

.. sectionauthor:: Georg Brandl <georg@python.org>
.. index:: pair: coding; style

此时你已经可以写一些更长更复杂的 Python 程序，是时候讨论一下 *编码风格* 了。大多数语言可以写（或者更明白的说， *格式化* ）作几种不同的风格。有些比其它的更好读。让你的代码对别人更易读是个好想法，养成良好的编码风格对此很有帮助。 

对于 Python，`PEP 8`_ 引入了大多数项目遵循的风格指导。它给出了一个高度可读，视觉友好的编码风格。每个 Python 开发者都应该读一下，大多数要点都会对你有帮助：

* 使用 4 空格缩进，而非 TAB

  在小缩进（可以嵌套更深）和大缩进（更易读）之间，4空格是一个很好的折中。TAB 引发了一些混乱，最好弃用

* 折行以确保其不会超过 79 个字符

  这有助于小显示器用户阅读，也可以让大显示器能并排显示几个代码文件

* 使用空行分隔函数和类，以及函数中的大块代码

* 可能的话，注释独占一行

* 使用文档字符串

* 把空格放到操作符两边，以及逗号后面，但是括号里侧不加空格：``a = f(1, 2) + g(3, 4)`` 

* 统一函数和类命名

  推荐类名用 ``驼峰命名``， 函数和方法名用 ``小写_和_下划线``。总是用 ``self`` 作为方法的第一个参数（关于类和方法的知识详见 :ref:`tut-firstclasses` ）

* 不要使用花哨的编码，如果你的代码的目的是要在国际化环境。Python 的默认情况下，UTF-8，甚至普通的 ASCII 总是工作的最好

* 同样，也不要使用非 ASCII 字符的标识符，除非是不同语种的会阅读或者维护代码。


.. rubric:: Footnotes

.. [#] 实际上， *引用对象调用* 描述的更为准确。如果传入一个可变对像，调用者会看到调用操作带来的任何变化（如子项插入到列表中）。



.. _while: https://docs.python.org/3/reference/compound_stmts.html#while
.. _if: https://docs.python.org/3/reference/compound_stmts.html#if
.. _elif: https://docs.python.org/3/reference/compound_stmts.html#elif
.. _else: https://docs.python.org/3/reference/compound_stmts.html#else
.. _for: https://docs.python.org/3/reference/compound_stmts.html#for
.. _range(): https://docs.python.org/3/library/stdtypes.html#range 
.. _len(): https://docs.python.org/3/library/functions.html#len
.. _enumerate(): https://docs.python.org/3/library/functions.html#enumerate
.. _list(): https://docs.python.org/3/library/stdtypes.html#list
.. _break: https://docs.python.org/3/reference/simple_stmts.html#break
.. _continue: https://docs.python.org/3/reference/simple_stmts.html#continue
.. _try: https://docs.python.org/3/reference/compound_stmts.html#try
.. _pass: https://docs.python.org/3/reference/simple_stmts.html#pass
.. _def: https://docs.python.org/3/reference/compound_stmts.html#def
.. _global: https://docs.python.org/3/reference/simple_stmts.html#global
.. _return: https://docs.python.org/3/reference/simple_stmts.html#return
.. _print(): https://docs.python.org/3/library/functions.html#print
.. _in: https://docs.python.org/3/reference/expressions.html#in
.. _关键字参数 <keyword argument>: https://docs.python.org/3/glossary.html#term-keyword-argument
.. _Mapping Types — dict: https://docs.python.org/3/library/stdtypes.html#typesmapping
.. _lambda: https://docs.python.org/3/reference/expressions.html#lambda
.. _PEP 8: http://www.python.org/dev/peps/pep-0008
.. _函数注解: https://docs.python.org/3/reference/compound_stmts.html#function
