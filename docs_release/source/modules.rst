.. _tut-modules:

*******
模块
*******

如果你退出 Python 解释器并重新进入，你做的任何定义（变量和方法）都会丢失。因此，如果你想要编写一些更大的程序，为准备解释器输入使用一个文本编辑器会更好，并以那个文件替代作为输入执行。这就是传说中的 *脚本*。随着你的程序变得越来越长，你可能想要将它分割成几个更易于维护的文件。你也可能想在不同的程序中使用顺手的函数，而不是把代码在它们之间中拷来拷去。

为了满足这些需要，Python 提供了一个方法可以从文件中获取定义，在脚本或者解释器的一个交互式实例中使用。这样的文件被称为 *模块*；模块中的定义可以 *导入* 到另一个模块或 *主模块* 中（在脚本执行时可以调用的变量集位于最高级，并且处于计算器模式）。

模块是包括 Python 定义和声明的文件。文件名就是模块名加上 :file:`.py` 后缀。模块的模块名（做为一个字符串）可以由全局变量 ``__name__`` 得到。例如，你可以用自己惯用的文件编辑器在当前目录下创建一个叫 fibo.py 的文件，录入如下内容::

   # Fibonacci numbers module

   def fib(n):    # write Fibonacci series up to n
       a, b = 0, 1
       while b < n:
           print(b, end=' ')
           a, b = b, a+b
       print()

   def fib2(n): # return Fibonacci series up to n
       result = []
       a, b = 0, 1
       while b < n:
           result.append(b)
           a, b = b, a+b
       return result

现在进入 Python 解释器并使用以下命令导入这个模块::

   >>> import fibo

这样做不会直接把 ``fibo`` 中的函数导入当前的语义表；它只是引入了模块名 ``fibo``。你可以通过模块名按如下方式访问这个函数::

   >>> fibo.fib(1000)
   1 1 2 3 5 8 13 21 34 55 89 144 233 377 610 987
   >>> fibo.fib2(100)
   [1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89]
   >>> fibo.__name__
   'fibo'

如果打算频繁使用一个函数，你可以将它赋予一个本地变量::

   >>> fib = fibo.fib
   >>> fib(500)
   1 1 2 3 5 8 13 21 34 55 89 144 233 377


.. _tut-moremodules:

深入模块
===============

除了包含函数定义外，模块也可以包含可执行语句。这些语句一般用来初始化模块。他们仅在 *第一次* 被导入的地方执行一次。[#]_

每个模块都有自己私有的符号表，被模块内所有的函数定义作为全局符号表使用。因此，模块的作者可以在模块内部使用全局变量，而无需担心它与某个用户的全局变量意外冲突。从另一个方面讲，如果你确切的知道自己在做什么，你可以使用引用模块函数的表示法访问模块的全局变量，``modname.itemname``。

模块可以导入其他的模块。一个（好的）习惯是将所有的 `import`_ 语句放在模块的开始（或者是脚本），这并非强制。被导入的模块名会放入当前模块的全局符号表中。

`import`_ 语句的一个变体直接从被导入的模块中导入命名到本模块的语义表中。例如::

   >>> from fibo import fib, fib2
   >>> fib(500)
   1 1 2 3 5 8 13 21 34 55 89 144 233 377

这样不会从局域语义表中导入模块名（如上所示， ``fibo`` 没有定义）。 

甚至有种方式可以导入模块中的所有定义::

   >>> from fibo import *
   >>> fib(500)
   1 1 2 3 5 8 13 21 34 55 89 144 233 377

这样可以导入所有除了以下划线( ``_`` )开头的命名。 

需要注意的是在实践中往往不鼓励从一个模块或包中使用 ``*`` 导入所有，因为这样会让代码变得很难读。不过，在交互式会话中这样用很方便省力。

.. note::

   出于性能考虑，每个模块在每个解释器会话中只导入一遍。因此，如果你修改了你的模块，需要重启解释器；或者，如果你就是想交互式的测试这么一个模块，可以用 `imp.reload()`_ 重新加载，例如 ``import imp; imp.reload(modulename)``。


.. _tut-modulesasscripts:

作为脚本来执行模块
----------------------------

当你使用以下方式运行 Python 模块时，模块中的代码便会被执行::

   python fibo.py <arguments>

模块中的代码会被执行，就像导入它一样，不过此时 ``__name__`` 被设置为 ``"__main__"``。这相当于，如果你在模块后加入如下代码::

   if __name__ == "__main__":
       import sys
       fib(int(sys.argv[1]))

就可以让此文件像作为模块导入时一样作为脚本执行。此代码只有在模块作为 “main” 文件执行时才被调用::

   $ python fibo.py 50
   1 1 2 3 5 8 13 21 34

如果模块被导入，不会执行这段代码::

   >>> import fibo
   >>>

这通常用来为模块提供一个便于测试的用户接口（将模块作为脚本执行测试需求）。


.. _tut-searchpath:

模块的搜索路径
----------------------

.. index:: triple: module; search; path

导入一个叫 :mod:`spam` 的模块时，解释器先在当前目录中搜索名为 :file:`spam.py` 的文件。如果没有找到的话，接着会到 `sys.path`_ 变量中给出的目录列表中查找。 `sys.path`_ 变量的初始值来自如下：


* 输入脚本的目录（当前目录）。

* 环境变量 `PYTHONPATH`_ 表示的目录列表中搜索 
  
  (这和 shell 变量 :envvar:`PATH` 具有一样的语法，即一系列目录名的列表)。

* Python 默认安装路径中搜索。
  
  .. note::

     在支持符号连接的文件系统中，输入的脚本所在的目录是符号连接指向的目录。 换句话说也就是包含符号链接的目录不会被加到目录搜索路径中。

实际上，解释器由 `sys.path`_ 变量指定的路径目录搜索模块，该变量初始化时默认包含了输入脚本（或者当前目录）， `PYTHONPATH`_ 和安装目录。这样就允许 Python 程序了解如何修改或替换模块搜索目录。需要注意的是由于这些目录中包含有搜索路径中运行的脚本，所以这些脚本不应该和标准模块重名，否则在导入模块时 Python 会尝试把这些脚本当作模块来加载。这通常会引发错误。请参见 :ref:`tut-standardmodules` 以了解更多的信息。

.. %
    Do we need stuff on zip files etc. ? DUBOIS

“编译的” Python 文件
-----------------------

为了加快加载模块的速度，Python 会在 ``__pycache__`` 目录下以 :file:`module.{version}.pyc` 名字缓存每个模块编译后的版本，这里的版本编制了编译后文件的格式。它通常会包含 Python 的版本号。例如，在 CPython 3.3 版中，spam.py 编译后的版本将缓存为 ``__pycache__/spam.cpython-33.pyc``。这种命名约定允许由不同发布和不同版本的 Python 编译的模块同时存在。

Python 会检查源文件与编译版的修改日期以确定它是否过期并需要重新编译。这是完全自动化的过程。同时，编译后的模块是跨平台的，所以同一个库可以在不同架构的系统之间共享。

Python 不检查在两个不同环境中的缓存。首先，它会永远重新编译而且不会存储直接从命令行加载的模块。其次，如果没有源模块它不会检查缓存。若要支持没有源文件（只有编译版）的发布，编译后的模块必须在源目录下，并且必须没有源文件的模块。

部分高级技巧:

* 为了减少一个编译模块的大小，你可以在 Python 命令行中使用 `-O`_ 或者 `-OO`_。`-O`_ 参数删除了断言语句，`-OO`_ 参数删除了断言语句和 __doc__ 字符串。
  
  因为某些程序依赖于这些变量的可用性，你应该只在确定无误的场合使用这一选项。“优化的” 模块有一个 .pyo 后缀而不是 .pyc 后缀。未来的版本可能会改变优化的效果。

* 来自 :file:`.pyc` 文件或 :file:`.pyo` 文件中的程序不会比来自 :file:`.py` 文件的运行更快；:file:`.pyc` 或 :file:`.pyo` 文件只是在它们加载的时候更快一些。

* `compileall`_ 模块可以为指定目录中的所有模块创建 :file:`.pyc` 文件（或者使用 `-O`_ 参数创建 :file:`.pyo` 文件）。

* 在 PEP 3147 中有很多关这一部分内容的细节，并且包含了一个决策流程。


.. _tut-standardmodules:

标准模块
================

.. index:: module: sys

Python 带有一个标准模块库，并发布有独立的文档，名为 Python 库参考手册（此后称其为“库参考手册”）。有一些模块内置于解释器之中，这些操作的访问接口不是语言内核的一部分，但是已经内置于解释器了。这既是为了提高效率，也是为了给系统调用等操作系统原生访问提供接口。这类模块集合是一个依赖于底层平台的配置选项。例如，`winreg`_ 模块只提供在 Windows 系统上才有。有一个具体的模块值得注意： `sys`_ ，这个模块内置于所有的 Python 解释器。变量 ``sys.ps1`` 和 ``sys.ps2`` 定义了主提示符和辅助提示符字符串::

   >>> import sys
   >>> sys.ps1
   '>>> '
   >>> sys.ps2
   '... '
   >>> sys.ps1 = 'C> '
   C> print('Yuck!')
   Yuck!
   C>


这两个变量只在解释器的交互模式下有意义。 

变量 ``sys.path`` 是解释器模块搜索路径的字符串列表。它由环境变量 `PYTHONPATH`_ 初始化，如果没有设定 `PYTHONPATH`_ ，就由内置的默认值初始化。你可以用标准的字符串操作修改它::

   >>> import sys
   >>> sys.path.append('/ufs/guido/lib/python')


.. _tut-dir:

`dir()`_ 函数
========================

内置函数 `dir()`_ 用于按模块名搜索模块定义，它返回一个字符串类型的存储列表::

   >>> import fibo, sys
   >>> dir(fibo)
   ['__name__', 'fib', 'fib2']
   >>> dir(sys)  # doctest: +NORMALIZE_WHITESPACE
   ['__displayhook__', '__doc__', '__excepthook__', '__loader__', '__name__',
    '__package__', '__stderr__', '__stdin__', '__stdout__',
    '_clear_type_cache', '_current_frames', '_debugmallocstats', '_getframe',
    '_home', '_mercurial', '_xoptions', 'abiflags', 'api_version', 'argv',
    'base_exec_prefix', 'base_prefix', 'builtin_module_names', 'byteorder',
    'call_tracing', 'callstats', 'copyright', 'displayhook',
    'dont_write_bytecode', 'exc_info', 'excepthook', 'exec_prefix',
    'executable', 'exit', 'flags', 'float_info', 'float_repr_style',
    'getcheckinterval', 'getdefaultencoding', 'getdlopenflags',
    'getfilesystemencoding', 'getobjects', 'getprofile', 'getrecursionlimit',
    'getrefcount', 'getsizeof', 'getswitchinterval', 'gettotalrefcount',
    'gettrace', 'hash_info', 'hexversion', 'implementation', 'int_info',
    'intern', 'maxsize', 'maxunicode', 'meta_path', 'modules', 'path',
    'path_hooks', 'path_importer_cache', 'platform', 'prefix', 'ps1',
    'setcheckinterval', 'setdlopenflags', 'setprofile', 'setrecursionlimit',
    'setswitchinterval', 'settrace', 'stderr', 'stdin', 'stdout',
    'thread_info', 'version', 'version_info', 'warnoptions']

无参数调用时，`dir()`_ 函数返回当前定义的命名::

   >>> a = [1, 2, 3, 4, 5]
   >>> import fibo
   >>> fib = fibo.fib
   >>> dir()
   ['__builtins__', '__doc__', '__file__', '__name__', 'a', 'fib', 'fibo', 'sys']

注意该列表列出了所有类型的名称：变量，模块，函数，等等。

.. index:: module: builtins

`dir()`_ 不会列出内置函数和变量名。如果你想列出这些内容，它们在标准模块 `builtins`_ 中定义::


   >>> import builtins
   >>> dir(builtins)  # doctest: +NORMALIZE_WHITESPACE
   ['ArithmeticError', 'AssertionError', 'AttributeError', 'BaseException',
    'BlockingIOError', 'BrokenPipeError', 'BufferError', 'BytesWarning',
    'ChildProcessError', 'ConnectionAbortedError', 'ConnectionError',
    'ConnectionRefusedError', 'ConnectionResetError', 'DeprecationWarning',
    'EOFError', 'Ellipsis', 'EnvironmentError', 'Exception', 'False',
    'FileExistsError', 'FileNotFoundError', 'FloatingPointError',
    'FutureWarning', 'GeneratorExit', 'IOError', 'ImportError',
    'ImportWarning', 'IndentationError', 'IndexError', 'InterruptedError',
    'IsADirectoryError', 'KeyError', 'KeyboardInterrupt', 'LookupError',
    'MemoryError', 'NameError', 'None', 'NotADirectoryError', 'NotImplemented',
    'NotImplementedError', 'OSError', 'OverflowError',
    'PendingDeprecationWarning', 'PermissionError', 'ProcessLookupError',
    'ReferenceError', 'ResourceWarning', 'RuntimeError', 'RuntimeWarning',
    'StopIteration', 'SyntaxError', 'SyntaxWarning', 'SystemError',
    'SystemExit', 'TabError', 'TimeoutError', 'True', 'TypeError',
    'UnboundLocalError', 'UnicodeDecodeError', 'UnicodeEncodeError',
    'UnicodeError', 'UnicodeTranslateError', 'UnicodeWarning', 'UserWarning',
    'ValueError', 'Warning', 'ZeroDivisionError', '_', '__build_class__',
    '__debug__', '__doc__', '__import__', '__name__', '__package__', 'abs',
    'all', 'any', 'ascii', 'bin', 'bool', 'bytearray', 'bytes', 'callable',
    'chr', 'classmethod', 'compile', 'complex', 'copyright', 'credits',
    'delattr', 'dict', 'dir', 'divmod', 'enumerate', 'eval', 'exec', 'exit',
    'filter', 'float', 'format', 'frozenset', 'getattr', 'globals', 'hasattr',
    'hash', 'help', 'hex', 'id', 'input', 'int', 'isinstance', 'issubclass',
    'iter', 'len', 'license', 'list', 'locals', 'map', 'max', 'memoryview',
    'min', 'next', 'object', 'oct', 'open', 'ord', 'pow', 'print', 'property',
    'quit', 'range', 'repr', 'reversed', 'round', 'set', 'setattr', 'slice',
    'sorted', 'staticmethod', 'str', 'sum', 'super', 'tuple', 'type', 'vars',
    'zip']

.. _tut-packages:

包
========

包通常是使用用“圆点模块名”的结构化模块命名空间。例如，名为 :mod:`A.B` 的模块表示了名为 ``A`` 的包中名为 ``B`` 的子模块。正如同用模块来保存不同的模块架构可以避免全局变量之间的相互冲突，使用圆点模块名保存像 NumPy 或 Python Imaging Library 之类的不同类库架构可以避免模块之间的命名冲突。 

假设你现在想要设计一个模块集（一个“包”）来统一处理声音文件和声音数据。存在几种不同的声音格式（通常由它们的扩展名来标识，例如：:file:`.wav`，
:file:`.aiff`，:file:`.au` ），于是，为了在不同类型的文件格式之间转换，你需要维护一个不断增长的包集合。可能你还想要对声音数据做很多不同的操作（例如混音，添加回声，应用平衡 功能，创建一个人造效果），所以你要加入一个无限流模块来执行这些操作。你的包可能会是这个样子（通过分级的文件体系来进行分组）:

.. code-block:: text

   sound/                          Top-level package
         __init__.py               Initialize the sound package
         formats/                  Subpackage for file format conversions
                 __init__.py
                 wavread.py
                 wavwrite.py
                 aiffread.py
                 aiffwrite.py
                 auread.py
                 auwrite.py
                 ...
         effects/                  Subpackage for sound effects
                 __init__.py
                 echo.py
                 surround.py
                 reverse.py
                 ...
         filters/                  Subpackage for filters
                 __init__.py
                 equalizer.py
                 vocoder.py
                 karaoke.py
                 ...

当导入这个包时，Python 通过 ``sys.path`` 搜索路径查找包含这个包的子目录。

为了让 Python 将目录当做内容包，目录中必须包含 :file:`__init__.py` 文件。这是为了避免一个含有烂俗名字的目录无意中隐藏了稍后在模块搜索路径中出现的有效模块，比如 string。最简单的情况下，只需要一个空的 :file:`__init__.py` 文件即可。当然它也可以执行包的初始化代码，或者定义稍后介绍的 ``__all__`` 变量。

用户可以每次只导入包里的特定模块，例如::

   import sound.effects.echo

这样就导入了 :mod:`sound.effects.echo` 子模块。它必需通过完整的名称来引用::

   sound.effects.echo.echofilter(input, output, delay=0.7, atten=4)

导入包时有一个可以选择的方式::

   from sound.effects import echo

这样就加载了 :mod:`echo` 子模块，并且使得它在没有包前缀的情况下也可以使用，所以它可以如下方式调用::

   echo.echofilter(input, output, delay=0.7, atten=4)

还有另一种变体用于直接导入函数或变量::

   from sound.effects.echo import echofilter

这样就又一次加载了 :mod:`echo` 子模块，但这样就可以直接调用它的 :func:`echofilter` 函数::

   echofilter(input, output, delay=0.7, atten=4)

需要注意的是使用 ``from package import item`` 方式导入包时，这个子项（item）既可以是包中的一个子模块（或一个子包），也可以是包中定义的其它命名，像函数、类或变量。``import`` 语句首先核对是否包中有这个子项，如果没有，它假定这是一个模块，并尝试加载它。如果没有找到它，会引发一个  `ImportError`_ 异常。 

相反，使用类似 ``import item.subitem.subsubitem`` 这样的语法时，这些子项必须是包，最后的子项可以是包或模块，但不能是前面子项中定义的类、函数或变量。


.. _tut-pkg-import-star:

从 \* 导入包
---------------------------

.. index:: single: __all__

那么当用户写下 ``from sound.Effects import *`` 时会发生什么事？理想中，总是希望在文件系统中找出包中所有的子模块，然后导入它们。这可能会花掉委有长时间，并且出现期待之外的边界效应，导出了希望只能显式导入的包。 

对于包的作者来说唯一的解决方案就是给提供一个明确的包索引。`import`_ 语句按如下条件进行转换：执行 ``from package import *`` 时，如果包中的 :file:`__init__.py` 代码定义了一个名为 ``__all__`` 的列表，就会按照列表中给出的模块名进行导入。新版本的包发布时作者可以任意更新这个列表。如果包作者不想 import \* 的时候导入他们的包中所有模块，那么也可能会决定不支持它（ import \* ）。例如， :file:`sound/effects/__init__.py` 这个文件可能包括如下代码::

   __all__ = ["echo", "surround", "reverse"]

这意味着 ``from Sound.Effects import *`` 语句会从 :mod:`sound` 包中导入以上三个已命名的子模块。 

如果没有定义 ``__all__`` ， ``from Sound.Effects import *`` 语句 *不会* 从 :mod:`sound.effects` 包中导入所有的子模块。无论包中定义多少命名，只能确定的是导入了 :mod:`sound.effects`  包（可能会运行 :file:`__init__.py` 中的初始化代码）以及包中定义的所有命名会随之导入。这样就从 :file:`__init__.py` 中导入了每一个命名（以及明确导入的子模块）。同样也包括了前述的 `import`_ 语句从包中明确导入的子模块，考虑以下代码::

   import sound.effects.echo
   import sound.effects.surround
   from sound.effects import *

在这个例子中，:mod:`echo` 和 :mod:`surround` 模块导入了当前的命名空间，这是因为执行 ``from...import`` 语句时它们已经定义在 :mod:`sound.effects` 包中了（定义了 ``__all__`` 时也会同样工作）。 

尽管某些模块设计为使用 ``import *`` 时它只导出符全某种模式的命名，仍然不建议在生产代码中使用这种写法。 

记住，``from Package import specific_submodule`` 没有错误！事实上，除非导入的模块需要使用其它包中的同名子模块，否则这是推荐的写法。


包内引用
------------------------

如果包中使用了子包结构（就像示例中的 :mod:`sound` 包），可以按绝对位置从相邻的包中引入子模块。例如，如果 :mod:`sound.filters.vocoder` 包需要使用 :mod:`sound.effects` 包中的 :mod:`echo` 模块，它可以 ``from Sound.Effects import echo``。 

你可以用这样的形式 ``from module import name`` 来写显式的相对位置导入。那些显式相对导入用点号标明关联导入当前和上级包。以 :mod:`surround` 模块为例，你可以这样用::

   from . import echo
   from .. import formats
   from ..filters import equalizer

需要注意的是显式或隐式相对位置导入都基于当前模块的命名。因为主模块的名字总是 ``"__main__"``，Python 应用程序的主模块应该总是用绝对导入。


多重目录中的包
--------------------------------

包支持一个更为特殊的特性， `__path__ <https://docs.python.org/3/reference/import.html#__path__>`_。 在包的 :file:`__init__.py` 文件代码执行之前，该变量初始化一个目录名列表。该变量可以修改，它作用于包中的子包和模块的搜索功能。 

这个功能可以用于扩展包中的模块集，不过它不常用。


.. rubric:: Footnotes

.. [#] 事实上函数定义既是“声明”又是“可执行体”；执行体由函数在模块全局语义表中的命名导入。



.. _import: https://docs.python.org/3/reference/simple_stmts.html#import
.. _imp.reload(): https://docs.python.org/3/library/imp.html#imp.reload
.. _sys.path: https://docs.python.org/3/library/sys.html#sys.path
.. _PYTHONPATH: https://docs.python.org/3/using/cmdline.html#envvar-PYTHONPATH
.. _-O: https://docs.python.org/3/using/cmdline.html#cmdoption-O
.. _-OO: https://docs.python.org/3/using/cmdline.html#cmdoption-OO
.. _compileall: https://docs.python.org/3/library/compileall.html#module-compileall
.. _winreg: https://docs.python.org/3/library/winreg.html#module-winreg
.. _sys: https://docs.python.org/3/library/sys.html#module-sys
.. _dir(): https://docs.python.org/3/library/functions.html#dir
.. _builtins: https://docs.python.org/3/library/builtins.html#module-builtins
.. _ImportError: https://docs.python.org/3/library/exceptions.html#ImportError