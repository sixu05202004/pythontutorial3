.. _tut-using:

****************************
使用 Python 解释器
****************************


.. _tut-invoking:

调用 Python 解释器
========================

Python 解释器通常被安装在目标机器的 :file:`/usr/local/bin/python3.4` 目录下。 将 :file:`/usr/local/bin` 目录包含进 Unix shell 的搜索路径里，以确保可以通过输入::

   python3.4

命令来启动他。 [#]_ 由于 Python 解释器的安装路径是可选的，这也可能是其它路径，你可以联系安装 Python 的用户或系统管理员确认。 （例如， :file:`/usr/local/python` 就是一个常见的选择）

在 Windows 机器上，Python 通常安装在 :file:`C:\\Python34` 位置，当然你可以在运行安装向导时修改此值。 要想把此目录添加到你的 PATH 环境变量中，你可以在 DOS 窗口中输入以下命令::

   set path=%path%;C:\python33

通常你可以在主窗口输入一个文件结束符（Unix 系统是 :kbd:`Control-D` ，Windows 系统是 :kbd:`Control-Z` ）让解释器以 0 状态码退出。 如果那没有作用，你可以通过输入 ``quit()``  命令退出解释器。

Python 解释器具有简单的行编辑功能。 在 Unix 系统上，任何 Python 解释器都可能已经添加了 GNU readline 库支持，这样就具备了精巧的交互编辑和历史记录等功能。 在 Python 主窗口中输入 Control-P 可能是检查是否支持命令行编辑的最简单的方法。 如果发出嘟嘟声（计算机扬声器），则说明你可以使用命令行编辑功能；更多快捷键的介绍请参考  :ref:`tut-interacting` 。 如果没有任何声音，或者显示 ``^P`` 字符，则说明命令行编辑功能不可用；你只能通过退格键从当前行删除已键入的字符并重新输入。

Python 解释器有些操作类似 Unix shell： 当使用终端设备（tty）作为标准输入调用时，它交互的解释并执行命令； 当使用文件名参数或以文件作为标准输入调用时，它读取文件并将文件作为 *脚本* 执行。

第二种启动 Python 解释器的方法是 ``python -c command [arg] ...`` ，这种方法可以在 *命令行* 执行 Python 语句，类似于 shell 中的 :option:`-c` 选项。 由于 Python 语句通常会包含空格或其他特殊 shell 字符，一般建议将 *命令* 用单引号包裹起来。

有一些 Python 模块也可以当作脚本使用。 你可以使用 ``python -m module [arg] ...`` 命令调用它们，这类似在命令行中键入完整的路径名执行 *模块* 源文件一样。

使用脚本文件时，经常会运行脚本然后进入交互模式。这也可以通过在脚本之前加上 :option:`-i` 参数来实现


.. _tut-argpassing:

参数传递
----------------

调用解释器时，脚本名和附加参数传入一个名为 ``sys.argv`` 的字符串列表。你能够获取这个列表通过执行 ``import
sys``，列表的长度大于等于1；没有给定脚本和参数时，它至少也有一个元素： ``sys.argv[0]`` 此时为空字符串。脚本名指定为 ``'-'`` （表示标准输入）时， ``sys.argv[0]`` 被设定为 ``'-'`` ，使用 :option:`-c` *指令* 时， ``sys.argv[0]`` 被设定为 ``'-c'`` 。 使用 :option:`-m` *模块* 参数时， ``sys.argv[0]`` 被设定为指定模块的全名。:option:`-c` *指令* 或者 :option:`-m` *模块* 之后的参数不会被 Python 解释器的选项处理机制所截获，而是留在 ``sys.argv`` 中，供脚本命令操作。


.. _tut-interactive:

交互模式
----------------

从 tty 读取命令时，我们称解释器工作于 *交互模式* 。这种模式下它根据 主提示符 来执行，主提示符通常标识为三个大于号 (``>>>``)；继续的部分被称为 *从属提示符* ，由三个点标识 (``...``) 。在第一行之前，解释器打印欢迎信息、版本号和授权提示::

   $ python3.3
   Python 3.3 (py3k, Sep 12 2007, 12:21:02)
   [GCC 3.4.6 20060404 (Red Hat 3.4.6-8)] on linux2
   Type "help", "copyright", "credits" or "license" for more information.
   >>>

.. XXX update for new releases

输入多行结构时需要从属提示符了，例如，下面这个 :keyword:`if` 语句::

   >>> the_world_is_flat = 1
   >>> if the_world_is_flat:
   ...     print("Be careful not to fall off!")
   ...
   Be careful not to fall off!


.. _tut-interp:

解释器及其环境
===================================


.. _tut-error:

错误处理
--------------

有错误发生时，解释器打印一个错误信息和栈跟踪器。交互模式下，它返回主提示符，如果从文件输入执行，它在打印栈跟踪器后以非零状态退出。（异常可以由 :keyword:`try` 语句中的 :keyword:`except` 子句来控制，这样就不会出现上文中的错误信息）有一些非常致命的错误会导致非零状态下退出，这由通常由内部矛盾和内存溢出造成。所有的错误信息都写入标准错误流；命令中执行的普通输出写入标准输出。

在主提示符或附属提示符输入中断符（通常是 Control-C 或者 DEL）就会取消当前输入，回到主命令行。 [#]_ 执行命令时输入一个中断符会抛出一个 :exc:`KeyboardInterrupt` 异常，它可以被 :keyword:`try` 句截获。


.. _tut-scripts:

执行 Python 脚本
-------------------------

BSD 类的 Unix 系统中，Python 脚本可以像 Shell 脚本那样直接执行。只要在脚本文件开头写一行命令，指定文件和模式 ::

   #! /usr/bin/env python3.3

(要确认 Python 解释器在用户的 :envvar:`PATH` 中) ``#!``  必须是文件的前两个字符，在某些平台上，第一行必须以 Unix 风格的行结束符（ ``'n'`` ）结束，不能用 Windows （ ``'rn'`` ） 的结束符。注意， ``'#'`` 是 Python 中是行注释的起始符。 

脚本可以通过 :program:`chmod` 命令指定执行模式和权限::

   $ chmod +x myscript.py

Windows 系统上没有“执行模式”。 Python 安装程序自动将 ``.py`` 文件关联到 ``python.exe`` ，所以在 Python 文件图标上双击，它就会作为脚本执行。同样 ``.pyw``  也作了这样的关联，通常它执行时不会显示控制台窗口。


.. _tut-source-encoding:

源程序编码
--------------------

默认情况下，Python 源文件是 UTF-8 编码。 在此编码下，全世界大多数语言的字符可以同时用在字符串、标识符和注释中 — 尽管 Python 标准库仅使用 ASCII 字符做为标识符，这只是任何可移植代码应该遵守的约定。 如果要正确的显示所有的字符，你的编辑器必须能识别出文件是 UTF-8 编码，并且它使用的字体能支持文件中所有的字符。

你也可以为源文件指定不同的字符编码。 为此，在 ``#!`` 行（首行）后插入至少一行特殊的注释行来定义源文件的编码。 ::

   # -*- coding: encoding -*-

通过此声明，源文件中所有的东西都会被当做用 *encoding* 指代的 UTF-8 编码对待。 在 Python 库参考手册 :mod:`codecs` 一节中你可以找到一张可用的编码列表。

例如，如果你的编辑器不支持 UTF-8 编码的文件，但支持像 Windows-1252 的其他一些编码，你可以定义::

   # -*- coding: cp-1252 -*-

这样就可以在源文件中使用 Windows-1252 字符集中的所有字符了。 这个特殊的编码注释必须在文件中的 *第一或第二* 行定义。


.. _tut-startup:

交互执行文件
----------------------------

使用 Python 解释器的时候，我们可能需要在每次解释器启动时执行一些命令。你可以在一个文件中包含你想要执行的命令，设定一个名为 :envvar:`PYTHONSTARTUP` 的环境变量来指定这个文件。这类似于 Unix shell 的 :file:`.profile` 文件。 

这个文件在交互会话期是只读的，当 Python 从脚本中解读文件或以终端 :file:`/dev/tty` 做为外部命令源时则不会如此（尽管它们的行为很像是处在交互会话期。）它与解释器执行的命令处在同一个命名空间，所以由它定义或引用的一切可以在解释器中不受限制的使用。你也可以在这个文件中改变 ``sys.ps1`` 和 ``sys.ps2``  指令。 

如果你想要在当前目录中执行附加的启动文件，可以在全局启动文件中加入类似以下的代码： ``if os.path.isfile('.pythonrc.py'): execfile('.pythonrc.py')``  。如果你想要在某个脚本中使用启动文件，必须要在脚本中写入这样的语句::

   import os
   filename = os.environ.get('PYTHONSTARTUP')
   if filename and os.path.isfile(filename):
       exec(open(filename).read())


.. _tut-customize:

本地化模块
-------------------------

Python 提供了两个钩子（方法）来本地化: :mod:`sitecustomize` 和
:mod:`usercustomize`。 为了见识它们，你首先需要找到你的 site-packages 的目录。启动 python 执行下面的代码::

   >>> import site
   >>> site.getusersitepackages()
   '/home/user/.local/lib/python3.2/site-packages'

现在你可以在 site-packages 的目录下创建 :file:`usercustomize.py` 文件，内容就悉听尊便了。
这个文件将会影响 python 的每次调用，除非启动的时候加入 :option:`-s` 选项禁止自动导入。

:mod:`sitecustomize` 的工作方式一样，但是是由电脑的管理账户创建以及在 :mod:`usercustomize` 之前导入。 具体可以参见 :mod:`site` 。


.. rubric:: Footnotes

.. [#] 在 Unix 系统上，Python 3.1 解释器默认未被安装成名为 python 的命令，所以它不会与同时安装在系统中的 Python 2.x 命令冲突。

.. [#] GNU Readline 包的一个问题可能禁止此功能。
