.. _tut-io:

****************
输入和输出
****************

一个程序可以有几种输出方式：以人类可读的方式打印数据，或者写入一个文件供以后使用。本章将讨论几种可能性。


.. _tut-formatting:

格式化输出
=========================

我们有两种大相径庭地输出值方法：*表达式语句* 和 `print()`_ 函数（第三种访求是使用文件对象的 :meth:`write` 方法，标准文件输出可以参考 ``sys.stdout``，详细内容参见库参考手册）。

通常，你想要对输出做更多的格式控制，而不是简单的打印使用空格分隔的值。有两种方法可以格式化你的输出：第一种方法是由你自己处理整个字符串，通过使用字符串切割和连接操作可以创建任何你想要的输出形式。string 类型包含一些将字符串填充到指定列宽度的有用操作，随后就会讨论这些。第二种方法是使用 `str.format()`_ 方法。

标准模块 `string`_ 包括了一些操作，将字符串填充入给定列时，这些操作很有用。随后我们会讨论这部分内容。第二种方法是使用 `Template`_ 方法。 

当然，还有一个问题，如何将值转化为字符串？很幸运，Python 有办法将任意值转为字符串：将它传入 `repr()`_ 或 `str()`_ 函数。 

函数 `str()`_ 用于将值转化为适于人阅读的形式，而 `repr()`_ 转化为供解释器读取的形式（如果没有等价的语法，则会发生 `SyntaxError`_ 异常）某对象没有适于人阅读的解释形式的话，`str()`_ 会返回与 `repr()`_ 等同的值。很多类型，诸如数值或链表、字典这样的结构，针对各函数都有着统一的解读方式。字符串和浮点数，有着独特的解读方式。

下面有些例子::

   >>> s = 'Hello, world.'
   >>> str(s)
   'Hello, world.'
   >>> repr(s)
   "'Hello, world.'"
   >>> str(1/7)
   '0.14285714285714285'
   >>> x = 10 * 3.25
   >>> y = 200 * 200
   >>> s = 'The value of x is ' + repr(x) + ', and y is ' + repr(y) + '...'
   >>> print(s)
   The value of x is 32.5, and y is 40000...
   >>> # The repr() of a string adds string quotes and backslashes:
   ... hello = 'hello, world\n'
   >>> hellos = repr(hello)
   >>> print(hellos)
   'hello, world\n'
   >>> # The argument to repr() may be any Python object:
   ... repr((x, y, ('spam', 'eggs')))
   "(32.5, 40000, ('spam', 'eggs'))"

有两种方式可以写平方和立方表::

   >>> for x in range(1, 11):
   ...     print(repr(x).rjust(2), repr(x*x).rjust(3), end=' ')
   ...     # Note use of 'end' on previous line
   ...     print(repr(x*x*x).rjust(4))
   ...
    1   1    1
    2   4    8
    3   9   27
    4  16   64
    5  25  125
    6  36  216
    7  49  343
    8  64  512
    9  81  729
   10 100 1000

   >>> for x in range(1, 11):
   ...     print('{0:2d} {1:3d} {2:4d}'.format(x, x*x, x*x*x))
   ...
    1   1    1
    2   4    8
    3   9   27
    4  16   64
    5  25  125
    6  36  216
    7  49  343
    8  64  512
    9  81  729
   10 100 1000

(注意第一个例子，`print()`_ 在每列之间加了一个空格，它总是在参数间加入空格。)

以上是一个 `str.rjust()`_ 方法的演示，它把字符串输出到一列，并通过向左侧填充空格来使其右对齐。类似的方法还有 `str.ljust()`_ 和 `str.center()`_。这些函数只是输出新的字符串，并不改变什么。如果输出的字符串太长，它们也不会截断它，而是原样输出，这会使你的输出格式变得混乱，不过总强过另一种选择（截断字符串），因为那样会产生错误的输出值（如果你确实需要截断它，可以使用切割操作，例如：``x.ljust(n)[:n]`` ）。

还有另一个方法， `str.zfill()`_ 它用于向数值的字符串表达左侧填充 0。该函数可以正确理解正负号::

   >>> '12'.zfill(5)
   '00012'
   >>> '-3.14'.zfill(7)
   '-003.14'
   >>> '3.14159265359'.zfill(5)
   '3.14159265359'

方法 `str.format()`_ 的基本用法如下::

   >>> print('We are the {} who say "{}!"'.format('knights', 'Ni'))
   We are the knights who say "Ni!"

大括号和其中的字符会被替换成传入 `str.format()`_ 的参数。大括号中的数值指明使用传入 `str.format()`_ 方法的对象中的哪一个::

   >>> print('{0} and {1}'.format('spam', 'eggs'))
   spam and eggs
   >>> print('{1} and {0}'.format('spam', 'eggs'))
   eggs and spam


如果在 `str.format()`_ 调用时使用关键字参数，可以通过参数名来引用值::


   >>> print('This {food} is {adjective}.'.format(
   ...       food='spam', adjective='absolutely horrible'))
   This spam is absolutely horrible.

位置参数和关键字参数可以随意组合::

   >>> print('The story of {0}, {1}, and {other}.'.format('Bill', 'Manfred',
                                                          other='Georg'))
   The story of Bill, Manfred, and Georg.

``'!a'`` (应用 `ascii()`_)，``'!s'`` （应用 `str()`_ ）和 ``'!r'`` （应用 `repr()`_ ）可以在格式化之前转换值::

   >>> import math
   >>> print('The value of PI is approximately {}.'.format(math.pi))
   The value of PI is approximately 3.14159265359.
   >>> print('The value of PI is approximately {!r}.'.format(math.pi))
   The value of PI is approximately 3.141592653589793.

字段名后允许可选的 ``':'`` 和格式指令。这允许对值的格式化加以更深入的控制。下例将 Pi 转为三位精度。

   >>> import math
   >>> print('The value of PI is approximately {0:.3f}.'.format(math.pi))
   The value of PI is approximately 3.142.

在字段后的 ``':'`` 后面加一个整数会限定该字段的最小宽度，这在美化表格时很有用::

   >>> table = {'Sjoerd': 4127, 'Jack': 4098, 'Dcab': 7678}
   >>> for name, phone in table.items():
   ...     print('{0:10} ==> {1:10d}'.format(name, phone))
   ...
   Jack       ==>       4098
   Dcab       ==>       7678
   Sjoerd     ==>       4127

如果你有个实在是很长的格式化字符串，不想分割它。如果你可以用命名来引用被格式化的变量而不是位置就好了。有个简单的方法，可以传入一个字典，用中括号( ``'[]'`` )访问它的键::

   >>> table = {'Sjoerd': 4127, 'Jack': 4098, 'Dcab': 8637678}
   >>> print('Jack: {0[Jack]:d}; Sjoerd: {0[Sjoerd]:d}; '
             'Dcab: {0[Dcab]:d}'.format(table))
   Jack: 4098; Sjoerd: 4127; Dcab: 8637678

也可以用 ‘**’ 标志将这个字典以关键字参数的方式传入::

   >>> table = {'Sjoerd': 4127, 'Jack': 4098, 'Dcab': 8637678}
   >>> print('Jack: {Jack:d}; Sjoerd: {Sjoerd:d}; Dcab: {Dcab:d}'.format(**table))
   Jack: 4098; Sjoerd: 4127; Dcab: 8637678

这种方式与新的内置函数 `vars()`_ 组合使用非常有效。该函数返回包含所有局部变量的字典。

要进一步了解字符串格式化方法 `str.format()`_，参见 `格式字符串语法`_。


旧式的字符串格式化
---------------------

操作符 ``%`` 也可以用于字符串格式化。它以类似 :c:func:`sprintf`\ -style 的方式解析左参数，将右参数应用于此，得到格式化操作生成的字符串，例如::

   >>> import math
   >>> print('The value of PI is approximately %5.3f.' % math.pi)
   The value of PI is approximately 3.142.

更多的信息可以参见 `printf-style String Formatting`_ 一节。


.. _tut-files:

文件读写
=========================

.. index::
   builtin: open
   object: file

函数 `open()`_ 返回 `文件对象`_，通常的用法需要两个参数：``open(filename, mode)``。

::

   >>> f = open('workfile', 'w')

.. XXX str(f) is <io.TextIOWrapper object at 0x82e8dc4>

   >>> print(f)
   <open file 'workfile', mode 'w' at 80a0960>

第一个参数是一个含有文件名的字符串。第二个参数也是一个字符串，含有描述如何使用该文件的几个字符。*mode* 为 ``'r'`` 时表示只是读取文件；``'w'`` 表示只是写入文件（已经存在的同名文件将被删掉）；``'a'`` 表示打开文件进行追加，写入到文件中的任何数据将自动添加到末尾。 ``'r+'`` 表示打开文件进行读取和写入。*mode* 参数是可选的，默认为 ``'r'``。

通常，文件以 :dfn:`文本` 打开，这意味着，你从文件读出和向文件写入的字符串会被特定的编码方式（默认是UTF-8）编码。模式后面的 ``'b'`` 以 :dfn:`二进制模式` 打开文件：数据会以字节对象的形式读出和写入。这种模式应该用于所有不包含文本的文件。

在文本模式下，读取时默认会将平台有关的行结束符（Unix上是 ``\n`` , Windows上是 ``\r\n``）转换为 ``\n``。在文本模式下写入时，默认会将出现的 ``\n`` 转换成平台有关的行结束符。这种暗地里的修改对 ASCII 文本文件没有问题，但会损坏 :file:`JPEG` 或 :file:`EXE` 这样的二进制文件中的数据。使用二进制模式读写此类文件时要特别小心。

.. _tut-filemethods:

文件对象方法
-----------------------

本节中的示例都默认文件对象 ``f`` 已经创建。 

要读取文件内容，需要调用 ``f.read(size)``，该方法读取若干数量的数据并以字符串形式返回其内容，*size* 是可选的数值，指定字符串长度。如果没有指定 *size* 或者指定为负数，就会读取并返回整个文件。当文件大小为当前机器内存两倍时，就会产生问题。反之，会尽可能按比较大的 *size* 读取和返回数据。如果到了文件末尾，``f.read()`` 会返回一个空字符串（``''``）::

   >>> f.read()
   'This is the entire file.\n'
   >>> f.read()
   ''

``f.readline()`` 从文件中读取单独一行，字符串结尾会自动加上一个换行符（ ``\n`` ），只有当文件最后一行没有以换行符结尾时，这一操作才会被忽略。这样返回值就不会有混淆，如果 ``f.readline()`` 返回一个空字符串，那就表示到达了文件末尾，如果是一个空行，就会描述为 ``'\n'``，一个只包含换行符的字符串::

   >>> f.readline()
   'This is the first line of the file.\n'
   >>> f.readline()
   'Second line of the file\n'
   >>> f.readline()
   ''

你可以循环遍历文件对象来读取文件中的每一行。这是一种内存高效、快速，并且代码简介的方式::

   >>> for line in f:
   ...     print(line, end='')
   ...
   This is the first line of the file.
   Second line of the file

如果你想把文件中的所有行读到一个列表中，你也可以使用 ``list(f)`` 或者 ``f.readlines()``。

``f.write(string)`` 方法将 *string* 的内容写入文件，并返回写入字符的长度::

   >>> f.write('This is a test\n')
   15

想要写入其他非字符串内容，首先要将它转换为字符串::

   >>> value = ('the answer', 42)
   >>> s = str(value)
   >>> f.write(s)
   18

``f.tell()`` 返回一个整数，代表文件对象在文件中的指针位置，该数值计量了自文件开头到指针处的比特数。需要改变文件对象指针话话，使用 ``f.seek(offset,from_what)``。指针在该操作中从指定的引用位置移动 *offset* 比特，引用位置由 *from_what* 参数指定。 *from_what* 值为 0 表示自文件起始处开始，1 表示自当前文件指针位置开始，2 表示自文件末尾开始。*from_what* 可以忽略，其默认值为零，此时从文件头开始::

   >>> f = open('workfile', 'rb+')
   >>> f.write(b'0123456789abcdef')
   16
   >>> f.seek(5)     # Go to the 6th byte in the file
   5
   >>> f.read(1)
   b'5'
   >>> f.seek(-3, 2) # Go to the 3rd byte before the end
   13
   >>> f.read(1)
   b'd'

在文本文件中（没有以 ``b`` 模式打开），只允许从文件头开始寻找（有个例外是用 ``seek(0, 2)`` 寻找文件的最末尾处）而且合法的 *偏移* 值只能是 ``f.tell()`` 返回的值或者是零。其它任何 *偏移* 值都会产生未定义的行为。

当你使用完一个文件时，调用 ``f.close()`` 方法就可以关闭它并释放其占用的所有系统资源。 在调用 ``f.close()`` 方法后，试图再次使用文件对象将会自动失败。 ::

   >>> f.close()
   >>> f.read()
   Traceback (most recent call last):
     File "<stdin>", line 1, in ?
   ValueError: I/O operation on closed file

用关键字 `with`_ 处理文件对象是个好习惯。它的先进之处在于文件用完后会自动关闭，就算发生异常也没关系。它是 `try`_\ -\ `finally`_ 块的简写::

    >>> with open('workfile', 'r') as f:
    ...     read_data = f.read()
    >>> f.closed
    True

文件对象还有一些不太常用的附加方法，比如 :meth:`~file.isatty` 和 :meth:`~file.truncate` 在库参考手册中有文件对象的完整指南。


.. _tut-json:

使用 `json`_ 存储结构化数据 
------------------------------------------------------------------------------------------

.. index:: module: json


从文件中读写字符串很容易。数值就要多费点儿周折，因为 :meth:`read` 方法只会返回字符串，应将其传入 `int()`_ 这样的函数，就可以将 ``'123'`` 这样的字符串转换为对应的数值 123。当你想要保存更为复杂的数据类型，例如嵌套的列表和字典，手工解析和序列化它们将变得更复杂。

好在用户不是非得自己编写和调试保存复杂数据类型的代码，Python 允许你使用常用的数据交换格式 `JSON（JavaScript Object Notation）`_。标准模块 `json`_ 可以接受 Python 数据结构，并将它们转换为字符串表示形式；此过程称为 **序列化**。从字符串表示形式重新构建数据结构称为 **反序列化**。序列化和反序列化的过程中，表示该对象的字符串可以存储在文件或数据中，也可以通过网络连接传送给远程的机器。

.. note::
   JSON 格式经常用于现代应用程序中进行数据交换。许多程序员都已经熟悉它了，使它成为相互协作的一个不错的选择。

如果你有一个对象 ``x``，你可以用简单的一行代码查看其 JSON 字符串表示形式::

   >>> json.dumps([1, 'simple', 'list'])
   '[1, "simple", "list"]'

`dumps()`_ 函数的另外一个变体 `dump()`_，直接将对象序列化到一个文件。所以如果 ``f`` 是为写入而打开的一个 `文件对象`_，我们可以这样做::

   json.dump(x, f)

为了重新解码对象，如果 ``f`` 是为读取而打开的 `文件对象`_::

   x = json.load(f)

这种简单的序列化技术可以处理列表和字典，但序列化任意类实例为 JSON 需要一点额外的努力。 `json`_ 模块的手册对此有详细的解释。

.. seealso::

   `pickle`_ - pickle 模块

   与 :ref:`JSON <tut-json>` 不同，*pickle* 是一个协议，它允许任意复杂的 Python 对象的序列化。因此，它只能用于 Python 而不能用来与其他语言编写的应用程序进行通信。默认情况下它也是不安全的：如果数据由熟练的攻击者精心设计， 反序列化来自一个不受信任源的 pickle 数据可以执行任意代码。


.. _print(): https://docs.python.org/3/library/functions.html#print
.. _str.format(): https://docs.python.org/3/library/stdtypes.html#str.format
.. _string: https://docs.python.org/3/library/string.html#module-string
.. _Template: https://docs.python.org/3/library/string.html#string.Template
.. _repr(): https://docs.python.org/3/library/functions.html#repr
.. _str(): https://docs.python.org/3/library/stdtypes.html#str
.. _SyntaxError: https://docs.python.org/3/library/exceptions.html#SyntaxError
.. _str.rjust(): https://docs.python.org/3/library/stdtypes.html#str.rjust
.. _str.ljust(): https://docs.python.org/3/library/stdtypes.html#str.ljust
.. _str.center(): https://docs.python.org/3/library/stdtypes.html#str.center
.. _str.zfill(): https://docs.python.org/3/library/stdtypes.html#str.zfill
.. _ascii(): https://docs.python.org/3/library/functions.html#ascii
.. _vars(): https://docs.python.org/3/library/functions.html#vars
.. _格式字符串语法: https://docs.python.org/3/library/string.html#formatstrings
.. _printf-style String Formatting: https://docs.python.org/3/library/stdtypes.html#old-string-formatting
.. _open(): https://docs.python.org/3/library/functions.html#open
.. _文件对象: https://docs.python.org/3/glossary.html#term-file-object
.. _with: https://docs.python.org/3/reference/compound_stmts.html#with
.. _try: https://docs.python.org/3/reference/compound_stmts.html#try
.. _finally: https://docs.python.org/3/reference/compound_stmts.html#finally
.. _json: https://docs.python.org/3/library/json.html#module-json
.. _int(): https://docs.python.org/3/library/functions.html#int
.. _JSON（JavaScript Object Notation）: http://json.org/
.. _dumps(): https://docs.python.org/3/library/json.html#json.dumps
.. _dump(): https://docs.python.org/3/library/json.html#json.dump
.. _pickle: https://docs.python.org/3/library/pickle.html#module-pickle

