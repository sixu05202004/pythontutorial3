.. _tut-errors:

*********************
错误和异常
*********************

至今为止还没有进一步的谈论过错误信息，不过在你已经试验过的那些例子中，可能已经遇到过一些。Python 中（至少）有两种错误：语法错误和异常（ *syntax errors* 和 *exceptions* ）。


.. _tut-syntaxerrors:

语法错误
=============

语法错误，也被称作解析错误，也许是你学习 Python 过程中最常见抱怨::

   >>> while True print('Hello world')
     File "<stdin>", line 1, in ?
       while True print('Hello world')
                      ^
   SyntaxError: invalid syntax

语法分析器指出错误行，并且在检测到错误的位置前面显示一个小“箭头”。 错误是由箭头 *前面* 的标记引起的（或者至少是这么检测的）： 这个例子中，函数 `print()`_ 被发现存在错误，因为它前面少了一个冒号（ ``':'`` ）。 错误会输出文件名和行号，所以如果是从脚本输入的你就知道去哪里检查错误了。


.. _tut-exceptions:

异常
==========

即使一条语句或表达式在语法上是正确的，当试图执行它时也可能会引发错误。运行期检测到的错误称为 *异常*，并且程序不会无条件的崩溃：很快，你将学到如何在 Python 程序中处理它们。然而，大多数异常都不会被程序处理，像这里展示的一样最终会产生一个错误信息::

   >>> 10 * (1/0)
   Traceback (most recent call last):
     File "<stdin>", line 1, in ?
   ZeroDivisionError: int division or modulo by zero
   >>> 4 + spam*3
   Traceback (most recent call last):
     File "<stdin>", line 1, in ?
   NameError: name 'spam' is not defined
   >>> '2' + 2
   Traceback (most recent call last):
     File "<stdin>", line 1, in ?
   TypeError: Can't convert 'int' object to str implicitly

错误信息的最后一行指出发生了什么错误。异常也有不同的类型，异常类型做为错误信息的一部分显示出来：示例中的异常分别为 零除错误（ `ZeroDivisionError`_ ） ，命名错误（ `NameError`_） 和 类型错误（ `TypeError`_ ）。打印错误信息时，异常的类型作为异常的内置名显示。对于所有的内置异常都是如此，不过用户自定义异常就不一定了（尽管这是一个很有用的约定）。标准异常名是内置的标识（没有保留关键字）。 

这一行后一部分是关于该异常类型的详细说明，这意味着它的内容依赖于异常类型。

错误信息的前半部分以堆栈的形式列出异常发生的位置。通常在堆栈中列出了源代码行，然而，来自标准输入的源码不会显示出来。 

`内置的异常`_ 列出了内置异常和它们的含义。


.. _tut-handling:

异常处理
===================

通过编程处理选择的异常是可行的。看一下下面的例子：它会一直要求用户输入，直到输入一个合法的整数为止，但允许用户中断这个程序（使用 :kbd:`Control-C` 或系统支持的任何方法）。注意：用户产生的中断会引发一个 `KeyboardInterrupt`_ 异常。 ::

   >>> while True:
   ...     try:
   ...         x = int(input("Please enter a number: "))
   ...         break
   ...     except ValueError:
   ...         print("Oops!  That was no valid number.  Try again...")
   ...

`try`_ 语句按如下方式工作。

* 首先，执行 *try* 子句 （在 `try`_ 和 `except`_ 关键字之间的部分）。

* 如果没有异常发生， *except* 子句 在 `try`_ 语句执行完毕后就被忽略了。

* 如果在 try 子句执行过程中发生了异常，那么该子句其余的部分就会被忽略。
  
  如果异常匹配于 `except`_ 关键字后面指定的异常类型，就执行对应的except子句。然后继续执行 `try`_ 语句之后的代码。

* 如果发生了一个异常，在 `except`_ 子句中没有与之匹配的分支，它就会传递到上一级 `try`_  语句中。
  
  如果最终仍找不到对应的处理语句，它就成为一个 *未处理异常*，终止程序运行，显示提示信息。

一个 `try`_ 语句可能包含多个 except 子句，分别指定处理不同的异常。至多只会有一个分支被执行。异常处理程序只会处理对应的 try 子句中发生的异常，在同一个 `try`_ 语句中，其他子句中发生的异常则不作处理。一个 except 子句可以在括号中列出多个异常的名字，例如::

   ... except (RuntimeError, TypeError, NameError):
   ...     pass

最后一个 except 子句可以省略异常名称，以作为通配符使用。你需要慎用此法，因为它会轻易隐藏一个实际的程序错误！可以使用这种方法打印一条错误信息，然后重新抛出异常（允许调用者处理这个异常)::

   import sys

   try:
       f = open('myfile.txt')
       s = f.readline()
       i = int(s.strip())
   except OSError as err:
       print("OS error: {0}".format(err))
   except ValueError:
       print("Could not convert data to an integer.")
   except:
       print("Unexpected error:", sys.exc_info()[0])
       raise

`try`_ ... `except`_ 语句可以带有一个 *else子句*，该子句只能出现在所有 except 子句之后。当 try 语句没有抛出异常时，需要执行一些代码，可以使用这个子句。例如::

   for arg in sys.argv[1:]:
       try:
           f = open(arg, 'r')
       except IOError:
           print('cannot open', arg)
       else:
           print(arg, 'has', len(f.readlines()), 'lines')
           f.close()

使用 `else`_ 子句比在 `try`_ 子句中附加代码要好，因为这样可以避免 `try`_ ... `except`_ 意外的截获本来不属于它们保护的那些代码抛出的异常。 

发生异常时，可能会有一个附属值，作为异常的 *参数* 存在。这个参数是否存在、是什么类型，依赖于异常的类型。 

在异常名（列表）之后，也可以为 except 子句指定一个变量。这个变量绑定于一个异常实例，它存储在 ``instance.args`` 的参数中。为了方便起见，异常实例定义了 `__str__() <https://docs.python.org/3/reference/datamodel.html#object.__str__>`_ ，这样就可以直接访问过打印参数而不必引用 ``.args``。这种做法不受鼓励。相反，更好的做法是给异常传递一个参数（如果要传递多个参数，可以传递一个元组），把它绑定到 message 属性。一旦异常发生，它会在抛出前绑定所有指定的属性。 ::

   >>> try:
   ...    raise Exception('spam', 'eggs')
   ... except Exception as inst:
   ...    print(type(inst))    # the exception instance
   ...    print(inst.args)     # arguments stored in .args
   ...    print(inst)          # __str__ allows args to be printed directly,
   ...                         # but may be overridden in exception subclasses
   ...    x, y = inst.args     # unpack args
   ...    print('x =', x)
   ...    print('y =', y)
   ...
   <class 'Exception'>
   ('spam', 'eggs')
   ('spam', 'eggs')
   x = spam
   y = eggs

对于那些未处理的异常，如果一个它们带有参数，那么就会被作为异常信息的最后部分（“详情”）打印出来。

异常处理器不仅仅处理那些在 try 子句中立刻发生的异常，也会处理那些 try 子句中调用的函数内部发生的异常。例如::

   >>> def this_fails():
   ...     x = 1/0
   ...
   >>> try:
   ...     this_fails()
   ... except ZeroDivisionError as err:
   ...     print('Handling run-time error:', err)
   ...
   Handling run-time error: int division or modulo by zero


.. _tut-raising:

抛出异常
==================

`raise`_ 语句允许程序员强制抛出一个指定的异常。例如::

   >>> raise NameError('HiThere')
   Traceback (most recent call last):
     File "<stdin>", line 1, in ?
   NameError: HiThere

要抛出的异常由 `raise`_ 的唯一参数标识。它必需是一个异常实例或异常类（继承自 `Exception`_ 的类）。

如果你需要明确一个异常是否抛出，但不想处理它，`raise`_ 语句可以让你很简单的重新抛出该异常::

   >>> try:
   ...     raise NameError('HiThere')
   ... except NameError:
   ...     print('An exception flew by!')
   ...     raise
   ...
   An exception flew by!
   Traceback (most recent call last):
     File "<stdin>", line 2, in ?
   NameError: HiThere


.. _tut-userexceptions:

用户自定义异常
=======================

在程序中可以通过创建新的异常类型来命名自己的异常（Python 类的内容请参见 :ref:`tut-classes` ）。异常类通常应该直接或间接的从 `Exception`_ 类派生，例如::

   >>> class MyError(Exception):
   ...     def __init__(self, value):
   ...         self.value = value
   ...     def __str__(self):
   ...         return repr(self.value)
   ...
   >>> try:
   ...     raise MyError(2*2)
   ... except MyError as e:
   ...     print('My exception occurred, value:', e.value)
   ...
   My exception occurred, value: 4
   >>> raise MyError('oops!')
   Traceback (most recent call last):
     File "<stdin>", line 1, in ?
   __main__.MyError: 'oops!'

在这个例子中，`Exception`_ 默认的 `__init__() <https://docs.python.org/3/reference/datamodel.html#object.__init__>`_ 被覆盖。新的方式简单的创建 *value* 属性。这就替换了原来创建 *args* 属性的方式。 

异常类中可以定义任何其它类中可以定义的东西，但是通常为了保持简单，只在其中加入几个属性信息，以供异常处理句柄提取。如果一个新创建的模块中需要抛出几种不同的错误时，一个通常的作法是为该模块定义一个异常基类，然后针对不同的错误类型派生出对应的异常子类::

   class Error(Exception):
       """Base class for exceptions in this module."""
       pass

   class InputError(Error):
       """Exception raised for errors in the input.

       Attributes:
           expression -- input expression in which the error occurred
           message -- explanation of the error
       """

       def __init__(self, expression, message):
           self.expression = expression
           self.message = message

   class TransitionError(Error):
       """Raised when an operation attempts a state transition that's not
       allowed.

       Attributes:
           previous -- state at beginning of transition
           next -- attempted new state
           message -- explanation of why the specific transition is not allowed
       """

       def __init__(self, previous, next, message):
           self.previous = previous
           self.next = next
           self.message = message

与标准异常相似，大多数异常的命名都以 “Error” 结尾。

很多标准模块中都定义了自己的异常，用以报告在他们所定义的函数中可能发生的错误。关于类的进一步信息请参见 :ref:`tut-classes` 一章。


.. _tut-cleanup:

定义清理行为
=========================

`try`_ 语句还有另一个可选的子句，目的在于定义在任何情况下都一定要执行的功能。例如::

   >>> try:
   ...     raise KeyboardInterrupt
   ... finally:
   ...     print('Goodbye, world!')
   ...
   Goodbye, world!
   Traceback (most recent call last):
     File "<stdin>", line 2, in ?
   KeyboardInterrupt

不管有没有发生异常，*finally子句* 在程序离开 `try`_ 后都一定会被执行。当 `try`_ 语句中发生了未被 `except`_ 捕获的异常（或者它发生在 `except`_ 或 `else`_ 子句中），在 `finally`_ 子句执行完后它会被重新抛出。 `try`_ 语句经由 `break`_ ，`continue`_ 或 `return`_ 语句退 出也一样会执行 `finally`_ 子句。以下是一个更复杂些的例子::

   >>> def divide(x, y):
   ...     try:
   ...         result = x / y
   ...     except ZeroDivisionError:
   ...         print("division by zero!")
   ...     else:
   ...         print("result is", result)
   ...     finally:
   ...         print("executing finally clause")
   ...
   >>> divide(2, 1)
   result is 2
   executing finally clause
   >>> divide(2, 0)
   division by zero!
   executing finally clause
   >>> divide("2", "1")
   executing finally clause
   Traceback (most recent call last):
     File "<stdin>", line 1, in ?
     File "<stdin>", line 3, in divide
   TypeError: unsupported operand type(s) for /: 'str' and 'str'

如你所见， `finally`_ 子句在任何情况下都会执行。`TypeError`_ 在两个字符串相除的时候抛出，未被 except 子句捕获，因此在 `finally`_ 子句执行完毕后重新抛出。 

在真实场景的应用程序中，`finally`_ 子句用于释放外部资源（文件 或网络连接之类的），无论它们的使用过程中是否出错。


.. _tut-cleanup-with:

预定义清理行为
===========================

有些对象定义了标准的清理行为，无论对象操作是否成功，不再需要该对象的时候就会起作用。以下示例尝试打开文件并把内容打印到屏幕上。 ::

   for line in open("myfile.txt"):
       print(line)

这段代码的问题在于在代码执行完后没有立即关闭打开的文件。这在简单的脚本里没什么，但是大型应用程序就会出问题。`with`_ 语句使得文件之类的对象可以 确保总能及时准确地进行清理。 ::

   with open("myfile.txt") as f:
       for line in f:
           print(line)

语句执行后，文件 *f* 总会被关闭，即使是在处理文件中的数据时出错也一样。其它对象是否提供了预定义的清理行为要查看它们的文档。



.. _print(): https://docs.python.org/3/library/functions.html#print
.. _ZeroDivisionError: https://docs.python.org/3/library/exceptions.html#ZeroDivisionError
.. _NameError: https://docs.python.org/3/library/exceptions.html#NameError
.. _TypeError: https://docs.python.org/3/library/exceptions.html#TypeError
.. _内置的异常: https://docs.python.org/3/library/exceptions.html#bltin-exceptions
.. _KeyboardInterrupt: https://docs.python.org/3/library/exceptions.html#KeyboardInterrupt
.. _try: https://docs.python.org/3/reference/compound_stmts.html#try
.. _except: https://docs.python.org/3/reference/compound_stmts.html#except
.. _else: https://docs.python.org/3/reference/compound_stmts.html#else
.. _raise: https://docs.python.org/3/reference/simple_stmts.html#raise
.. _Exception: https://docs.python.org/3/library/exceptions.html#Exception
.. _finally: https://docs.python.org/3/reference/compound_stmts.html#finally
.. _break: https://docs.python.org/3/reference/simple_stmts.html#break
.. _continue: https://docs.python.org/3/reference/simple_stmts.html#continue
.. _return: https://docs.python.org/3/reference/simple_stmts.html#return
.. _with: https://docs.python.org/3/reference/compound_stmts.html#with
