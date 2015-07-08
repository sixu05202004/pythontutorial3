.. _tut-interacting:

**************************************************
交互式输入行编辑历史回溯
**************************************************

某些版本的 Python 解释器支持编辑当前的输入行和历史记录，类似于在 Korn shell 和 GNU Bash shell 中看到的功能。这是使用 `GNU Readline`_ 库实现的，它支持各种编辑风格。 这个库有它自己的文档，在这里我们不就重复了。


.. _tut-keybindings:

Tab 补全和历史记录
==================================

变量和模块名的补全在解释器启动时 `自动打开 <https://docs.python.org/3/library/site.html#rlcompleter-config>`_ 以便 :kbd:`Tab` 键调用补全功能；它会查看Python语句的名字，当前局部变量以及可以访问的模块名。对于点分表达式如 ``string.a``，它将求出表达式最后一个 ``'.'`` 之前的值，然后根据结果的属性给出补全的建议。注意，如果一个具有 `__getattr__() <https://docs.python.org/3/reference/datamodel.html#object.__getattr__>`_ 方法的对象是表达式的某部分，这可能执行应用程序定义的代码。默认的配置同时会把历史记录保存在你的用户目录下一个名为 :file:`.python_history` 的文件中。在下次与交互式解释器的回话中，历史记录将还可以访问。


.. _tut-commentary:

其它交互式解释器
===========================================

与早期版本的解释器相比，现在是向前巨大的进步；然而，有些愿望还是没有实现：如果能对连续的行给出正确的建议就更好了（解析器知道下一行是否需要缩进）。补全机制可以使用解释器的符号表。检查（或者只是建议）匹配的括号、 引号的命令等也会非常有用。

一个增强的交互式解释器是 IPython_，它已经存在相当一段时间，具有 tab 补全、 对象 exploration 和高级的历史记录功能。它也可以彻底定制并嵌入到其他应用程序中。另一个类似的增强的交互式环境是 bpython_。



.. _GNU Readline: http://tiswww.case.edu/php/chet/readline/rltop.html
.. _IPython: http://ipython.scipy.org/
.. _bpython: http://www.bpython-interpreter.org/
