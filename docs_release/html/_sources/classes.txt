.. _tut-classes:

*******
类
*******

Python 的类机制通过最小的新语法和语义在语言中实现了类。它是 C++ 或者 Modula-3 语言中类机制的混合。就像模块一样，Python 的类并没有在用户和定义之间设立绝对的屏障，而是依赖于用户不去“强行闯入定义”的优雅。另一方面，类的大多数重要特性都被完整的保留下来：类继承机制允许多重继承，派生类可以覆盖（override）基类中的任何方法或类，可以使用相同的方法名称调用基类的方法。对象可以包含任意数量的私有数据。

用 C++ 术语来讲，所有的类成员（包括数据成员）都是公有（ *public* ）的（其它情况见下文 :ref:`tut-private`），所有的成员函数都是虚（ *virtual* ）的。用 Modula-3 的术语来讲，在成员方法中没有简便的方式引用对象的成员：方法函数在定义时需要以引用的对象做为第一个参数，调用时则会隐式引用对象。像在 Smalltalk 中一个，类也是对象。这就提供了导入和重命名语义。不像 C++ 和 Modula-3 中那样，大多数带有特殊语法的内置操作符（算法运算符、下标等）都可以针对类的需要重新定义。 

在讨论类时，没有足够的得到共识的术语，我会偶尔从 Smalltalk 和 C++ 借用一些。我比较喜欢用 Modula-3 的用语，因为比起 C++，Python 的面向对象语法更像它，但是我想很少有读者听过这个。


.. _tut-object:

术语相关
==============================

对象具有特性，并且多个名称（在多个作用于中）可以绑定在同一个对象上。在其它语言中被称为别名。在对 Python 的第一印象中这通常会被忽略，并且当处理不可变基础类型（数字，字符串，元组）时可以被放心的忽略。但是，在调用列表、字典这类可变对象，或者大多数程序外部类型（文件，窗体等）描述实体时，别名对 Python 代码的语义便具有（有意而为）影响。这通常有助于程序的优化，因为在某些方面别名表现的就像是指针。例如，你可以轻易的传递一个对象，因为通过继承只是传递一个指针。并且如果一个方法修改了一个作为参数传递的对象，调用者可以接收这一变化——这消除了两种不同的参数传递机制的需要，像 Pascal 语言。


.. _tut-scopes:

Python 作用域和命名空间
============================

在介绍类之前，我首先介绍一些有关 Python 作用域的规则。类的定义非常巧妙的运用了命名空间，要完全理解接下来的知识，需要先理解作用域和命名空间的工作原理。另外，这一切的知识对于任何高级 Python 程序员都非常有用。 

让我们从一些定义说起。

*命名空间* 是从命名到对象的映射。当前命名空间主要是通过 Python 字典实现的，不过通常不关心具体的实现方式（除非出于性能考虑），以后也有可能会改变其实现方式。以下有一些命名空间的例子：内置命名（像 `abs()`_ 这样的函数，以及内置异常名）集，模块中的全局命名，函数调用中的局部命名。某种意义上讲对象的属性集也是一个命名空间。关于命名空间需要了解的一件很重要的事就是不同命名空间中的命名没有任何联系，例如两个不同的模块可能都会定义一个名为 ``maximize`` 的函数而不会发生混淆－用户必须以模块名为前缀来引用它们。 

顺便提一句，我称 Python 中任何一个“.”之后的命名为 *属性* －－例如，表达式 ``z.real`` 中的 ``real`` 是对象 ``z`` 的一个属性。严格来讲，从模块中引用命名是引用属性：表达式 ``modname.funcname`` 中，``modname`` 是一个模块对象，``funcname`` 是它的一个属性。因此，模块的属性和模块中的全局命名有直接的映射关系：它们共享同一命名空间！[#]_

属性可以是只读过或写的。后一种情况下，可以对属性赋值。你可以这样作： ``modname.the_answer = 42`` 。可写的属性也可以用 `del`_ 语句删除。例如： ``del modname.the_answer`` 会从 ``modname`` 对象中删除 :attr:`the_answer` 属性。 

不同的命名空间在不同的时刻创建，有不同的生存期。包含内置命名的命名空间在 Python 解释器启动时创建，会一直保留，不被删除。模块的全局命名空间在模块定义被读入时创建，通常，模块命名空间也会一直保存到解释器退出。由解释器在最高层调用执行的语句，不管它是从脚本文件中读入还是来自交互式输入，都是 `__main__ <https://docs.python.org/3/library/__main__.html#module-__main__>`_ 模块的一部分，所以它们也拥有自己的命名空间（内置命名也同样被包含在一个模块中，它被称作 `builtins`_ ）。

当调用函数时，就会为它创建一个局部命名空间，并且在函数返回或抛出一个并没有在函数内部处理的异常时被删除。（实际上，用遗忘来形容到底发生了什么更为贴切。）当然，每个递归调用都有自己的局部命名空间。

*作用域* 就是一个 Python 程序可以直接访问命名空间的正文区域。这里的直接访问意思是一个对名称的错误引用会尝试在命名空间内查找。尽管作用域是静态定义，在使用时他们都是动态的。每次执行时，至少有三个命名空间可以直接访问的作用域嵌套在一起：

* 包含局部命名的使用域在最里面，首先被搜索；其次搜索的是中层的作用域，这里包含了同级的函数；
  
  最后搜索最外面的作用域，它包含内置命名。

* 首先搜索最内层的作用域，它包含局部命名任意函数包含的作用域，是内层嵌套作用域搜索起点，包含非局部，但是也非全局的命名

* 接下来的作用域包含当前模块的全局命名

* 最外层的作用域（最后搜索）是包含内置命名的命名空间

如果一个命名声明为全局的，那么对它的所有引用和赋值会直接搜索包含这个模块全局命名的作用域。如果要重新绑定最里层作用域之外的变量，可以使用 `nonlocal`_ 语句；如果不声明为 nonlocal，这些变量将是只读的（对这样的变量赋值会在最里面的作用域创建一个新的局部变量，外部具有相同命名的那个变量不会改变）。

通常，局部作用域引用当前函数的命名。在函数之外，局部作用域与全局使用域引用同一命名空间：模块命名空间。类定义也是局部作用域中的另一个命名空间。 

重要的是作用域决定于源程序的意义：一个定义于某模块中的函数的全局作用域是该模块的命名空间，而不是该函数的别名被定义或调用的位置，了解这一点非常重要。另一方面，命名的实际搜索过程是动态的，在运行时确定的——然而，Python 语言也在不断发展，以后有可能会成为静态的“编译”时确定，所以不要依赖动态解析！（事实上，局部变量已经是静态确定了。）

Python 的一个特别之处在于：如果没有使用 `global`_ 语法，其赋值操作总是在最里层的作用域。赋值不会复制数据，只是将命名绑定到对象。删除也是如此：``del x`` 只是从局部作用域的命名空间中删除命名 ``x`` 。事实上，所有引入新命名的操作都作用于局部作用域。特别是 `import`_ 语句和函数定将模块名或函数绑定于局部作用域（可以使用 `global`_ 语句将变量引入到全局作用域）。

`global`_ 语句用以指明某个特定的变量为全局作用域，并重新绑定它。`nonlocal`_ 语句用以指明某个特定的变量为封闭作用域，并重新绑定它。

.. _tut-scopeexample:

作用域和命名空间示例
-----------------------------

以下是一个示例，演示了如何引用不同作用域和命名空间，以及 `global`_ 和 `nonlocal`_ 如何影响变量绑定::

   def scope_test():
       def do_local():
           spam = "local spam"
       def do_nonlocal():
           nonlocal spam
           spam = "nonlocal spam"
       def do_global():
           global spam
           spam = "global spam"
       spam = "test spam"
       do_local()
       print("After local assignment:", spam)
       do_nonlocal()
       print("After nonlocal assignment:", spam)
       do_global()
       print("After global assignment:", spam)

   scope_test()
   print("In global scope:", spam)

以上示例代码的输出为:

.. code-block:: none

   After local assignment: test spam
   After nonlocal assignment: nonlocal spam
   After global assignment: nonlocal spam
   In global scope: global spam

注意：*local* 赋值语句是无法改变 *scope_test* 的 *spam* 绑定。`nonlocal`_ 赋值语句改变了 *scope_test* 的 *spam* 绑定，并且 `global`_ 赋值语句从模块级改变了 spam 绑定。

你也可以看到在 `global`_ 赋值语句之前对 *spam* 是没有预先绑定的。


.. _tut-firstclasses:

初识类
=======================

类引入了一些新语法：三种新的对象类型和一些新的语义。


.. _tut-classdefinition:

类定义语法
-----------------------

类定义最简单的形式如下::

   class ClassName:
       <statement-1>
       .
       .
       .
       <statement-N>

类的定义就像函数定义（ `def`_ 语句），要先执行才能生效。（你当然可以把它放进 `if`_ 语句的某一分支，或者一个函数的内部。） 

习惯上，类定义语句的内容通常是函数定义，不过其它语句也可以，有时会很有用，后面我们再回过头来讨论。类中的函数定义通常包括了一个特殊形式的参数列表，用于方法调用约定——同样我们在后面讨论这些。

进入类定义部分后，会创建出一个新的命名空间，作为局部作用域。因此，所有的赋值成为这个新命名空间的局部变量。特别是函数定义在此绑定了新的命名。 

类定义完成时（正常退出），就创建了一个 *类对象*。基本上它是对类定义创建的命名空间进行了一个包装；我们在下一节进一步学习类对象的知识。原始的局部作用域（类定义引入之前生效的那个）得到恢复，类对象在这里绑定到类定义头部的类名（例子中是 :class:`ClassName` ）。


.. _tut-classobjects:

类对象
-------------

类对象支持两种操作：属性引用和实例化。 

*属性引用* 使用和 Python 中所有的属性引用一样的标准语法：``obj.name``。类对象创建后，类命名空间中所有的命名都是有效属性名。所以如果类定义是这样::

   class MyClass:
       """A simple example class"""
       i = 12345
       def f(self):
           return 'hello world'

那么 ``MyClass.i`` 和 ``MyClass.f`` 是有效的属性引用，分别返回一个整数和一个方法对象。也可以对类属性赋值，你可以通过给 ``MyClass.i`` 赋值来修改它。 :attr:`__doc__` 也是一个有效的属性，返回类的文档字符串：``"A simple example class"``。 

类的 *实例化* 使用函数符号。只要将类对象看作是一个返回新的类实例的无参数函数即可。例如（假设沿用前面的类）::

   x = MyClass()

以上创建了一个新的类 *实例* 并将该对象赋给局部变量 ``x``。

这个实例化操作（“调用”一个类对象）来创建一个空的对象。很多类都倾向于将对象创建为有初始状态的。因此类可能会定义一个名为 :meth:`__init__` 的特殊方法，像下面这样::

   def __init__(self):
       self.data = []

类定义了 :meth:`__init__` 方法的话，类的实例化操作会自动为新创建的类实例调用 :meth:`__init__` 方法。所以在下例中，可以这样创建一个新的实例::

   x = MyClass()

当然，出于弹性的需要，:meth:`__init__` 方法可以有参数。事实上，参数通过 :meth:`__init__` 传递到类的实例化操作上。例如， ::

   >>> class Complex:
   ...     def __init__(self, realpart, imagpart):
   ...         self.r = realpart
   ...         self.i = imagpart
   ...
   >>> x = Complex(3.0, -4.5)
   >>> x.r, x.i
   (3.0, -4.5)


.. _tut-instanceobjects:

实例对象
----------------

现在我们可以用实例对象作什么？实例对象唯一可用的操作就是属性引用。有两种有效的属性名。

*数据属性* 相当于 Smalltalk 中的“实例变量”或 C++ 中的“数据成员”。和局部变量一样，数据属性不需要声明，第一次使用时它们就会生成。例如，如果 ``x`` 是前面创建的 :class:`MyClass` 实例，下面这段代码会打印出 16 而在堆栈中留下多余的东西::

   x.counter = 1
   while x.counter < 10:
       x.counter = x.counter * 2
   print(x.counter)
   del x.counter

另一种为实例对象所接受的引用属性是 *方法*。方法是“属于”一个对象的函数。（在 Python 中，方法不止是类实例所独有：其它类型的对象也可有方法。例如，链表对象有 append，insert，remove，sort 等等方法。然而，在后面的介绍中，除非特别说明，我们提到的方法特指类方法） 

.. index:: object: method

实例对象的有效名称依赖于它的类。按照定义，类中所有（用户定义）的函数对象对应它的实例中的方法。所以在我们的例子中，``x.f`` 是一个有效的方法引用，因为 ``MyClass.f`` 是一个函数。但 ``x.i`` 不是，因为 ``MyClass.i`` 不是函数。不过 ``x.f`` 和 ``MyClass.f`` 不同，它是一个 *方法对象* ，不是一个函数对象。


.. _tut-methodobjects:

方法对象
--------------

通常，方法通过右绑定方式调用::

   x.f()

在 :class:`MyClass` 示例中，这会返回字符串 ``'hello world'``。然而，也不是一定要直接调用方法。 ``x.f`` 是一个方法对象，它可以存储起来以后调用。例如::

   xf = x.f
   while True:
       print(xf())

会不断的打印 ``hello world``。 

调用方法时发生了什么？你可能注意到调用 ``x.f()`` 时没有引用前面标出的变量，尽管在 :meth:`f` 的函数定义中指明了一个参数。这个参数怎么了？事实上如果函数调用中缺少参数，Python 会抛出异常－－甚至这个参数实际上没什么用…… 

实际上，你可能已经猜到了答案：方法的特别之处在于实例对象作为函数的第一个参数传给了函数。在我们的例子中，调用 ``x.f()`` 相当于 ``MyClass.f(x)`` 。通常，以 *n* 个参数的列表去调用一个方法就相当于将方法的对象插入到参数列表的最前面后，以这个列表去调用相应的函数。 

如果你还是不理解方法的工作原理，了解一下它的实现也许有帮助。引用非数据属性的实例属性时，会搜索它的类。如果这个命名确认为一个有效的函数对象类属性，就会将实例对象和函数对象封装进一个抽象对象：这就是方法对象。以一个参数列表调用方法对象时，它被重新拆封，用实例对象和原始的参数列表构造一个新的参数列表，然后函数对象调用这个新的参数列表。


.. _tut-class-and-instance-variables:

类和实例变量
----------------------------

一般来说，实例变量用于对每一个实例都是唯一的数据，类变量用于类的所有实例共享的属性和方法::

    class Dog:

        kind = 'canine'         # class variable shared by all instances

        def __init__(self, name):
            self.name = name    # instance variable unique to each instance

    >>> d = Dog('Fido')
    >>> e = Dog('Buddy')
    >>> d.kind                  # shared by all dogs
    'canine'
    >>> e.kind                  # shared by all dogs
    'canine'
    >>> d.name                  # unique to d
    'Fido'
    >>> e.name                  # unique to e
    'Buddy'

正如在 :ref:`tut-object` 讨论的， `可变`_ 对象，例如列表和字典，的共享数据可能带来意外的效果。例如，下面代码中的 *tricks* 列表不应该用作类变量，因为所有的 *Dog*  实例将共享同一个列表::

    class Dog:

        tricks = []             # mistaken use of a class variable

        def __init__(self, name):
            self.name = name

        def add_trick(self, trick):
            self.tricks.append(trick)

    >>> d = Dog('Fido')
    >>> e = Dog('Buddy')
    >>> d.add_trick('roll over')
    >>> e.add_trick('play dead')
    >>> d.tricks                # unexpectedly shared by all dogs
    ['roll over', 'play dead']

这个类的正确设计应该使用一个实例变量::

    class Dog:

        def __init__(self, name):
            self.name = name
            self.tricks = []    # creates a new empty list for each dog

        def add_trick(self, trick):
            self.tricks.append(trick)

    >>> d = Dog('Fido')
    >>> e = Dog('Buddy')
    >>> d.add_trick('roll over')
    >>> e.add_trick('play dead')
    >>> d.tricks
    ['roll over']
    >>> e.tricks
    ['play dead']


.. _tut-remarks:

一些说明
==============

.. These should perhaps be placed more carefully...

数据属性会覆盖同名的方法属性。为了避免意外的名称冲突，这在大型程序中是极难发现的 Bug，使用一些约定来减少冲突的机会是明智的。可能的约定包括：大写方法名称的首字母，使用一个唯一的小字符串（也许只是一个下划线）作为数据属性名称的前缀，或者方法使用动词而数据属性使用名词。

数据属性可以被方法引用，也可以由一个对象的普通用户（客户）使用。换句话说，类不能用来实现纯净的数据类型。事实上，Python 中不可能强制隐藏数据——一切基于约定（如果需要，使用 C 编写的 Python 实现可以完全隐藏实现细节并控制对象的访问。这可以用来通过 C 语言扩展 Python）。

客户应该谨慎的使用数据属性——客户可能通过践踏他们的数据属性而使那些由方法维护的常量变得混乱。注意：只要能避免冲突，客户可以向一个实例对象添加他们自己的数据属性，而不会影响方法的正确性——再次强调，命名约定可以避免很多麻烦。

从方法内部引用数据属性（或其他方法）并没有快捷方式。我觉得这实际上增加了方法的可读性：当浏览一个方法时，在局部变量和实例变量之间不会出现令人费解的情况。

一般，方法的第一个参数被命名为 ``self``。这仅仅是一个约定：对 Python 而言，名称 ``self`` 绝对没有任何特殊含义。（但是请注意：如果不遵循这个约定，对其他的 Python 程序员而言你的代码可读性就会变差，而且有些 *类查看器* 程序也可能是遵循此约定编写的。）

类属性的任何函数对象都为那个类的实例定义了一个方法。函数定义代码不一定非得定义在类中：也可以将一个函数对象赋值给类中的一个局部变量。例如::

   # Function defined outside the class
   def f1(self, x, y):
       return min(x, x+y)

   class C:
       f = f1
       def g(self):
           return 'hello world'
       h = g

现在 ``f``， ``g`` 和 ``h`` 都是类 :class:`C` 的属性，引用的都是函数对象，因此它们都是 :class:`C` 实例的方法－－ ``h`` 严格等于 ``g`` 。要注意的是这种习惯通常只会迷惑程序的读者。 

通过 ``self`` 参数的方法属性，方法可以调用其它的方法::

   class Bag:
       def __init__(self):
           self.data = []
       def add(self, x):
           self.data.append(x)
       def addtwice(self, x):
           self.add(x)
           self.add(x)

方法可以像引用普通的函数那样引用全局命名。与方法关联的全局作用域是包含类定义的模块。（类本身永远不会做为全局作用域使用。）尽管很少有好的理由在方法 中使用全局数据，全局作用域确有很多合法的用途：其一是方法可以调用导入全局作用域的函数和方法，也可以调用定义在其中的类和函数。通常，包含此方法的类也会定义在这个全局作用域，在下一节我们会了解为何一个方法要引用自己的类。 

每个值都是一个对象，因此每个值都有一个 类( *class* ) （也称为它的 类型( *type* ) ），它存储为 ``object.__class__`` 。


.. _tut-inheritance:

继承
===========

当然，如果一种语言不支持继承就，“类”就没有什么意义。派生类的定义如下所示::

   class DerivedClassName(BaseClassName):
       <statement-1>
       .
       .
       .
       <statement-N>

命名 :class:`BaseClassName` （示例中的基类名）必须与派生类定义在一个作用域内。除了类，还可以用表达式，基类定义在另一个模块中时这一点非常有用::

   class DerivedClassName(modname.BaseClassName):

派生类定义的执行过程和基类是一样的。构造派生类对象时，就记住了基类。这在解析属性引用的时候尤其有用：如果在类中找不到请求调用的属性，就搜索基类。如果基类是由别的类派生而来，这个规则会递归的应用上去。 

派生类的实例化没有什么特殊之处： ``DerivedClassName()`` （示列中的派生类）创建一个新的类实例。方法引用按如下规则解析：搜索对应的类属性，必要时沿基类链逐级搜索，如果找到了函数对象这个方法引用就是合法的。 

派生类可能会覆盖其基类的方法。因为方法调用同一个对象中的其它方法时没有特权，基类的方法调用同一个基类的方法时，可能实际上最终调用了派生类中的覆盖方法。（对于 C++ 程序员来说，Python 中的所有方法本质上都是 ``虚`` 方法。） 

派生类中的覆盖方法可能是想要扩充而不是简单的替代基类中的重名方法。有一个简单的方法可以直接调用基类方法，只要调用： ``BaseClassName.methodname(self, arguments)``。有时这对于客户也很有用。（要注意只有 ``BaseClassName`` 在同一全局作用域定义或导入时才能这样用。） 

Python 有两个用于继承的函数：

* 函数 `isinstance()`_ 用于检查实例类型： ``isinstance(obj, int)`` 只有在 ``obj.__class__`` 是 `int`_ 或其它从 `int`_ 继承的类型

* 函数 `issubclass()`_ 用于检查类继承： ``issubclass(bool, int)`` 为 ``True``，因为 `bool`_ 是 `int`_ 的子类。
  
  然而， ``issubclass(float, int)`` 为 ``False``，因为 `float`_ 不是 `int`_ 的子类。



.. _tut-multiple:

多继承
--------------------

Python 同样有限的支持多继承形式。多继承的类定义形如下例::

   class DerivedClassName(Base1, Base2, Base3):
       <statement-1>
       .
       .
       .
       <statement-N>

在大多数情况下，在最简单的情况下，你能想到的搜索属性从父类继承的深度优先，左到右，而不是搜索两次在同一个类层次结构中，其中有一个重叠。因此，如果在 :class:`DerivedClassName` （示例中的派生类）中没有找到某个属性，就会搜索 :class:`Base1`，然后（递归的）搜索其基类，如果最终没有找到，就搜索 :class:`Base2`，以此类推。 

实际上，`super()`_ 可以动态的改变解析顺序。这个方式可见于其它的一些多继承语言，类似 call-next-method，比单继承语言中的 super 更强大 。

动态调整顺序十分必要的，因为所有的多继承会有一到多个菱形关系（指有至少一个祖先类可以从子类经由多个继承路径到达）。例如，所有的 new-style 类继承自 `object`_ ，所以任意的多继承总是会有多于一条继承路径到达 `object`_ 。

为了防止重复访问基类，通过动态的线性化算法，每个类都按从左到右的顺序特别指定了顺序，每个祖先类只调用一次，这是单调的（意味着一个类被继承时不会影响它祖先的次序）。总算可以通过这种方式使得设计一个可靠并且可扩展的多继承类成为可能。进一步的内容请参见 `<http://www.python.org/download/releases/2.3/mro/>`_ 。


.. _tut-private:

私有变量
=================

只能从对像内部访问的“私有”实例变量，在 Python 中不存在。然而，也有一个变通的访问用于大多数 Python 代码：以一个下划线开头的命名（例如 ``_spam`` ）会被处理为 API 的非公开部分（无论它是一个函数、方法或数据成员）。它会被视为一个实现细节，无需公开。

因为有一个正当的类私有成员用途（即避免子类里定义的命名与之冲突），Python 提供了对这种结构的有限支持，称为 :dfn:`name mangling` （命名编码） 。任何形如 ``__spam`` 的标识（前面至少两个下划线，后面至多一个），被替代为 ``_classname__spam`` ，去掉前导下划线的 ``classname`` 即当前的类名。此语法不关注标识的位置，只要求在类定义内。

名称重整是有助于子类重写方法，而不会打破组内的方法调用。例如::

   class Mapping:
       def __init__(self, iterable):
           self.items_list = []
           self.__update(iterable)

       def update(self, iterable):
           for item in iterable:
               self.items_list.append(item)

       __update = update   # private copy of original update() method

   class MappingSubclass(Mapping):

       def update(self, keys, values):
           # provides new signature for update()
           # but does not break __init__()
           for item in zip(keys, values):
               self.items_list.append(item)

需要注意的是编码规则设计为尽可能的避免冲突，被认作为私有的变量仍然有可能被访问或修改。在特定的场合它也是有用的，比如调试的时候。 

要注意的是代码传入 ``exec()``， ``eval()`` 时不考虑所调用的类的类名，视其为当前类，这类似于 ``global`` 语句的效应，已经按字节编译的部分也有同样的限制。这也同样作用于 ``getattr()``， ``setattr()`` 和 ``delattr()``，像直接引用 ``__dict__`` 一样。


.. _tut-odds:

补充
=============

有时类似于 Pascal 中“记录（record）”或 C 中“结构（struct）”的数据类型很有用，它将一组已命名的数据项绑定在一起。一个空的类定义可以很好的实现它::

   class Employee:
       pass

   john = Employee() # Create an empty employee record

   # Fill the fields of the record
   john.name = 'John Doe'
   john.dept = 'computer lab'
   john.salary = 1000

某一段 Python 代码需要一个特殊的抽象数据结构的话，通常可以传入一个类，事实上这模仿了该类的方法。例如，如果你有一个用于从文件对象中格式化数据的函数，你可以定义一个带有 :meth:`read` 和 :meth:`readline` 方法的类，以此从字符串缓冲读取数据，然后将该类的对象作为参数传入前述的函数。

实例方法对象也有属性：``m.__self__`` 是一个实例方法所属的对象，而 ``m.__func__`` 是这个方法对应的函数对象。


.. _tut-exceptionclasses:

异常也是类
==========================

用户自定义异常也可以是类。利用这个机制可以创建可扩展的异常体系。 

以下是两种新的，有效的（语义上的）异常抛出形式，使用 `raise`_ 语句::

   raise Class

   raise Instance

第一种形式中，``instance`` 必须是 `type`_ 或其派生类的一个实例。第二种形式是以下形式的简写::

   raise Class()

发生的异常其类型如果是 `except`_ 子句中列出的类，或者是其派生类，那么它们就是相符的（反过来说－－发生的异常其类型如果是异常子句中列出的类的基类，它们就不相符）。例如，以下代码会按顺序打印 B，C，D::

   class B(Exception):
       pass
   class C(B):
       pass
   class D(C):
       pass

   for cls in [B, C, D]:
       try:
           raise cls()
       except D:
           print("D")
       except C:
           print("C")
       except B:
           print("B")

要注意的是如果异常子句的顺序颠倒过来（ ``execpt B`` 在最前），它就会打印 B，B，B－－第一个匹配的异常被触发。

打印一个异常类的错误信息时，先打印类名，然后是一个空格、一个冒号，然后是用内置函数 `str()`_ 将类转换得到的完整字符串。


.. _tut-iterators:

迭代器
=========

现在你可能注意到大多数容器对象都可以用 `for`_ 遍历::

   for element in [1, 2, 3]:
       print(element)
   for element in (1, 2, 3):
       print(element)
   for key in {'one':1, 'two':2}:
       print(key)
   for char in "123":
       print(char)
   for line in open("myfile.txt"):
       print(line, end='')

这种形式的访问清晰、简洁、方便。迭代器的用法在 Python 中普遍而且统一。在后台， `for`_ 语句在容器对象中调用 `iter()`_ 。该函数返回一个定义了 `__next__() <https://docs.python.org/3/library/stdtypes.html#iterator.__next__>`_ 方法的迭代器对象，它在容器中逐一访问元素。没有后续的元素时， `__next__() <https://docs.python.org/3/library/stdtypes.html#iterator.__next__>`_  抛出一个 `StopIteration`_ 异常通知 `for`_ 语句循环结束。你可以是用内建的 `next()`_ 函数调用 `__next__() <https://docs.python.org/3/library/stdtypes.html#iterator.__next__>`_ 方法；以下是其工作原理的示例::

   >>> s = 'abc'
   >>> it = iter(s)
   >>> it
   <iterator object at 0x00A1DB50>
   >>> next(it)
   'a'
   >>> next(it)
   'b'
   >>> next(it)
   'c'
   >>> next(it)
   Traceback (most recent call last):
     File "<stdin>", line 1, in ?
       next(it)
   StopIteration

了解了迭代器协议的后台机制，就可以很容易的给自己的类添加迭代器行为。定义一个 `__iter__() <https://docs.python.org/3/reference/datamodel.html#object.__iter__>`_ 方法，使其返回一个带有 `__next__() <https://docs.python.org/3/library/stdtypes.html#iterator.__next__>`_ 方法的对象。如果这个类已经定义了 `__next__() <https://docs.python.org/3/library/stdtypes.html#iterator.__next__>`_ ，那么 `__iter__() <https://docs.python.org/3/reference/datamodel.html#object.__iter__>`_ 只需要返回 ``self``::

   class Reverse:
       """Iterator for looping over a sequence backwards."""
       def __init__(self, data):
           self.data = data
           self.index = len(data)
       def __iter__(self):
           return self
       def __next__(self):
           if self.index == 0:
               raise StopIteration
           self.index = self.index - 1
           return self.data[self.index]

::

   >>> rev = Reverse('spam')
   >>> iter(rev)
   <__main__.Reverse object at 0x00A1DB50>
   >>> for char in rev:
   ...     print(char)
   ...
   m
   a
   p
   s


.. _tut-generators:

生成器
==========

`Generator`_ 是创建迭代器的简单而强大的工具。它们写起来就像是正规的函数，需要返回数据的时候使用 `yield`_ 语句。每次 `next()`_ 被调用时，生成器回复它脱离的位置（它记忆语句最后一次执行的位置和所有的数据值）。以下示例演示了生成器可以很简单的创建出来::

   def reverse(data):
       for index in range(len(data)-1, -1, -1):
           yield data[index]

::

   >>> for char in reverse('golf'):
   ...     print(char)
   ...
   f
   l
   o
   g

前一节中描述了基于类的迭代器，它能作的每一件事生成器也能作到。因为自动创建了 `__iter__() <https://docs.python.org/3/reference/datamodel.html#object.__iter__>`_ 和 `__next__() <https://docs.python.org/3/reference/expressions.html#generator.__next__>`_ 方法，生成器显得如此简洁。 

另一个关键的功能在于两次执行之间，局部变量和执行状态都自动的保存下来。这使函数很容易写，而且比使用 ``self.index`` 和 ``self.data`` 之类的方式更清晰。 

除了创建和保存程序状态的自动方法，当发生器终结时，还会自动抛出 `StopIteration`_  异常。综上所述，这些功能使得编写一个正规函数成为创建迭代器的最简单方法。


.. _tut-genexps:

生成器表达式
=====================

有时简单的生成器可以用简洁的方式调用，就像不带中括号的链表推导式。这些表达式是为函数调用生成器而设计的。生成器表达式比完整的生成器定义更简洁，但是没有那么多变，而且通常比等价的链表推导式更容易记。 

例如::

   >>> sum(i*i for i in range(10))                 # sum of squares
   285

   >>> xvec = [10, 20, 30]
   >>> yvec = [7, 5, 3]
   >>> sum(x*y for x,y in zip(xvec, yvec))         # dot product
   260

   >>> from math import pi, sin
   >>> sine_table = {x: sin(x*pi/180) for x in range(0, 91)}

   >>> unique_words = set(word  for line in page  for word in line.split())

   >>> valedictorian = max((student.gpa, student.name) for student in graduates)

   >>> data = 'golf'
   >>> list(data[i] for i in range(len(data)-1, -1, -1))
   ['f', 'l', 'o', 'g']




.. rubric:: Footnotes

.. [#] 有一个例外。模块对象有一个隐秘的只读对象，名为 :attr:`__dict__` ，它返回用于实现模块命名空间的字典，命名 :attr:`__dict__`  是一个属性而非全局命名。显然，使用它违反了命名空间实现的抽象原则，应该被严格限制于调试中。



.. _abs(): https://docs.python.org/3/library/functions.html#abs
.. _del: https://docs.python.org/3/reference/simple_stmts.html#del
.. _builtins: https://docs.python.org/3/library/builtins.html#module-builtins
.. _nonlocal: https://docs.python.org/3/reference/simple_stmts.html#nonlocal
.. _global: https://docs.python.org/3/reference/simple_stmts.html#global
.. _import: https://docs.python.org/3/reference/simple_stmts.html#import
.. _def: https://docs.python.org/3/reference/compound_stmts.html#def
.. _if: https://docs.python.org/3/reference/compound_stmts.html#if
.. _可变: https://docs.python.org/3/glossary.html#term-mutable
.. _isinstance(): https://docs.python.org/3/library/functions.html#isinstance
.. _int: https://docs.python.org/3/library/functions.html#int
.. _bool: https://docs.python.org/3/library/functions.html#bool
.. _float: https://docs.python.org/3/library/functions.html#float
.. _issubclass(): https://docs.python.org/3/library/functions.html#issubclass
.. _super(): https://docs.python.org/3/library/functions.html#super
.. _object: https://docs.python.org/3/library/functions.html#object
.. _raise: https://docs.python.org/3/reference/simple_stmts.html#raise
.. _type: https://docs.python.org/3/library/functions.html#type
.. _except: https://docs.python.org/3/reference/compound_stmts.html#except
.. _str(): https://docs.python.org/3/library/stdtypes.html#str
.. _for: https://docs.python.org/3/reference/compound_stmts.html#for
.. _iter(): https://docs.python.org/3/library/functions.html#iter
.. _StopIteration: https://docs.python.org/3/library/exceptions.html#StopIteration
.. _next(): https://docs.python.org/3/library/functions.html#next
.. _Generator: https://docs.python.org/3/glossary.html#term-generator
.. _yield: https://docs.python.org/3/reference/simple_stmts.html#yield
.. _StopIteration: https://docs.python.org/3/library/exceptions.html#StopIteration