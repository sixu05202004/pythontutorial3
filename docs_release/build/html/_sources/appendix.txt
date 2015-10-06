.. _tut-appendix:

********
附录
********


.. _tut-interac:

交互模式
================

.. _tut-error:

错误处理
--------------

当错误发生时，解释器打印一个错误信息和堆栈跟踪。在交互模式下，它返回主提示符；当输入来自文件的时候，在打印堆栈跟踪后以非零退出状态退出。（在 `try <https://docs.python.org/3/reference/compound_stmts.html#try>`_ 声明中被 `except <https://docs.python.org/3/reference/compound_stmts.html#except>`_ 子句捕捉到的异常在这种情况下不是错误。）有些错误是非常致命的会导致一个非零状态的退出；这也适用于内部错误以及某些情况的内存耗尽。所有的错误信息都写入到标准错误流；来自执行的命令的普通输出写入到标准输出。

输入中断符（通常是 Control-C 或者 DEL）到主或者从提示符中慧取消输入并且返回到主提示。[#]_ 当命令执行中输入中断符会引起 `KeyboardInterrupt <https://docs.python.org/3/library/exceptions.html#KeyboardInterrupt>`_ 异常，这个异常能够被一个 `try <https://docs.python.org/3/reference/compound_stmts.html#try>`_ 声明处理。


.. _tut-scripts:

可执行 Python 脚本
-------------------------

在 BSD'ish Unix 系统上，Python 脚本可直接执行，像 shell 脚本一样，只需要把下面内容加入到 ::

   #!/usr/bin/env python3.5

（假设 python 解释器在用户的 :envvar:`PATH` 中）脚本的开头，并给予该文件的可执行模式。``#!`` 必须是文件的头两个字符。在一些系统上，第一行必须以 Unix-style 的行结束符（``'\n'``）结束，不能以 Windows 的行结束符（``'\r\n'``）。 注意 ``'#'`` 在 Python 中是用于注释的。

使用 :program:`chmod` 命令能够给予脚本执行模式或者权限。

.. code-block:: bash

   $ chmod +x myscript.py

在 Windows 系统上，没有一个 “可执行模式” 的概念。Python 安装器会自动地把 ``.py``  文件和 ``python.exe`` 关联起来，因此双击 Python 分拣将会把它当成一个脚本运行。文件扩展名也可以是 ``.pyw``，在这种情况下，运行时不会出现控制台窗口。


.. _tut-startup:

交互式启动文件
----------------------------

当你使用交互式 Python 的时候，它常常很方便地执行一些命令在每次解释器启动时。你可以这样做：设置一个名为 `PYTHONSTARTUP <https://docs.python.org/3/using/cmdline.html#envvar-PYTHONSTARTUP>`_ 的环境变量为包含你的启动命令的文件名。这跟 Unix shells 的 :file:`.profile` 特点有些类似。

这个文件在交互式会话中是只读的，在当 Python 从脚本中读取命令，以及在当 :file:`/dev/tty` 被作为明确的命令源的时候不只是可读的。该文件在交互式命令被执行的时候在相同的命名空间中能够被执行，因此在交互式会话中定义或者导入的对象能够无需授权就能使用。你也能在文件中更改提示 ``sys.ps1`` 和 ``sys.ps2``。

如果你想要从当前目录中读取一个附加的启动文件，你可以在全局启动文件中编写代码像这样：``if
os.path.isfile('.pythonrc.py'): exec(open('.pythonrc.py').read())``。如果你想要在脚本中使用启动文件的话，你必须在脚本中明确要这么做::

   import os
   filename = os.environ.get('PYTHONSTARTUP')
   if filename and os.path.isfile(filename):
       with open(filename) as fobj:
          startup_file = fobj.read()
       exec(startup_file)


.. _tut-customize:

定制模块
-------------------------

Python 提供两个钩子为了让你们定制 :mod:`sitecustomize` 和 :mod:`usercustomize`。为了看看它的工作机制的话，你必须首先找到你的用户 site-packages 目录的位置。启动 Python 并且运行这段代码::

   >>> import site
   >>> site.getusersitepackages()
   '/home/user/.local/lib/python3.4/site-packages'

现在你可以创建一个名为 :file:`usercustomize.py` 的文件在你的用户 site-packages 目录，并且在里面放置你想要的任何内容。它会影响 Python 的每一次调用，除非它以 `-s <https://docs.python.org/3/using/cmdline.html#cmdoption-s>`_ （禁用自动导入）选项启动。

:mod:`sitecustomize` 以同样地方式工作，但是通常由是机器的管理员创建在全局的 site-packages 目录中，并且是在 :mod:`usercustomize` 之前导入。请参阅 `site <https://docs.python.org/3/library/site.html#module-site>`_ 模块获取更多信息。


.. rubric:: Footnotes

.. [#] GNU 的 Readline 包的问题可能会阻止这种做法。
