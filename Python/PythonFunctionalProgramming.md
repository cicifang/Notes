[TOC]

### 介绍

#### Functional 函数式编程

更加抽象 贴近计算 (汇编贴近计算机)

**特点**：
把计算视为函数而非指令
纯函数式编程：无需变量，没有副作用，测试简单
支持高阶函数，代码简洁

**Python支持的函数式编程**：
不是纯函数式编程：允许有变量
支持告诫函数：函数也可以作为变量传入
支持闭包：有了闭包就能返回函数
有限度地支持匿名函数

#### Python高阶函数

变量可以指向一个函数
函数名 就是指向函数的一个变量 和普通变量没有区别




### 高阶函数

能接受函数作为参数的函数

*demo*：

```python
def add(x, y, f)
...    return f(x) + f(y)
...
>>>add(-5, 9, abs)
14
```

#### map()函数
*demo*1:

```python
def f(x):
    return x*x

>>>map(f, [1, 2, 3, 4, 5, 6, 7, 8, 9])
[1, 4, 9, 10, 25, 36, 49, 64, 81]
```

*demo*2:

```python
def format_name(s):
    return s[0].upper()+s[1:].lower()
	# return s.capitalize() #首字母大写
print map(format_name, ['adam', 'LISA', 'barT'])
```

#### reduce()函数

接收的参数和map()类似，一个函数f，一个list。
但行为不同，reduce()传入函数f的参数必须接受两个参数，reduce()对list的每个参数反复调用函数f，并返回最终结果

*demo1*:

```python
def f(x, y):
    return x + y
reduce(f, [1, 3, 5, 7, 9]) #返回25  先计算头两个元素：f(1, 3)，结果为4,...,结果和第5个元素计算：f(16, 9)，结果为25

reduce(f, [1, 3, 5, 7, 9], 100) #第三个参数作为初始值
```

*demo2*：

```python
def prod(x, y):  #求积函数
	return x * y

print reduce(prod, [2, 4, 5, 7, 12])
```

#### filter()函数

fileter()函数接收一个函数f和一个list，函数f的作用是对list中每一个元素进行判断，返回True或False
filter()根据判断结果自动过滤掉不符合条件的元素，返回由符合条件元素组成的新list

*demo1*：

```python
def is_odd(x):
    return x % 2 == 1

>>>filter(is_odd, [1, 4, 6, 7, 9, 12, 17]) #过滤偶数
```

*demo2*：

```python
def is_not_empty(s):
    return s and len(s.strip()) > 0 
#s.strip()删除空白字符(包括'\n', '\r', '\t', ' ')
#s.strip('rm') 删除 s 字符串中开头、结尾处的 rm 序列的字符

>>>filter(is_not_empty, ['test', None, '', 'str', '  ', 'END']) #删除None或空字符串
['test', 'str', 'END']
```

*demo3*:

```python
import math
def is_sqr(x):
    return math.sqrt(x) % 1 == 0  #math.sqrt()返回float类型 

>>>filter(is_sqr, range(1, 101))
```

#### sorted()函数

sorted()也是一个高阶函数，它可以接收一个比较函数来实现自定义排序，
比较函数的定义是，传入两个待比较的元素 x, y；
如果 x 应该排在 y 的前面，返回 -1，如果 x 应该排在 y 的后面，返回 1。如果 x 和 y 相等，返回 0

*demo1*：

```python
def reversed_cmp(x, y): #实现倒序
    if x > y:
    	return -1
	if x < y:
    	return 1
	return 0

>>>sorted([36, 5, 12, 9, 21], reversed_cmp)
[36, 21, 12, 9, 5]
>>>sorted(['bob', 'about', 'Zoo', 'Credit']) #'Zoo'排在'about'之前是因为'Z'的ASCII码比'a'小
['Credit', 'Zoo', 'about', 'bob']
```




### python中返回函数
*demo1*：

```python
def f():
    print 'call f()...'
    # 定义函数g:
	def g():
    	print 'call g()...'
    # 返回函数g:
	return g 

>>>x = f()   # 调用f()
call f()...

>>>x   # 变量x是f()返回的函数：
<function g at 0x1037bf320>

>>>x()   # x指向函数，因此可以调用
call g()...   # 调用x()就是执行g()函数定义的代码
```

**注意区别返回函数g和返回函数值g()的区别
**返回函数可以“延迟计算”



### python中闭包

内层函数引用了外层函数的变量（参数也算是变量），然后返回内层函数的情况，称为闭包（Closure)。

闭包的特点是返回的函数还引用了外层函数的局部变量（比如for循环中的i），
所以，要正确使用闭包，就要确保引用的局部变量在函数返回后不能发生改变

*demo1*：

```python
def count():
    fs = []
    for i in range(1, 4):
        def f(i):
        	return lambda : i*i
		#这里返回的是一个匿名函数    
    	fs.append(f(i))
	return fs

f1, f2, f3 = count()
print f1(), f2(), f3()

>>>count() 
[<function __main__.count.<locals>.f.<locals>.<lambda>>,
 <function __main__.count.<locals>.f.<locals>.<lambda>>,
 <function __main__.count.<locals>.f.<locals>.<lambda>>]
#count()返回的是三个函数
```

*demo2*:

```python
def count():
    fs = []
    for i in range(1, 4):
        def f(i):
        	return i*i
    	#这里返回的是一个函数值
    	fs.append(f(i))
    return f

f1, f2, f3 = count()
print f1(), f2(), f3()

>>>count()
[1,4,9]
```



### python中的匿名函数

高阶函数可以接收函数做参数，有些时候，我们不需要显式地定义函数，直接传入匿名函数更方便

```python
lambda x: x * x  #冒号前面的x表示函数参数

'''等价于'''

def f(x):
    return x * x
```

匿名函数有个限制，就是只能有一个表达式，可不必定义函数名，不写return，返回值就是该表达式的结果。

```Python
>>>sorted([1, 3, 9, 5, 0], lambda x,y: -cmp(x,y))
[9, 5, 3, 1, 0]
```

返回函数的时候，也可以返回匿名函数



### python中的decorator装饰器

装饰器的作用:
打印日志： @log
检查性能： @performance
数据库事物：@transaction
URL路由：@post('/register')

#### 1).无参数的decorator

*demo1*: 

```python
def log(f):
    def fn(*args, **kw):
    	print 'call ' + f.__name__ + '()...'
    	return f(*args, **kw)
	return fn

@log
def add(x, y):
    return x + y
print add(1, 2)

@log
def factorial(n):  #阶乘
    return reduce(lambda x,y: x*y, range(1, n+1))
print factorial(10)
```

*demo2*：

```python
#计算函数调用的时间可以记录调用前后的当前时间戳，然后计算两个时间戳的差
import time
def performance(f):
    def fn(*args, **kw):
    t1 = time.time()
    r = f(*args, **kw)
    t2 = time.time()
    print 'call %s() in %fs' % (f.__name__, (t2 - t1))
    return r
return fn

@performance
def factorial(n):
    return reduce(lambda x,y: x*y, range(1, n+1))
print factorial(10)
```

#### 2).有参数的decorator（三层嵌套decorator）

带参数的log函数首先返回一个decorator函数，再让这个decorator函数接收my_func并返回新函数

*demo1*：

```python
def log(prefix):
    def log_decorator(f):
    	def wrapper(*args, **kw):
        	print '[%s] %s()...' % (prefix, f.__name__)
        	return f(*args, **kw)
    	return wrapper
	return log_decorator

@log('DEBUG')
def test():
	pass

print test()
```

*execute*：

```python
[DEBUG] test()...
None
```

*demo2*：

```python
import time
def performance(unit):
	def perf_decorator(f):
    def wrapper(*args, **kw):
        t1 = time.time()
        r = f(*args, **kw)
        t2 = time.time()
        t = (t2 - t1) * 1000 if unit=='ms' else (t2 - t1)
        print 'call %s() in %f %s' % (f.__name__, t, unit)
        return r
    return wrapper
return perf_decorator

@performance('ms')
def factorial(n):
    return reduce(lambda x,y: x*y, range(1, n+1))
print factorial(10)
```

#### 3).完善decorator

Python内置的functools可以用来自动化完成这个“复制”函数属性的任务

*demo1*：

```python
def log(f):
    @functools.wraps(f)
	def wrapper(x):
    	print 'call...'
    	return f(x)
	return wrapper
```



### python中的偏函数

functools.partial就是帮助我们创建一个偏函数
把一个参数多的函数变成一个参数少的新函数，少的参数需要在创建时指定默认值
functools.partial(函数名，固定参数)

*demo1*:

```python
def int2(x, base=2):
	return int(x, base)

>>>int2('1000000')
64

'''<=>'''

import functools
int2 = functools.partial(int, base=2)

>>>int2('1000000')
64

```

*demo2*:

```python
import functools
sorted_ignore_case = functools.partial(sorted, cmp=lambda s1, s2: cmp(s1.upper(), s2.upper()))

print sorted_ignore_case(['bob', 'about', 'Zoo', 'Credit'])
```



### python 中的模块和包

 每个包中必须有__init__.py文件

 *动态导入模块*：

```python
 try:
 	from cStringIO import StringIO #cStringIO是c语言编写的，执行速度更快
 except ImportError:
    from StringIO import StringIO
```



### python中限制访问

 Python对属性权限的控制是通过属性名来实现的，
 如果一个属性由双下划线开头(__)，该属性就无法被外部访问。

只有以双下划线开头的"__job"不能直接被外部访问。

但是，如果一个属性以"__xxx__"的形式定义，那它又可以被外部访问了，
以"__xxx__"定义的属性在Python的类中被称为特殊属性，有很多预定义的特殊属性可以使用，
通常我们不要把普通属性用"__xxx__"定义。

以单下划线开头的属性"_xxx"虽然也可以被外部访问，但是，按照习惯，他们不应该被外部访问。




### python 定义类
当创建实例时，__init__()方法被自动调用

```Python
class Person(object):
    def __init__(self, name, gender, birth, job):
        self.name = name
        self.gender = gender
        self.birth = birth
        self.job = job
    
xiaoming = Person('Xiao Ming', 'Male', '1990-1-1', job='Student')
```

实例属性每个实例各自拥有，互相独立，而类属性有且只有一份。
当实例属性和类属性重名时，实例属性优先级高

*demo1*：

```python
class Person(object):
    __count = 0
	def __init__(self, name):
      	Person.__count = Person.__count + 1
    	self.name = name
    	print Person.__count  #__count只能在类内部被访问
p1 = Person('Bob')
p2 = Person('Alice')
print Person.count  #不能访问count
```

**在类中定义实例方法**：

```python
class Person(object):
    def __init__(self, name, score):
    self.__name = name
    self.__score = score

    def get_grade(self):
        if self.__score >= 80:
            return 'A'
        if self.__score >= 60:
            return 'B'
        return 'C'
    
p1 = Person('Bob', 90)
p2 = Person('Alice', 65)
p3 = Person('Tim', 48)
print p1.get_grade()
print p2.get_grade()
print p3.get_grade()
```

函数调用不需要传入 self，但是方法调用需要传入 self：

由于属性可以是普通的值对象，如 str，int 等，也可以是方法，还可以是函数，
大家看看下面代码的运行结果，请想一想 p1.get_grade 为什么是函数而不是方法：


```Python
class Person(object):
	def __init__(self, name, score):
      	self.name = name
      	self.score = score
      	self.get_grade = lambda: 'A'
        
p1 = Person('Bob', 90)
print p1.get_grade  #这里返回的是函数
print p1.get_grade()
```



### python中定义类方法

和属性类似，方法也分实例方法和类方法
可以通过类方法获取类的私有属性

#### 1).继承

继承树从object开始
继承是is关系
总是从某个类继承

```Python
class Myclass(object):
	pass
```

继承父类需要在初始化
注意self参数已在super()中传入，在__init__()中将隐式传递，不需要写出（也不能写）。
```Python
def init(self, args):
	super(SubClass, self).__init__(args)
	pass
```

在一条继承链上，
一个父类的实例不能是子类类型，因为子类比父类多了一些属性和方法
一个实例可以看成它本身的类型，也可以看成它父类的类型

#### 2).多态

当实例调用某一个方法时总是先查找自身的定义，
如果没有定义，则顺着继承链向上查找，直到在某个父类中找到为止

动态语言和静态语言（例如Java）最大的差别之一：
动态语言调用实例方法，不检查类型，只要方法存在，参数正确，就可以调用

由于Python的动态特性，json.load()并不一定要从一个File对象读取内容。
任何对象，只要有read()方法，就称为File-like Object，都可以传给json.load()

```Python
import json
class Students(object):
  def read(self):
      return r'["Tim", "Bob", "Alice"]'
    
s = Students()
print json.load(s)
```

#### 3).多重继承

python获取对象信息：
type() 函数获取变量的类型
dir() 函数获取变量的所有属性，dir()返回的属性是字符串列表
如果已知一个属性名称，要获取或者设置对象的属性，就需要用 getattr() 和 setattr( )函数了
传入**kw 即可传入任意数量的参数，并通过 setattr() 绑定属性

```Python
class Person(object):
    def __init__(self, name, gender, **kw):
        self.name = name
        self.gender = gender
        for k, v in kw.iteritems():
            setattr(self, k, v)
p = Person('Bob', 'Male', age=18, course='Python')
print p.age
print p.course
```




### 特殊方法
任何数据类型的实例都有一个特殊方法`  __str__()`

`print lst` <=> `print lst.__str__()`

一些特殊方法：
用于print的`__str__`
用于len的`__len__`
用于cmp的`__cmp__`
...

特点：
定义在class中
不需要直接调用
Python的某些函数或者操作符会调用对应的特殊方法

只需要编写用到的特殊方法
有关联性的特殊方法都必须实现
eg: `__getattr__`
	__setattr__
	__delattr__

`__str__` 和` __repe__`:
`__str__()`用于显示给用户，而`__repr__()`用于显示给开发人员
demo:

```Python
class Person(object):
    def __init__(self, name, gender):
        self.name = name
        self.gender = gender
    def __str__(self):
        return '(Person: %s, %s)' % (self.name, self.gender)
	__repr__ = __str__
```

` __cmp__`:
 对 int、str 等内置数据类型排序时，Python的 sorted() 按照默认的比较函数 cmp 排序，
 但是，如果对一组 Student 类的实例排序时，就必须提供我们自己的特殊方法 `__cmp__()`


@property:
python支持高阶函数，在函数式编程中我们介绍了装饰器函数，
可以用装饰器函数把 get/set 方法“装饰”成属性调用
```Python
class Student(object):
    def __init__(self, name, score):
        self.name = name
        self.__score = score
    @property
    def score(self):
        return self.__score
    @score.setter
    def score(self, score):
        if score < 0 or score > 100:
            raise ValueError('invalid score')
        self.__score = score
```

 注意: 第一个score(self)是get方法，用@property装饰，第二个score(self, score)是set方法，用@score.setter装饰，@score.setter是前一个@property装饰后的副产品。

```python
>>> s = Student('Bob', 59)
>>> s.score = 60
>>> print s.score
60

>>> s.score = 1000
Traceback (most recent call last):
    ...
ValueError: invalid score
```


所有的函数都是可调用对象。

一个类实例也可以变成一个可调用对象，只需要实现一个特殊方法`__call__()`



