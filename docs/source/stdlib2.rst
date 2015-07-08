.. _tut-brieftourtwo:

*********************************************
标准库浏览 -- Part II
*********************************************

第二部分包含了支持专业编程工作所需的更高级的模块，这些模块很少出现在小脚本中。


.. _tut-output-formatting:

输出格式
=================

`reprlib <https://docs.python.org/3/library/reprlib.html#module-reprlib>`_ 模块为大型的或深度嵌套的容器缩写显示提供了 :`repr() <https://docs.python.org/3/library/functions.html#repr>`_ 函数的一个定制版本::

   >>> import reprlib
   >>> reprlib.repr(set('supercalifragilisticexpialidocious'))
   "set(['a', 'c', 'd', 'e', 'f', 'g', ...])"

`pprint <https://docs.python.org/3/library/pprint.html#module-pprint>`_ 模块给老手提供了一种解释器可读的方式深入控制内置和用户自定义对象的打印。当输出超过一行的时候，“美化打印（pretty printer）”添加断行和标识符，使得数据结构显示的更清晰::

   >>> import pprint
   >>> t = [[[['black', 'cyan'], 'white', ['green', 'red']], [['magenta',
   ...     'yellow'], 'blue']]]
   ...
   >>> pprint.pprint(t, width=30)
   [[[['black', 'cyan'],
      'white',
      ['green', 'red']],
     [['magenta', 'yellow'],
      'blue']]]

`textwrap <https://docs.python.org/3/library/textwrap.html#module-textwrap>`_ 模块格式化文本段落以适应设定的屏宽::

   >>> import textwrap
   >>> doc = """The wrap() method is just like fill() except that it returns
   ... a list of strings instead of one big string with newlines to separate
   ... the wrapped lines."""
   ...
   >>> print(textwrap.fill(doc, width=40))
   The wrap() method is just like fill()
   except that it returns a list of strings
   instead of one big string with newlines
   to separate the wrapped lines.

`locale <https://docs.python.org/3/library/locale.html#module-locale>`_ 模块按访问预定好的国家信息数据库。locale 的格式化函数属性集提供了一个直接方式以分组标示格式化数字::

   >>> import locale
   >>> locale.setlocale(locale.LC_ALL, 'English_United States.1252')
   'English_United States.1252'
   >>> conv = locale.localeconv()          # get a mapping of conventions
   >>> x = 1234567.8
   >>> locale.format("%d", x, grouping=True)
   '1,234,567'
   >>> locale.format_string("%s%.*f", (conv['currency_symbol'],
   ...                      conv['frac_digits'], x), grouping=True)
   '$1,234,567.80'


.. _tut-templating:

模板
==========

`string <https://docs.python.org/3/library/string.html#module-string>`_ 提供了一个灵活多变的模版类 `Template <https://docs.python.org/3/library/string.html#string.Template>`_ ，使用它最终用户可以用简单的进行编辑。这使用户可以在不进行改变的情况下定制他们的应用程序。 

格式使用 ``$`` 为开头的 Python 合法标识（数字、字母和下划线）作为占位符。占位符外面的大括号使它可以和其它的字符不加空格混在一起。 ``$$`` 创建一个单独的 ``$``::

   >>> from string import Template
   >>> t = Template('${village}folk send $$10 to $cause.')
   >>> t.substitute(village='Nottingham', cause='the ditch fund')
   'Nottinghamfolk send $10 to the ditch fund.'

当一个占位符在字典或关键字参数中没有被提供时，`substitute() <https://docs.python.org/3/library/string.html#string.Template.substitute>`_ 方法就会抛出一个 `KeyError <https://docs.python.org/3/library/exceptions.html#KeyError>`_ 异常。 对于邮件合并风格的应用程序，用户提供的数据可能并不完整，这时使用 `safe_substitute() <https://docs.python.org/3/library/string.html#string.Template.safe_substitute>`_ 方法可能更适合 — 如果数据不完整，它就不会改变占位符::

   >>> t = Template('Return the $item to $owner.')
   >>> d = dict(item='unladen swallow')
   >>> t.substitute(d)
   Traceback (most recent call last):
     ...
   KeyError: 'owner'
   >>> t.safe_substitute(d)
   'Return the unladen swallow to $owner.'

模板子类可以指定一个自定义分隔符。例如，图像查看器的批量重命名工具可能选择使用百分号作为占位符，像当前日期，图片序列号或文件格式::

   >>> import time, os.path
   >>> photofiles = ['img_1074.jpg', 'img_1076.jpg', 'img_1077.jpg']
   >>> class BatchRename(Template):
   ...     delimiter = '%'
   >>> fmt = input('Enter rename style (%d-date %n-seqnum %f-format):  ')
   Enter rename style (%d-date %n-seqnum %f-format):  Ashley_%n%f

   >>> t = BatchRename(fmt)
   >>> date = time.strftime('%d%b%y')
   >>> for i, filename in enumerate(photofiles):
   ...     base, ext = os.path.splitext(filename)
   ...     newname = t.substitute(d=date, n=i, f=ext)
   ...     print('{0} --> {1}'.format(filename, newname))

   img_1074.jpg --> Ashley_0.jpg
   img_1076.jpg --> Ashley_1.jpg
   img_1077.jpg --> Ashley_2.jpg

模板的另一个应用是把多样的输出格式细节从程序逻辑中分类出来。这便使得 XML 文件，纯文本报表和 HTML WEB 报表定制模板成为可能。


.. _tut-binary-formats:

使用二进制数据记录布局
=======================================

`struct <https://docs.python.org/3/library/struct.html#module-struct>`_ 模块为使用变长的二进制记录格式提供了 `pack() <https://docs.python.org/3/library/struct.html#struct.pack>`_ 和 `unpack() <https://docs.python.org/3/library/struct.html#struct.unpack>`_ 函数。下面的示例演示了在不使用 `zipfile <https://docs.python.org/3/library/zipfile.html#module-zipfile>`_ 模块的情况下如何迭代一个 ZIP 文件的头信息。压缩码 ``"H"`` 和 ``"I"`` 分别表示2和4字节无符号数字， ``"<"`` 表明它们都是标准大小并且按照 little-endian 字节排序。 ::

   import struct

   with open('myfile.zip', 'rb') as f:
       data = f.read()

   start = 0
   for i in range(3):                      # show the first 3 file headers
       start += 14
       fields = struct.unpack('<IIIHH', data[start:start+16])
       crc32, comp_size, uncomp_size, filenamesize, extra_size = fields

       start += 16
       filename = data[start:start+filenamesize]
       start += filenamesize
       extra = data[start:start+extra_size]
       print(filename, hex(crc32), comp_size, uncomp_size)

       start += extra_size + comp_size     # skip to the next header


.. _tut-multi-threading:

多线程
===============

线程是一个分离无顺序依赖关系任务的技术。在某些任务运行于后台的时候应用程序会变得迟缓，线程可以提升其速度。一个有关的用途是在 I/O 的同时其它线程可以并行计算。 

下面的代码显示了高级模块 `threading <https://docs.python.org/3/library/threading.html#module-threading>`_ 如何在主程序运行的同时运行任务::

   import threading, zipfile

   class AsyncZip(threading.Thread):
       def __init__(self, infile, outfile):
           threading.Thread.__init__(self)
           self.infile = infile
           self.outfile = outfile
       def run(self):
           f = zipfile.ZipFile(self.outfile, 'w', zipfile.ZIP_DEFLATED)
           f.write(self.infile)
           f.close()
           print('Finished background zip of:', self.infile)

   background = AsyncZip('mydata.txt', 'myarchive.zip')
   background.start()
   print('The main program continues to run in foreground.')

   background.join()    # Wait for the background task to finish
   print('Main program waited until background was done.')

多线程应用程序的主要挑战是协调线程，诸如线程间共享数据或其它资源。为了达到那个目的，线程模块提供了许多同步化的原生支持，包括：锁，事件，条件变量和信号灯。

尽管这些工具很强大，微小的设计错误也可能造成难以挽回的故障。因此，任务协调的首选方法是把对一个资源的所有访问集中在一个单独的线程中，然后使用 `queue <https://docs.python.org/3/library/queue.html#module-queue>`_ 模块用那个线程服务其他线程的请求。为内部线程通信和协调而使用 `Queue <https://docs.python.org/3/library/queue.html#queue.Queue>`_ 对象的应用程序更易于设计，更可读，并且更可靠。


.. _tut-logging:

日志
=======

`logging <https://docs.python.org/3/library/logging.html#module-logging>`_ 模块提供了完整和灵活的日志系统。它最简单的用法是记录信息并发送到一个文件或 ``sys.stderr``::

   import logging
   logging.debug('Debugging information')
   logging.info('Informational message')
   logging.warning('Warning:config file %s not found', 'server.conf')
   logging.error('Error occurred')
   logging.critical('Critical error -- shutting down')

输出如下::

   WARNING:root:Warning:config file server.conf not found
   ERROR:root:Error occurred
   CRITICAL:root:Critical error -- shutting down

默认情况下捕获信息和调试消息并将输出发送到标准错误流。其它可选的路由信息方式通过 email，数据报文，socket 或者 HTTP Server。基于消息属性，新的过滤器可以选择不同的路由： :const:`DEBUG`， :const:`INFO`，
:const:`WARNING`， :const:`ERROR` 和 :const:`CRITICAL` 。 

日志系统可以直接在 Python 代码中定制，也可以不经过应用程序直接在一个用户可编辑的配置文件中加载。


.. _tut-weak-references:

弱引用
===============

Python 自动进行内存管理（对大多数的对象进行引用计数和垃圾回收—— `垃圾回收 <https://docs.python.org/3/glossary.html#term-garbage-collection>`_  ——以循环利用）在最后一个引用消失后，内存会很快释放。

这个工作方式对大多数应用程序工作良好，但是偶尔会需要跟踪对象来做一些事。不幸的是，仅仅为跟踪它们创建引用也会使其长期存在。 `weakref <https://docs.python.org/3/library/weakref.html#module-weakref>`_ 模块提供了不用创建引用的跟踪对象工具，一旦对象不再存在，它自动从弱引用表上删除并触发回调。典型的应用包括捕获难以构造的对象::

   >>> import weakref, gc
   >>> class A:
   ...     def __init__(self, value):
   ...         self.value = value
   ...     def __repr__(self):
   ...         return str(self.value)
   ...
   >>> a = A(10)                   # create a reference
   >>> d = weakref.WeakValueDictionary()
   >>> d['primary'] = a            # does not create a reference
   >>> d['primary']                # fetch the object if it is still alive
   10
   >>> del a                       # remove the one reference
   >>> gc.collect()                # run garbage collection right away
   0
   >>> d['primary']                # entry was automatically removed
   Traceback (most recent call last):
     File "<stdin>", line 1, in <module>
       d['primary']                # entry was automatically removed
     File "C:/python34/lib/weakref.py", line 46, in __getitem__
       o = self.data[key]()
   KeyError: 'primary'


.. _tut-list-tools:

列表工具
============================

很多数据结构可能会用到内置列表类型。然而，有时可能需要不同性能代价的实现。 

`array <https://docs.python.org/3/library/array.html#module-array>`_ 模块提供了一个类似列表的 `array() <https://docs.python.org/3/library/array.html#array.array>`_ 对象，它仅仅是存储数据，更为紧凑。以下的示例演示了一个存储双字节无符号整数的数组（类型编码 ``"H"`` ）而非存储 16 字节 Python 整数对象的普通正规列表::

   >>> from array import array
   >>> a = array('H', [4000, 10, 700, 22222])
   >>> sum(a)
   26932
   >>> a[1:3]
   array('H', [10, 700])

`collections <https://docs.python.org/3/library/collections.html#module-collections>`_ 模块提供了类似列表的 `deque() <https://docs.python.org/3/library/collections.html#collections.deque>`_ 对象，它从左边添加（append）和弹出（pop）更快，但是在内部查询更慢。这些对象更适用于队列实现和广度优先的树搜索::

   >>> from collections import deque
   >>> d = deque(["task1", "task2", "task3"])
   >>> d.append("task4")
   >>> print("Handling", d.popleft())
   Handling task1

::

   unsearched = deque([starting_node])
   def breadth_first_search(unsearched):
       node = unsearched.popleft()
       for m in gen_moves(node):
           if is_goal(m):
               return m
           unsearched.append(m)

除了链表的替代实现，该库还提供了 `bisect <https://docs.python.org/3/library/bisect.html#module-bisect>`_ 这样的模块以操作存储链表::

   >>> import bisect
   >>> scores = [(100, 'perl'), (200, 'tcl'), (400, 'lua'), (500, 'python')]
   >>> bisect.insort(scores, (300, 'ruby'))
   >>> scores
   [(100, 'perl'), (200, 'tcl'), (300, 'ruby'), (400, 'lua'), (500, 'python')]

`heapq <https://docs.python.org/3/library/heapq.html#module-heapq>`_ 提供了基于正规链表的堆实现。最小的值总是保持在 0 点。这在希望循环访问最小元素但是不想执行完整堆排序的时候非常有用::

   >>> from heapq import heapify, heappop, heappush
   >>> data = [1, 3, 5, 7, 9, 2, 4, 6, 8, 0]
   >>> heapify(data)                      # rearrange the list into heap order
   >>> heappush(data, -5)                 # add a new entry
   >>> [heappop(data) for i in range(3)]  # fetch the three smallest entries
   [-5, 0, 1]


.. _tut-decimal-fp:

十进制浮点数算法
=================================

`decimal <https://docs.python.org/3/library/decimal.html#module-decimal>`_ 模块提供了一个 `Decimal <https://docs.python.org/3/library/decimal.html#decimal.Decimal>`_ 数据类型用于浮点数计算。相比内置的二进制浮点数实现 `float <https://docs.python.org/3/library/functions.html#float>`_，这个类型有助于

* 金融应用和其它需要精确十进制表达的场合，

* 控制精度，

* 控制舍入以适应法律或者规定要求，

* 确保十进制数位精度，
  
  或者

* 用户希望计算结果与手算相符的场合。

例如，计算 70 分电话费的 5% 税计算，十进制浮点数和二进制浮点数计算结果的差别如下。如果在分值上舍入，这个差别就很重要了::

   >>> from decimal import *
   >>> round(Decimal('0.70') * Decimal('1.05'), 2)
   Decimal('0.74')
   >>> round(.70 * 1.05, 2)
   0.73

`Decimal <https://docs.python.org/3/library/decimal.html#decimal.Decimal>`_ 的结果总是保有结尾的 0，自动从两位精度延伸到4位。Decimal 重现了手工的数学运算，这就确保了二进制浮点数无法精确保有的数据精度。

高精度使 `Decimal <https://docs.python.org/3/library/decimal.html#decimal.Decimal>`_ 可以执行二进制浮点数无法进行的模运算和等值测试::

   >>> Decimal('1.00') % Decimal('.10')
   Decimal('0.00')
   >>> 1.00 % 0.10
   0.09999999999999995

   >>> sum([Decimal('0.1')]*10) == Decimal('1.0')
   True
   >>> sum([0.1]*10) == 1.0
   False

`decimal <https://docs.python.org/3/library/decimal.html#module-decimal>`_ 提供了必须的高精度算法::

   >>> getcontext().prec = 36
   >>> Decimal(1) / Decimal(7)
   Decimal('0.142857142857142857142857142857142857')


