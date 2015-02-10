.. _tut-interacting:

**************************************************
交互式输入行编辑历史回溯
**************************************************

有些版本的 Python 解释器支持输入行编辑和历史回溯，类似 Korn shell 和 GNU bash shell 的功能。这是通过 `GNU Readline`_ 库实现的。它支持 Emacs 风格和 vi 风格的编辑。这个库有它自己的文档，在此不重复了。不过，基本的东西很容易演示。交互式编辑和历史查阅在 Unix 和 Cygwin 版中是可选项。 

本章 *不是* 马克 哈密尔顿的 PythonWin 包和随 Python 发布的基于 TK 的 IDLE 环境的文档。 NT 系统和其它 DOS、Windows 系统上的 DOS 窗中的命令行历史回调，属于另一个话题。


.. _tut-lineediting:

行编辑
============

如果支持，无论解释器打印主提示符或从属提示符，行编辑都会激活。当前行可以用 Emacs 风格的快捷键编辑。其中最重要的是： :kbd:`C-A` （Control-A）将光标移动到行首，　:kbd:`C-E` 移动到行尾， :kbd:`C-B` 向左移一个字符， :kbd:`C-F` 向右移一位。退格向左删除一个符串， :kbd:`C-D` 向右删除一个字符。 :kbd:`C-K` 删掉光标右边直到行尾的所有字符， :kbd:`C-Y` 将最后一次删除的字符串粘贴到光标位置。 :kbd:`C-underscore` （underscores 即下划线，译注）撤销最后一次修改，它可以因积累作用重复。


.. _tut-history:

历史回溯
====================

历史代替可以工作。所有非空的输入行都被保存在历史缓存中，获得一个新的提示符的时候，你处于这个缓存的最底的空行。 :kbd:`C-P` 在历史缓存中上溯一行， :kbd:`C-N` 向下移一行。历史缓存中的任一行都可以编辑；按下　:kbd:`Return` 键时将当前行传入解释器。 :kbd:`C-R` 开始一个增量向前搜索；:kbd:`C-S` 开始一个向后搜索。


.. _tut-keybindings:

快捷键绑定
============

Readline 库的快捷键绑定和其它一些参数可以通过名为 :file:`~/.inputrc`  的初始化文件的替换命名来定制。快捷键绑定如下形式::

   key-name: function-name

或者::

   "string": function-name

选项可以如下设置::

   set option-name value

例如::

   # I prefer vi-style editing:
   set editing-mode vi

   # Edit using a single line:
   set horizontal-scroll-mode On

   # Rebind some keys:
   Meta-h: backward-kill-word
   "\C-u": universal-argument
   "\C-x\C-r": re-read-init-file

需要注意的是 Python 中默认 :kbd:`Tab` 绑定为插入一个 :kbd:`Tab` 字符而不是 Readline 库的默认文件名完成函数，如果你想用这个，可以将以下内容插入::

   Tab: complete

到你的 :file:`~/.inputrc` 中来覆盖它。（当然，如果你真的把 :kbd:`Tab`  设置成这样，就很难在后继行中插入缩进。）

.. index::
   module: rlcompleter
   module: readline

自动完成变量和模块名也可以激活生效。要使之在解释器交互模式中可用，在你的启动文件中加入下面内容: [#]_ ::

   import rlcompleter, readline
   readline.parse_and_bind('tab: complete')

这个操作将 :kbd:`Tab` 绑定到完成函数，故按 Tab 键两次会给出建议的完成内容；它查找　Python 命名、当前的局部变量、有效的模块名。对于类似 ``string.a`` 这样的文件名，它会解析 ``'.'`` 相关的表达式，从返回的结果对象中获取属性，以提供完成建议。需要注意的是，如果对象的 :meth:`__getattr__` 方法是此表达式的一部分，这可能会执行应用程序定义代码。 

更有用的初始化文件可能是下面这个例子这样的。要注意一旦创建的名字没用了，它会删掉它们；因为初始化文件作为解释命令与之在同一个命名空间执行，在交互环境中删除命名带来了边际效应。可能你发现了它体贴的保留了一些导入模块，类似 :mod:`os` ，在解释器的大多数使用场合中都会用到它们。 ::

   # Add auto-completion and a stored history file of commands to your Python
   # interactive interpreter. Requires Python 2.0+, readline. Autocomplete is
   # bound to the Esc key by default (you can change it - see readline docs).
   #
   # Store the file in ~/.pystartup, and set an environment variable to point
   # to it:  "export PYTHONSTARTUP=~/.pystartup" in bash.

   import atexit
   import os
   import readline
   import rlcompleter

   historyPath = os.path.expanduser("~/.pyhistory")

   def save_history(historyPath=historyPath):
       import readline
       readline.write_history_file(historyPath)

   if os.path.exists(historyPath):
       readline.read_history_file(historyPath)

   atexit.register(save_history)
   del os, atexit, readline, rlcompleter, save_history, historyPath


.. _tut-commentary:

其它交互式解释器
===========================================

跟早先版本的解释器比，现在已经有了很大的进步。不过，还是有些期待没有完成：它应该在后继行中优美的提供缩进（解释器知道下一行是否需要缩进）建议。完成机制可以使用解释器的符号表。命名检查（或进一步建议）匹配括号、引号等等。 

另有一个强化交互式解释器已经存在一段时间了，它就是 IPython_，它支持 tab 完成，对象浏览和高级历史管理。它也可以完全定制或嵌入到其它应用程序中。另一个类似的强化交互环境是　bpython_ 。


.. rubric:: Footnotes

.. [#] 启动交互解释器时，Python 可以执行 :envvar:`PYTHONSTARTUP` 环境变量所指定的文件内容。

.. _GNU Readline: http://tiswww.case.edu/php/chet/readline/rltop.html
.. _IPython: http://ipython.scipy.org/
.. _bpython: http://www.bpython-interpreter.org/
