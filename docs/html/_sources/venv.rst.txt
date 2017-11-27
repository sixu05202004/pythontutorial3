
.. _tut-venv:

*********************************
虚拟环境和包
*********************************

简介
============

Python 应用程序经常会使用一些不属于标准库的包和模块。应用程序有时候需要某个特定版本的库，因为它需要一个特定的 bug 已得到修复的库或者它是使用了一个过时版本的库的接口编写的。

这就意味着可能无法安装一个 Python 来满足每个应用程序的要求。如果应用程序 A 需要一个特定模块的 1.0 版本但是应用程序 B 需要该模块的 2.0 版本，这两个应用程序的要求是冲突的，安装版本 1.0 或者版本 2.0 将会导致其中一个应用程序不能运行。

这个问题的解决方案就是创建一个 `虚拟环境 <https://docs.python.org/3/glossary.html#term-virtual-environment>`_ （通常简称为 “virtualenv”），包含一个特定版本的 Python，以及一些附加的包的独立的目录树。

不同的应用程序可以使用不同的虚拟环境。为了解决前面例子中的冲突，应用程序 A 可以有自己的虚拟环境，其中安装了特定模块的 1.0 版本。而应用程序 B 拥有另外一个安装了特定模块 2.0 版本的虚拟环境。如果应用程序 B 需求一个库升级到 3.0 的话，这也不会影响到应用程序 A 的环境。


创建虚拟环境
=============================

用于创建和管理虚拟环境的脚本叫做 :program:`pyvenv`。:program:`pyvenv` 通常会安装你可用的 Python 中最新的版本。这个脚本也能指定安装一个特定的版本的 Python，因此如果在你的系统中有多个版本的 Python 的话，你可以运行 ``pyvenv-3.5`` 或者你想要的任何版本来选择一个指定的 Python 版本。

要创建一个 virtualenv，首先决定一个你想要存放的目录接着运行 :program:`pyvenv` 后面携带着目录名::

   pyvenv tutorial-env

如果目录不存在的话，这将会创建一个 ``tutorial-env`` 目录，并且也在目录里面创建一个包含 Python 解释器，标准库，以及各种配套文件的 Python “副本”。

一旦你已经创建了一个虚拟环境，你必须激活它。

在 Windows 上，运行::

  tutorial-env/Scripts/activate

在 Unix 或者 MacOS 上，运行::

  source tutorial-env/bin/activate

（这个脚本是用 bash shell 编写的。如果你使用 :program:`csh` 或者 :program:`fish` shell，你应该使用 ``activate.csh`` 和 ``activate.fish`` 来替代。）

激活了虚拟环境会改变你的 shell 提示符，显示你正在使用的虚拟环境，并且修改了环境以致运行 ``python`` 将会让你得到了特定的 Python 版本。例如::

  -> source ~/envs/tutorial-env/bin/activate
  (tutorial-env) -> python
  Python 3.5.2+ (3.4:c7b9645a6f35+, May 22 2015, 09:31:25)
    ...
  >>> import sys
  >>> sys.path
  ['', '/usr/local/lib/python35.zip', ...,
  '~/envs/tutorial-env/lib/python3.5/site-packages']
  >>>


使用 pip 管理包
==========================

一旦你激活了一个虚拟环境，可以使用一个叫做 :program:`pip` 程序来安装，升级以及删除包。默认情况下 ``pip`` 将会从 Python Package Index，<https://pypi.python.org/pypi>， 中安装包。你可以通过 web 浏览器浏览它们，或者你也能使用 ``pip`` 有限的搜索功能::

  (tutorial-env) -> pip search astronomy
  skyfield               - Elegant astronomy for Python
  gary                   - Galactic astronomy and gravitational dynamics.
  novas                  - The United States Naval Observatory NOVAS astronomy library
  astroobs               - Provides astronomy ephemeris to plan telescope observations
  PyAstronomy            - A collection of astronomy related tools for Python.
  ...

``pip`` 有许多子命令：“搜索”，“安装”，“卸载”，“freeze”（译者注：这个词语暂时没有合适的词语来翻译），等等。（请参考 `installing-index <https://docs.python.org/3/installing/index.html#installing-index>`_ 指南获取 ``pip`` 更多完整的文档。）

你可以安装一个包最新的版本，通过指定包的名称::

  -> pip install novas
  Collecting novas
    Downloading novas-3.1.1.3.tar.gz (136kB)
  Installing collected packages: novas
    Running setup.py install for novas
  Successfully installed novas-3.1.1.3

你也能安装一个指定版本的包，通过给出包名后面紧跟着 ``==`` 和版本号::

  -> pip install requests==2.6.0
  Collecting requests==2.6.0
    Using cached requests-2.6.0-py2.py3-none-any.whl
  Installing collected packages: requests
  Successfully installed requests-2.6.0

如果你重新运行命令（pip install requests==2.6.0），``pip`` 会注意到要求的版本已经安装，不会去做任何事情。你也可以提供一个不同的版本号来安装，或者运行 ``pip
install --upgrade`` 来升级包到最新版本::

  -> pip install --upgrade requests
  Collecting requests
  Installing collected packages: requests
    Found existing installation: requests 2.6.0
      Uninstalling requests-2.6.0:
        Successfully uninstalled requests-2.6.0
  Successfully installed requests-2.7.0

``pip uninstall`` 后跟一个或者多个包名将会从虚拟环境中移除这些包。

``pip show`` 将会显示一个指定的包的信息::

  (tutorial-env) -> pip show requests
  ---
  Metadata-Version: 2.0
  Name: requests
  Version: 2.7.0
  Summary: Python HTTP for Humans.
  Home-page: http://python-requests.org
  Author: Kenneth Reitz
  Author-email: me@kennethreitz.com
  License: Apache 2.0
  Location: /Users/akuchling/envs/tutorial-env/lib/python3.4/site-packages
  Requires:

``pip list`` 将会列出所有安装在虚拟环境中的包::

  (tutorial-env) -> pip list
  novas (3.1.1.3)
  numpy (1.9.2)
  pip (7.0.3)
  requests (2.7.0)
  setuptools (16.0)
 
``pip freeze`` 将会生成一个类似需要安装的包的列表，但是输出采用了 ``pip install`` 期望的格式。常见的做法就是把它们放在一个 ``requirements.txt`` 文件::

  (tutorial-env) -> pip freeze > requirements.txt
  (tutorial-env) -> cat requirements.txt
  novas==3.1.1.3
  numpy==1.9.2
  requests==2.7.0

``requirements.txt`` 能够被提交到版本控制中并且作为一个应用程序的一部分。用户们可以使用 ``install -r`` 安装所有必须的包::

  -> pip install -r requirements.txt
  Collecting novas==3.1.1.3 (from -r requirements.txt (line 1))
    ...
  Collecting numpy==1.9.2 (from -r requirements.txt (line 2))
    ...
  Collecting requests==2.7.0 (from -r requirements.txt (line 3))
    ...
  Installing collected packages: novas, numpy, requests
    Running setup.py install for novas
  Successfully installed novas-3.1.1.3 numpy-1.9.2 requests-2.7.0

``pip`` 还有更多的选项。请参考 `installing-index <https://docs.python.org/3/installing/index.html#installing-index>`_ 指南获取关于 ``pip`` 完整的文档。当你编写一个包并且在 Python Package Index 中也出现的话，请参考 `distributing-index <https://docs.python.org/3/distributing/index.html#distributing-index>`_ 指南。
