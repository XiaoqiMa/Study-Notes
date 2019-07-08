Python核心技术与实战

[TOC]

### 03 | 列表和元组，到底用哪一个？

```python
# 创建空列表
# option A
empty_list = list()

# option B
empty_list = []

区别主要在于list()是一个function call，Python的function call会创建stack，并且进行一系列参数检查的操作，比较expensive，反观[]是一个内置的C函数，可以直接被调用，因此效率高。
```

元素不需要改变时: 两三个元素，使用 tuple，元素多一点使用namedtuple。
元素需要改变时: 需要高效随机读取，使用list。需要关键字高效查找，采用 dict。去重，使用 set。大型数据节省空间，使用标准库 array。大型数据高效操作，使用 numpy.array。

### 04 | 字典、集合，你真的了解吗？

```python
# Option A
d = {'name': 'jason', 'age': 20, 'gender': 'male'}

# Option B
d = dict({'name': 'jason', 'age': 20, 'gender': 'male'})


思考题 1：
第一种方法更快，原因感觉上是和之前一样，就是不需要去调用相关的函数，而且像老师说的那样 {} 应该是关键字，内部会去直接调用底层C写好的代码

思考题 2:
用列表作为 Key 在这里是不被允许的，因为列表是一个动态变化的数据结构，字典当中的 key 要求是不可变的，原因也很好理解，key 首先是不重复的，如果 Key 是可以变化的话，那么随着 Key 的变化，这里就有可能就会有重复的 Key，那么这就和字典的定义相违背；如果把这里的列表换成之前我们讲过的元组是可以的，因为元组不可变
```

### 05 | 深入浅出字符串

```python
最后，给你留一道思考题。在新版本的 Python（2.5+）下面的两个字符串拼接操作，你觉得哪个更优呢?
(1)
s = ''
for n in range(0, 100000):
    s += str(n)
(2)
l = []
for n in range(0, 100000):
    l.append(str(n))
    
s = ' '.join(l)
(3)
个人提一个更加pythonic，更加高效的办法
s = " ".join(map(str, range(0, 10000)))
综上，方式三性能最优，其次是在超过1000万条数据以上时，方式二优于方式一，相反，方式一优于方式二。

       
```

### 06 | Python “黑箱”：输入与输出

- json.dumps() 这个函数，接受 Python 的基本数据类型，然后将其序列化为 string；
- 而 json.loads() 这个函数，接受一个合法字符串，然后将其反序列化为 Python 的基本数据类型。
- 不过还是那句话，请记得加上错误处理。不然，哪怕只是给json.loads() 发送了一个非法字符串，而你没有catch 到，程序就会崩溃了
- dumps(), loads(); dump(), load() -->  (带s的：就是用来进行数据类型的转换。不带s的：只能跟文件结合一起使用。)

```python
import json

params = {
    'symbol': '123456',
    'type': 'limit',
    'price': 123.4,
    'amount': 23
}

with open('params.json', 'w') as fout:
    params_str = json.dump(params, fout)

with open('params.json', 'r') as fin:
    original_params = json.load(fin)

print('after json deserialization')
print('type of original_params = {}, original_params = {}'.format(type(original_params), original_params))

########## 输出 ##########

after json deserialization
type of original_params = <class 'dict'>, original_params = {'symbol': '123456', 'type': 'limit', 'price': 123.4, 'amount': 23}

```

```python
"""
从文件读取字符，进行word count
"""
from collections import defaultdict
import re

f = open("ini.txt", mode="r", encoding="utf-8")
d = defaultdict(int)

for line in f:
    for word in filter(lambda x: x, re.split(r"\s", line)):
        d[word] += 1


print(d)

#  filter(None, Iterable) 是一种容易出错的用法，这里不止过滤空字符串，还能过滤 0，None，空列表等值。这里的 None，严格意义上等于 lambda x: x, 是一个 callable

```

```python
思考题第一题：

import re

CHUNK_SIZE = 100 # 这个数表示一次最多读取的字符长度

# 这个函数每次会接收上一次得到的 last_word，然后和这次的 text 合并起来处理。
# 合并后判断最后一个词有没有可能连续，并分离出来，然后返回。
# 这里的代码没有 if 语句，但是仍然是正确的，可以想一想为什么。
def parse_to_word_list(text, last_word, word_list):
    text = re.sub(r'[^\w ]', ' ', last_word + text)
    text = text.lower()
    cur_word_list = text.split(' ')
    cur_word_list, last_word = cur_word_list[:-1], cur_word_list[-1]
    word_list += filter(None, cur_word_list)
    return last_word

def solve():
    with open('in.txt', 'r') as fin:
        word_list, last_word = [], ''
        while True:
            text = fin.read(CHUNK_SIZE)
            if not text: 
                break # 读取完毕，中断循环
            last_word = parse_to_word_list(text, last_word, word_list)

        word_cnt = {}
        for word in word_list:
            if word not in word_cnt:
                word_cnt[word] = 0
            word_cnt[word] += 1

        sorted_word_cnt = sorted(word_cnt.items(), key=lambda kv: kv[1], reverse=True)
        return sorted_word_cnt

print(solve())
```



### 07 | 修炼基本功：条件与循环

```python
当我们同时需要索引和元素时，还有一种更简洁的方式，那就是通过 Python 内置的函数 enumerate()。用它来遍历集合，不仅返回每个元素，并且还返回其对应的索引，这样一来，上面的例子就可以写成:
l = [1, 2, 3, 4, 5, 6, 7]
for index, item in enumerate(l):
    if index < 5:
        print(item)  

```

```python
for item in iterable:
    if condition:
        expression1
    else:
        expression2

--> expression1 if condition else expression2 for item in iterable

y = [value * 2 + 5 if value > 0 else -value * 2 + 5 for value in x]

```

```python
思考题
attributes = ['name', 'dob', 'gender']
values = [['jason', '2000-01-01', 'male'], 
['mike', '1999-01-01', 'male'],
['nancy', '2001-02-01', 'female']]

# solution
[dict(zip(attributes,v)) for v in values]

```

### 08 | 异常处理：如何提高程序的稳定性？

```python
try:
    s = input('please enter two numbers separated by comma: ')
    num1 = int(s.split(',')[0].strip())
    num2 = int(s.split(',')[1].strip())
    ...
except (ValueError, IndexError) as err:
    print('Error: {}'.format(err))
    
print('continue')

#不过，很多时候，我们很难保证程序覆盖所有的异常类型，所以，更通常的做法，是在最后一个 except block，声明其处理的异常类型是 Exception。Exception 是其他所有非系统异常的基类，能够匹配任意非系统异常。那么这段代码就可以写成下面这样
try:
    s = input('please enter two numbers separated by comma: ')
    num1 = int(s.split(',')[0].strip())
    num2 = int(s.split(',')[1].strip())
except ValueError as err:
    print('Value Error: {}'.format(err))
except IndexError as err:
    print('Index Error: {}'.format(err))
except Exception as err:
    print('Other error: {}'.format(err))

print('continue')
#或者，你也可以在 except 后面省略异常类型，这表示与任意异常相匹配（包括系统异常等）：
except:
    print('other error')
```

异常处理中，还有一个很常见的用法是 finally，经常和 try、except 放在一起来用。无论发生什么情况，finally block 中的语句都会被执行，哪怕前面的 try 和 excep block 中使用了 return 语句。

```python
import sys
try:
    f = open('file.txt', 'r')
    # some data processing
except OSError as err:
    print('OS error: {}'.format(err))
except:
    print('Unexpected error:', sys.exc_info()[0])
finally:
    f.close()

```

### 09 | 不可或缺的自定义函数

其实，函数的嵌套，主要有下面两个方面的作用。

- 第一，函数的嵌套能够保证内部函数的隐私。内部函数只能被外部函数所调用和访问，不会暴露在全局作用域，因此，如果你的函数内部有一些隐私数据（比如数据库的用户、密码等），不想暴露在外，那你就可以使用函数的的嵌套，将其封装在内部函数中，只通过外部函数来访问。比如：

- ```python
  def connect_DB():
      def get_DB_configuration():
          ...
          return host, username, password
      conn = connector.connect(get_DB_configuration())
      return conn
  # 我们只能通过调用外部函数 connect_DB() 来访问它，这样一来，程序的安全性便有了很大的提高。
  ```

- 第二，合理的使用函数嵌套，能够提高程序的运行效率。我们来看下面这个例子：

- ```python
  def factorial(input):
      # validation check
      if not isinstance(input, int):
          raise Exception('input must be an integer.')
      if input < 0:
          raise Exception('input must be greater or equal to 0' )
      ...
  
      def inner_factorial(input):
          if input <= 1:
              return 1
          return input * inner_factorial(input-1)
      return inner_factorial(input)
  
  
  print(factorial(5))
  
  # 这里，我们使用递归的方式计算一个数的阶乘。因为在计算之前，需要检查输入是否合法，所以我写成了函数嵌套的形式，这样一来，输入是否合法就只用检查一次。而如果我们不使用函数嵌套，那么每调用一次递归便会检查一次，这是没有必要的，也会降低程序的运行效率。
  ```



这里的 global 关键字，并不表示重新创建了一个全局变量 MIN_VALUE，而是告诉 Python 解释器，函数内部的变量 MIN_VALUE，就是之前定义的全局变量，并不是新的全局变量，也不是局部变量。这样，程序就可以在函数内部访问全局变量，并修改它的值了。

```python
MIN_VALUE = 1
MAX_VALUE = 10
def validation_check(value):
    global MIN_VALUE
    ...
    MIN_VALUE += 1
    ...
validation_check(5)


```

- 闭包 (合理地使用闭包，则可以简化程序的复杂度，提高可读性。, 闭包常常和装饰器（decorator）一起使用。)

- ```python
  def nth_power(exponent):
      def exponent_of(base):
          return base ** exponent
      return exponent_of # 返回值是 exponent_of 函数
  
  square = nth_power(2) # 计算一个数的平方
  cube = nth_power(3) # 计算一个数的立方 
  square
  # 输出
  <function __main__.nth_power.<locals>.exponent(base)>
  
  cube
  # 输出
  <function __main__.nth_power.<locals>.exponent(base)>
  
  print(square(2))  # 计算 2 的平方
  print(cube(2)) # 计算 2 的立方
  # 输出
  4 # 2^2
  8 # 2^3
  
  ```

- 闭包必须使用嵌套函数，一看到闭包我首先想到的是 JavaScript 里面的回调函数。闭包这里看似仅仅返回了一个嵌套函数，但是需要注意的是，**它其实连同嵌套函数的外部环境变量也一同保存返回回来了**（例子中的 exponent 变量），这个环境变量是在调用其外部函数时设定的，这样一来，对于一些参数性，不常改变的设定变量，我们可以通过这个形式来设定，这样返回的闭包函数仅需要关注那些核心输入变量，节省了效率，这样做也大大减少了全局变量的使用，增加代码可读性的同时，也会让代码变得更加的安全

### 10 | 简约不简单的匿名函数

所谓函数式编程，是指代码中每一块都是不可变的（immutable），都由纯函数（pure function）的形式组成。这里的纯函数，是指函数本身相互独立、互不影响，对于相同的输入，总会有相同的输出，没有任何副作用。函数式编程的优点，主要在于其纯函数和不可变的特性使程序更加健壮，易于调试（debug）和测试；缺点主要在于限制多，难写。当然，Python 不同于一些语言（比如 Scala），它并不是一门函数式编程语言，不过，Python 也提供了一些函数式编程的特性，值得我们了解和学习。

```python
def multiply_2_pure(l):
    new_list = []
    for item in l:
        new_list.append(item * 2)
    return new_list

```

通常来说，在我们想对集合中的元素进行一些操作时，如果操作非常简单，比如相加、累积这种，那么我们优先考虑 map()、filter()、reduce() 这类或者 list comprehension 的形式。至于这两种方式的选择：

- 在数据量非常多的情况下，比如机器学习的应用，那我们一般更倾向于函数式编程的表示，因为效率更高；
- 在数据量不多的情况下，并且你想要程序更加 Pythonic 的话，那么 list comprehension 也不失为一个好选择。

### 11 | 面向对象（上）：从生活中的类比说起

这里唯一需要强调的一点是，如果一个属性以 __ （注意，此处有两个 _） 开头，我们就默认这个属性是私有属性。私有属性，是指不希望在类的函数之外的地方被访问和修改的属性。所以，你可以看到，title 和 author 能够很自由地被打印出来，但是 print(harry_potter_book.__context) 就会报错。

- 如何在一个类中定义一些常量，每个对象都可以方便访问这些常量而不用重新构造？
- 如果一个函数不涉及到访问修改这个类的属性，而放到类外面有点不恰当，怎么做才能更优雅呢？
- 既然类是一群相似的对象的集合，那么可不可以是一群相似的类的集合呢？

```python
class Document():
    
    WELCOME_STR = 'Welcome! The context for this book is {}.'
    
    def __init__(self, title, author, context):
        print('init function called')
        self.title = title
        self.author = author
        self.__context = context
    
    # 类函数
    @classmethod
    def create_empty_book(cls, title, author):
        return cls(title=title, author=author, context='nothing')
    
    # 成员函数
    def get_context_length(self):
        return len(self.__context)
    
    # 静态函数
    @staticmethod
    def get_welcome(context):
        return Document.WELCOME_STR.format(context)


empty_book = Document.create_empty_book('What Every Man Thinks About Apart from Sex', 'Professor Sheridan Simove')


print(empty_book.get_context_length())
print(empty_book.get_welcome('indeed nothing'))

########## 输出 ##########

init function called
7
Welcome! The context for this book is indeed nothing.

```

而针对第二个问题，我们提出了类函数、成员函数和静态函数三个概念。它们其实很好理解，前两者产生的影响是动态的，能够访问或者修改对象的属性；而静态函数则与类没有什么关联，最明显的特征便是，静态函数的第一个参数没有任何特殊性。

具体来看这几种函数。一般而言，静态函数可以用来做一些简单独立的任务，既方便测试，也能优化代码结构。静态函数还可以通过在函数前一行加上 @staticmethod 来表示，代码中也有相应的示例。这其实使用了装饰器的概念，我们会在后面的章节中详细讲解。

而类函数的第一个参数一般为 cls，表示必须传一个类进来。类函数最常用的功能是实现不同的 init  构造函数，比如上文代码中，我们使用 create_empty_book 类函数，来创造新的书籍对象，其 context 一定为 'nothing' 。这样的代码，就比你直接构造要清晰一些。类似的，类函数需要装饰器 @classmethod 来声明。

成员函数则是我们最正常的类的函数，它不需要任何装饰器声明，第一个参数 self 代表当前对象的引用，可以通过此函数，来实现想要的查询 / 修改类的属性等功能。



**抽象类是一种特殊的类，它生下来就是作为父类存在的，一旦对象化就会报错。同样，抽象函数定义在抽象类之中，子类必须重写该函数才能使用。相应的抽象函数，则是使用装饰器 @abstractmethod 来表示。**

第一个问题，面向对象编程四要素是什么？它们的关系又是什么？
答：面向对象编程四要素是类，属性，函数，对象，
   四要素：封装、继承、抽象、多态；
   封装：一组相似特征对象的集合，即类
   继承：子类继承父类
   抽象：接口，通过继承实现具体方法或初始化属性
   多态：同一个父类函数可在子类中实现不同的过程
第二个问题，讲了这么久的继承，继承究竟是什么呢？你能用三个字表达出来吗？
三个字：父与子。儿子可以使用自己的东西，没有的可以使用父亲的东西。

### 12 | 面向对象（下）：如何实现一个搜索引擎？

一个搜索引擎由搜索器、索引器、检索器和用户接口四个部分组成

```python
class SearchEngineBase(object):
    def __init__(self):
        pass

    def add_corpus(self, file_path):
        with open(file_path, 'r') as fin:
            text = fin.read()
        self.process_corpus(file_path, text)

    def process_corpus(self, id, text):
        raise Exception('process_corpus not implemented.')

    def search(self, query):
        raise Exception('search not implemented.')

def main(search_engine):
    for file_path in ['1.txt', '2.txt', '3.txt', '4.txt', '5.txt']:
        search_engine.add_corpus(file_path)

    while True:
        query = input()
        results = search_engine.search(query)
        print('found {} result(s):'.format(len(results)))
        for result in results:
            print(result)

           
 class SimpleEngine(SearchEngineBase):
    def __init__(self):
        super(SimpleEngine, self).__init__()
        self.__id_to_texts = {}

    def process_corpus(self, id, text):
        self.__id_to_texts[id] = text

    def search(self, query):
        results = []
        for id, text in self.__id_to_texts.items():
            if query in text:
                results.append(id)
        return results

search_engine = SimpleEngine()
main(search_engine)


########## 输出 ##########


simple
found 0 result(s):
little
found 2 result(s):
1.txt
2.txt

```

```python
# Bag of words
import re

class BOWEngine(SearchEngineBase):
    def __init__(self):
        super(BOWEngine, self).__init__()
        self.__id_to_words = {}

    def process_corpus(self, id, text):
        self.__id_to_words[id] = self.parse_text_to_words(text)

    def search(self, query):
        query_words = self.parse_text_to_words(query)
        results = []
        for id, words in self.__id_to_words.items():
            if self.query_match(query_words, words):
                results.append(id)
        return results
    
    @staticmethod
    def query_match(query_words, words):
        for query_word in query_words:
            if query_word not in words:
                return False
        return True

    @staticmethod
    def parse_text_to_words(text):
        # 使用正则表达式去除标点符号和换行符
        text = re.sub(r'[^\w ]', ' ', text)
        # 转为小写
        text = text.lower()
        # 生成所有单词的列表
        word_list = text.split(' ')
        # 去除空白单词
        word_list = filter(None, word_list)
        # 返回单词的 set
        return set(word_list)

search_engine = BOWEngine()
main(search_engine)


########## 输出 ##########


i have a dream
found 3 result(s):
1.txt
2.txt
3.txt
freedom children
found 1 result(s):
5.txt

```

### 13 | 搭建积木：Python 模块化

最后我想再提一下版本区别。你可能在许多教程中看到过这样的要求：我们还需要在模块所在的文件夹新建一个`__init__.py`，内容可以为空，也可以用来表述包对外暴露的模块接口。不过，事实上，这是 Python 2 的规范。在 Python 3 规范中， `__init__.py`并不是必须的，很多教程里没提过这一点，或者没讲明白，我希望你还是能注意到这个地方。



首先，你会发现，相对位置是一种很不好的选择。因为代码可能会迁移，相对位置会使得重构既不雅观，也易出错。因此，在大型工程中尽可能使用绝对位置是第一要义。对于一个独立的项目，所有的模块的追寻方式，最好从项目的根目录开始追溯，这叫做相对的绝对路径。

实际上，Python 解释器在遇到 import 的时候，它会在一个特定的列表中寻找模块。这个特定的列表，可以用下面的方式拿到：

```python
import sys  

print(sys.path)

########## 输出 ##########

['', '/usr/lib/python36.zip', '/usr/lib/python3.6', '/usr/lib/python3.6/lib-dynload', '/usr/local/lib/python3.6/dist-packages', '/usr/lib/python3/dist-packages']

```



import 在导入文件的时候，会自动把所有暴露在外面的代码全都执行一遍。因此，如果你要把一个东西封装成模块，又想让它可以执行的话，你必须将要执行的代码放在 if `__name__ == '__main__'` 下面。

```python
# utils_with_main.py

def get_sum(a, b):
    return a + b

if __name__ == '__main__':
    print('testing')
    print('{} + {} = {}'.format(1, 2, get_sum(1, 2)))

```

### 15 | Python对象的比较、拷贝

等于 == 和 is 是 Python 中对象比较常用的两种方式。简单来说， ==操作符比较对象之间的值是否相等，比如下面的例子，表示比较变量 a 和 b 所指向的值是否相等。'is' 操作符比较的是对象的身份标识是否相等，即它们是否是同一个对象，是否指向同一个内存地址。

```python
a = 257
b = 257

a == b
True

id(a)
4473417552

id(b)
4473417584

a is b
False
# 事实上，出于对性能优化的考虑，Python 内部会对 -5 到 256 的整型维持一个数组，起到一个缓存的作用。这样，每次你试图创建一个 -5 到 256 范围内的整型数字时，Python 都会从这个数组中返回相对应的引用，而不是重新开辟一块新的内存空间。但是，如果整型数字超过了这个范围，比如上述例子中的 257，Python 则会为两个 257 开辟两块内存区域，因此 a 和 b 的 ID 不一样，
```

到这里，对于浅拷贝你应该很清楚了。浅拷贝，是指重新分配一块内存，创建一个新的对象，里面的元素是原对象中**子对象**的引用。因此，如果原对象中的元素不可变，那倒无所谓；但如果元素可变，浅拷贝通常会带来一些副作用，尤其需要注意。我们来看下面的例子：

```python
l1 = [[1, 2], (30, 40)]
l2 = list(l1)
l1.append(100)
l1[0].append(3)

l1
[[1, 2, 3], (30, 40), 100]

l2
[[1, 2, 3], (30, 40)]

l1[1] += (50, 60)
l1
[[1, 2, 3], (30, 40, 50, 60), 100]

l2
[[1, 2, 3], (30, 40)]

```

```python
#浅拷贝，不可变的不可变，可变的依旧可变
#深拷贝，都不可变
a = [1, 2, 3]
b = a.copy()
b.append(4)
a, b

###output
([1, 2, 3], [1, 2, 3, 4])

a = [[1, 2, 3]] # 子对象是list，可变
b = a.copy()
b[0].append(4)
a, b
###outpout
([[1, 2, 3, 4]], [[1, 2, 3, 4]])
```



所谓深度拷贝，是指重新分配一块内存，创建一个新的对象，并且将原对象中的元素，以递归的方式，通过创建新的子对象拷贝到新对象中。因此，新对象和原对象没有任何关联。

### 16 | 值传递，引用传递or其他，Python里参数是如何传递的？

- 变量的赋值，只是表示让变量指向了某个对象，并不表示拷贝对象给变量；而一个对象，可以被多个变量所指向。
- 可变对象（列表，字典，集合等等）的改变，会影响所有指向该对象的变量。
- 对于不可变对象（字符串，整型，元祖等等），所有指向该对象的变量的值总是一样的，也不会改变。但是通过某些操作（+= 等等）更新不可变对象的值时，会返回一个新的对象。
- 变量可以被删除，但是对象无法被删除。

不过，当可变对象当作参数传入函数里的时候，改变可变对象的值，就会影响所有指向它的变量。比如下面的例子：

```python
def my_func3(l2):
	l2.append(4)

l1 = [1, 2, 3]
my_func3(l1)
l1
[1, 2, 3, 4]

```

和其他语言不同的是，Python 中参数的传递既不是值传递，也不是引用传递，而是赋值传递，或者是叫对象的引用传递。需要注意的是，这里的赋值或对象的引用传递，不是指向一个具体的内存地址，而是指向一个具体的对象。

- 如果对象是可变的，当其改变时，所有指向这个对象的变量都会改变。
- 如果对象不可变，简单的赋值只能改变其中一个变量的值，其余变量则不受影响。

### 17 | 强大的装饰器

第一点，我们要知道，在 Python 中，函数是一等公民（first-class citizen），函数也是对象。我们可以把函数赋予变量，比如下面这段代码：

```python
def func(message):
    print('Got a message: {}'.format(message))
    
send_message = func
send_message('hello world')

# 输出
Got a message: hello world

```

第二点，我们可以把函数当作参数，传入另一个函数中，比如下面这段代码：

```python
def get_message(message):
    return 'Got a message: ' + message


def root_call(func, message):
    print(func(message))
    
root_call(get_message, 'hello world')

# 输出
Got a message: hello world
```

第三点，我们可以在函数里定义函数，也就是函数的嵌套。这里我同样举了一个例子：

```python
def func(message):
    def get_message(message):
        print('Got a message: {}'.format(message))
    return get_message(message)

func('hello world')

# 输出
Got a message: hello world

```

第四点，要知道，函数的返回值也可以是函数对象（闭包），比如下面这个例子：

```python
def func_closure():
    def get_message(message):
        print('Got a message: {}'.format(message))
    return get_message

send_message = func_closure()
send_message('hello world')

# 输出
Got a message: hello world
    
# func_closure()是一个闭包，返回的是函数对象。不能直接用send_message = func_closure，然后send_message('hello world')调用，必须是send_message = func_closure()，然后再send_message('hello world')，这样才能把参数'hello world'正确传给内部函数
```

这里的函数 my_decorator() 就是一个装饰器，它把真正需要执行的函数 greet() 包裹在其中，并且改变了它的行为，但是原函数 greet() 不变。

```python
def my_decorator(func):
    def wrapper():
        print('wrapper of decorator')
        func()
    return wrapper

def greet():
    print('hello world')

greet = my_decorator(greet)
greet()

# 输出
wrapper of decorator
hello world

def my_decorator(func):
    def wrapper():
        print('wrapper of decorator')
        func()
    return wrapper

@my_decorator
def greet():
    print('hello world')

greet()

```

事实上，通常情况下，我们会把`*args和**kwargs`，作为装饰器内部函数 wrapper() 的参数。`*args和**kwargs`表示接受任意数量和类型的参数，因此装饰器就可以写成下面的形式：

```python
def my_decorator(func):
    def wrapper(*args, **kwargs):
        print('wrapper of decorator')
        func(*args, **kwargs)
    return wrapper
```

你会发现，greet() 函数被装饰以后，它的元信息变了。元信息告诉我们“它不再是以前的那个 greet() 函数，而是被 wrapper() 函数取代了”。为了解决这个问题，我们通常使用内置的装饰器@functools.wrap，它会帮助保留原函数的元信息（也就是将原函数的元信息，拷贝到对应的装饰器函数里）。

```python
import functools

def my_decorator(func):
    @functools.wraps(func)
    def wrapper(*args, **kwargs):
        print('wrapper of decorator')
        func(*args, **kwargs)
    return wrapper
    
@my_decorator
def greet(message):
    print(message)

greet.__name__

# 输出
'greet'
```

```python
#类装饰器
class Count:
    def __init__(self, func):
        self.func = func
        self.num_calls = 0

    def __call__(self, *args, **kwargs):
        self.num_calls += 1
        print('num of calls is: {}'.format(self.num_calls))
        return self.func(*args, **kwargs)

@Count
def example():
    print("hello world")

example()

# 输出
num of calls is: 1
hello world

example()

# 输出
num of calls is: 2
hello world

...

```

```python
"""
这段代码中，我们定义了装饰器 authenticate；而函数 post_comment()，则表示发表用户对某篇文章的评论。每次调用这个函数前，都会先检查用户是否处于登录状态，如果是登录状态，则允许这项操作；如果没有登录，则不允许。
"""
import functools

def authenticate(func):
    @functools.wraps(func)
    def wrapper(*args, **kwargs):
        request = args[0]
        if check_user_logged_in(request): # 如果用户处于登录状态
            return func(*args, **kwargs) # 执行函数 post_comment() 
        else:
            raise Exception('Authentication failed')
    return wrapper
    
@authenticate
def post_comment(request, ...)
    ...
 
"""
日志记录同样是很常见的一个案例。在实际工作中，如果你怀疑某些函数的耗时过长，导致整个系统的 latency（延迟）增加，所以想在线上测试某些函数的执行时间，那么，装饰器就是一种很常用的手段。
"""

import time
import functools

def log_execution_time(func):
    @functools.wraps(func)
    def wrapper(*args, **kwargs):
        start = time.perf_counter()
        res = func(*args, **kwargs)
        end = time.perf_counter()
        print('{} took {} ms'.format(func.__name__, (end - start) * 1000))
        return res
    return wrapper
    
@log_execution_time
def calculate_similarity(items):
    ...
    
"""
在大型公司的机器学习框架中，我们调用机器集群进行模型训练前，往往会用装饰器对其输入（往往是很长的 json 文件）进行合理性检查。这样就可以大大避免，输入不正确对机器造成的巨大开销。
"""  
import functools

def validation_check(input):
    @functools.wraps(func)
    def wrapper(*args, **kwargs): 
        ... # 检查输入是否合法
    
@validation_check
def neural_network_training(param1, param2, ...):
    ...

```

这节课，我们一起学习了装饰器的概念及用法。**所谓的装饰器，其实就是通过装饰器函数，来修改原函数的一些功能，使得原函数不需要修改。**

### 18 | [名师分享] metaclass，是潘多拉魔盒还是阿拉丁神灯？

第一，所有的 Python 的用户定义类，都是 type 这个类的实例。

```python
# Python 3 和 Python 2 类似
class MyClass:
  pass

instance = MyClass()

type(instance)
# 输出
<class '__main__.C'>

type(MyClass)
# 输出
<class 'type'>
# 你可以看到，instance 是 MyClass 的实例，而 MyClass 不过是“上帝”type 的实例。
```

第二，用户自定义类，只不过是 type 类的`__call__`运算符重载。

```python

#用户自定义类, Python 真正执行的是下面这段代码：
class = type(classname, superclasses, attributedict)
#这里等号右边的type(classname, superclasses, attributedict)，就是 type 的__call__运算符重载，它会进一步调用：
type.__new__(typeclass, classname, superclasses, attributedict)
type.__init__(class, classname, superclasses, attributedict)


class MyClass:
  data = 1
  
instance = MyClass()
MyClass, instance
# 输出
(__main__.MyClass, <__main__.MyClass instance at 0x7fe4f0b00ab8>)

instance.data
# 输出
1

MyClass = type('MyClass', (), {'data': 1})
instance = MyClass()
MyClass, instance
# 输出
(__main__.MyClass, <__main__.MyClass at 0x7fe4f0aea5d0>)

instance.data
# 输出
1

```

第三，metaclass 是 type 的子类，通过替换 type 的`__call__`运算符重载机制，“超越变形”正常的类。



```python
思考题:
#学完了上节课的 Python 装饰器和这节课的 metaclass，你知道了，它们都能干预正常的 Python 类型机制。那么，你觉得装饰器和 metaclass 有什么区别呢？欢迎留言和我讨论。

之前讲装饰器的时候讲到函数装饰器和类装饰器，而类装饰器就是在雷里面定义了__call__方法，之后在函数执行的时候会调用类的__call__方法。
在metaclass中重载了__call__方法，在使用metaclass实例化生成类的时候也是调用了__call__方法，从这方面来讲是很像。
要说不一样的话，一个是在执行层面，一个是在生成层面。
```

### 19 | 深入理解迭代器和生成器

在 Python 中一切皆对象，对象的抽象就是类，而对象的集合就是容器。列表（list: [0, 1, 2]），元组（tuple: (0, 1, 2)），字典（dict: {0:0, 1:1, 2:2}），集合（set: set([0, 1, 2])）都是容器。对于容器，你可以很直观地想象成多个元素在一起的单元；而不同容器的区别，正是在于内部数据结构的实现方法。然后，你就可以针对不同场景，选择不同时间和空间复杂度的容器。

严谨地说，迭代器（iterator）提供了一个 next 的方法。调用这个方法后，你要么得到这个容器的下一个对象，要么得到一个 StopIteration 的错误. 而可迭代对象，通过 iter() 函数返回一个迭代器，再通过 next() 函数就可以实现遍历。for in 语句将这个过程隐式化，所以，你只需要知道它大概做了什么就行了。

```python
def is_iterable(param):
    try: 
        iter(param) 
        return True
    except TypeError:
        return False

params = [
    1234,
    '1234',
    [1, 2, 3, 4],
    set([1, 2, 3, 4]),
    {1:1, 2:2, 3:3, 4:4},
    (1, 2, 3, 4)
]
    
for param in params:
    print('{} is iterable? {}'.format(param, is_iterable(param)))

########## 输出 ##########

1234 is iterable? False
1234 is iterable? True
[1, 2, 3, 4] is iterable? True
{1, 2, 3, 4} is iterable? True
{1: 1, 2: 2, 3: 3, 4: 4} is iterable? True
(1, 2, 3, 4) is iterable? True

```

**生成器是懒人版本的迭代器**

```python
import os
import psutil

# 显示当前 python 程序占用的内存大小
def show_memory_info(hint):
    pid = os.getpid()
    p = psutil.Process(pid)
    
    info = p.memory_full_info()
    memory = info.uss / 1024. / 1024
    print('{} memory used: {} MB'.format(hint, memory))

def test_iterator():
    show_memory_info('initing iterator')
    list_1 = [i for i in range(100000000)]
    show_memory_info('after iterator initiated')
    print(sum(list_1))
    show_memory_info('after sum called')

def test_generator():
    show_memory_info('initing generator')
    list_2 = (i for i in range(100000000))
    show_memory_info('after generator initiated')
    print(sum(list_2))
    show_memory_info('after sum called')

%time test_iterator()
%time test_generator()

########## 输出 ##########

initing iterator memory used: 48.9765625 MB
after iterator initiated memory used: 3920.30078125 MB
4999999950000000
after sum called memory used: 3920.3046875 MB
Wall time: 17 s
initing generator memory used: 50.359375 MB
after generator initiated memory used: 50.359375 MB
4999999950000000
after sum called memory used: 50.109375 MB
Wall time: 12.5 s

```

于是，生成器的概念应运而生，在你调用 next() 函数的时候，才会生成下一个变量。生成器在 Python 的写法是用小括号括起来，

```python
def generator(k):
    i = 1
    while True:
        yield i ** k
        i += 1

gen_1 = generator(1)
gen_3 = generator(3)
print(gen_1)
print(gen_3)

def get_sum(n):
    sum_1, sum_3 = 0, 0
    for i in range(n):
        next_1 = next(gen_1)
        next_3 = next(gen_3)
        print('next_1 = {}, next_3 = {}'.format(next_1, next_3))
        sum_1 += next_1
        sum_3 += next_3
    print(sum_1 * sum_1, sum_3)

get_sum(8)

########## 输出 ##########

<generator object generator at 0x000001E70651C4F8>
<generator object generator at 0x000001E70651C390>
next_1 = 1, next_3 = 1
next_1 = 2, next_3 = 8
next_1 = 3, next_3 = 27
next_1 = 4, next_3 = 64
next_1 = 5, next_3 = 125
next_1 = 6, next_3 = 216
next_1 = 7, next_3 = 343
next_1 = 8, next_3 = 512
1296 1296

```

接下来的 yield 是魔术的关键。对于初学者来说，你可以理解为，函数运行到这一行的时候，程序会从这里暂停，然后跳出，不过跳到哪里呢？答案是 next() 函数。那么 i ** k  是干什么的呢？它其实成了 next() 函数的返回值。

这个生成器居然可以一直进行下去！没错，事实上，迭代器是一个有限集合，生成器则可以成为一个无限集。我只管调用 next()，生成器根据运算会自动生成新的元素，然后返回给你，非常便捷。

```python
#给定一个 list 和一个指定数字，求这个数字在 list 中的位置。
def index_generator(L, target):
    for i, num in enumerate(L):
        if num == target:
            yield i

print(list(index_generator([1, 6, 2, 4, 5, 2, 8, 6, 3, 2], 2)))

########## 输出 ##########

[2, 5, 9]

```

```python
#接下来我们再来看一个问题：给定两个序列，判定第一个是不是第二个的子序列。

def is_subsequence(a, b):
    b = iter(b)
    return all(i in b for i in a)

print(is_subsequence([1, 3, 5], [1, 2, 3, 4, 5]))
print(is_subsequence([1, 4, 3], [1, 2, 3, 4, 5]))

########## 输出 ##########

True
False

```



- 容器是可迭代对象，可迭代对象调用 iter() 函数，可以得到一个迭代器。迭代器可以通过 next() 函数来得到下一个元素，从而支持遍历。
- 生成器是一种特殊的迭代器（注意这个逻辑关系反之不成立）。使用生成器，你可以写出来更加清晰的代码；合理使用生成器，可以降低内存占用、优化程序结构、提高程序速度。
- 生成器在 Python 2 的版本上，是协程的一种重要实现方式；而 Python 3.5 引入 async await 语法糖后，生成器实现协程的方式就已经落后了。我们会在下节课，继续深入讲解 Python 协程。

**迭代完成后，继续调用 next()会出现StopIteration。生成器只能遍历一次，但是可以重新调用重新遍历。**

### 20 | 揭秘 Python 协程

协程是实现并发编程的一种方式。一说并发，你肯定想到了多线程 / 多进程模型，没错，多线程 / 多进程，正是解决并发问题的经典模型之一。最初的互联网世界，多线程 / 多进程在服务器并发中，起到举足轻重的作用。

如果将多进程 / 多线程类比为起源于唐朝的藩镇割据，那么事件循环，就是宋朝加强的中央集权制。事件循环启动一个统一的调度器，让调度器来决定一个时刻去运行哪个任务，于是省却了多线程中启动线程、管理线程、同步锁等各种开销。同一时期的 NGINX，在高并发下能保持低资源低消耗高性能，相比 Apache 也支持更多的并发连接。

再到后来，出现了一个很有名的名词，叫做回调地狱（callback hell），手撸过 JavaScript 的朋友肯定知道我在说什么。我们大家惊喜地发现，这种工具完美地继承了事件循环的优越性，同时还能提供 async / await 语法糖，解决了执行性和可读性共存的难题。于是，协程逐渐被更多人发现并看好，也有越来越多的人尝试用 Node.js 做起了后端开发。

```python
import time

def crawl_page(url):
    print('crawling {}'.format(url))
    sleep_time = int(url.split('_')[-1])
    time.sleep(sleep_time)
    print('OK {}'.format(url))

def main(urls):
    for url in urls:
        crawl_page(url)

%time main(['url_1', 'url_2', 'url_3', 'url_4'])


import asyncio

async def crawl_page(url):
    print('crawling {}'.format(url))
    sleep_time = int(url.split('_')[-1])
    await asyncio.sleep(sleep_time)
    print('OK {}'.format(url))

async def main(urls):
    for url in urls:
        await crawl_page(url)

%time asyncio.run(main(['url_1', 'url_2', 'url_3', 'url_4']))

########## 输出 ##########

crawling url_1
OK url_1
crawling url_2
OK url_2
crawling url_3
OK url_3
crawling url_4
OK url_4
Wall time: 10 s


```

async 修饰词声明异步函数，于是，这里的 crawl_page 和 main 都变成了异步函数。而调用异步函数，我们便可得到一个协程对象（coroutine object）。

再来说说协程的执行。执行协程有多种方法，这里我介绍一下常用的三种。

- 首先，我们可以通过 await 来调用。await 执行的效果，和 Python 正常执行是一样的，也就是说程序会阻塞在这里，进入被调用的协程函数，执行完毕返回后再继续，而这也是 await 的字面意思。代码中 await asyncio.sleep(sleep_time)  会在这里休息若干秒，await crawl_page(url)  则会执行 crawl_page() 函数。
- 其次，我们可以通过 asyncio.create_task() 来创建任务，这个我们下节课会详细讲一下，你先简单知道即可。
- 最后，我们需要 asyncio.run 来触发运行。asyncio.run 这个函数是 Python 3.7 之后才有的特性，可以让 Python 的协程接口变得非常简单，你不用去理会事件循环怎么定义和怎么使用的问题（我们会在下面讲）。一个非常好的编程规范是，asyncio.run(main()) 作为主程序的入口函数，在程序运行周期内，只调用一次 asyncio.run。

```python
import asyncio

async def crawl_page(url):
    print('crawling {}'.format(url))
    sleep_time = int(url.split('_')[-1])
    await asyncio.sleep(sleep_time)
    print('OK {}'.format(url))

async def main(urls):
    tasks = [asyncio.create_task(crawl_page(url)) for url in urls]
    for task in tasks:
        await task

%time asyncio.run(main(['url_1', 'url_2', 'url_3', 'url_4']))

########## 输出 ##########

crawling url_1
crawling url_2
crawling url_3
crawling url_4
OK url_1
OK url_2
OK url_3
OK url_4
Wall time: 3.99 s

    
import asyncio

async def crawl_page(url):
    print('crawling {}'.format(url))
    sleep_time = int(url.split('_')[-1])
    await asyncio.sleep(sleep_time)
    print('OK {}'.format(url))

async def main(urls):
    tasks = [asyncio.create_task(crawl_page(url)) for url in urls]
    await asyncio.gather(*tasks)

%time asyncio.run(main(['url_1', 'url_2', 'url_3', 'url_4']))

########## 输出 ##########

crawling url_1
crawling url_2
crawling url_3
crawling url_4
OK url_1
OK url_2
OK url_3
OK url_4
Wall time: 4.01 s

```

你可以看到，我们有了协程对象后，便可以通过 asyncio.create_task  来创建任务。任务创建后很快就会被调度执行，这样，我们的代码也不会阻塞在任务这里。所以，我们要等所有任务都结束才行，用 for task in tasks: await task  即可。

这里的代码也很好理解。唯一要注意的是，*tasks  解包列表，将列表变成了函数的参数；与之对应的是，          ** dict  将字典变成了函数的参数。

asyncio.create_task, asyncio.run  这些函数都是 Python 3.7 以上的版本才提供的，自然，相比于旧接口它们也更容易理解和阅读。

- 协程和多线程的区别，主要在于两点，一是协程为单线程；二是协程由用户决定，在哪些地方交出控制权，切换到下一个任务。
- 协程的写法更加简洁清晰，把 async / await 语法和 create_task 结合来用，对于中小级别的并发需求已经毫无压力。
- 写协程程序的时候，你的脑海中要有清晰的事件循环概念，知道程序在什么时候需要暂停、等待 I/O，什么时候需要一并执行到底。

### 21 | Python并发编程之Futures

首先你要辨别一个误区，在 Python 中，并发并不是指同一时刻有多个操作（thread、task）同时进行。相反，某个特定的时刻，它只允许有一个操作发生，只不过线程 / 任务之间会互相切换，直到完成。我们来看下面这张图：

![image-20190628225610515](/Users/xiaoqi/Library/Application Support/typora-user-images/image-20190628225610515.png)

图中出现了 thread 和 task 两种切换顺序的不同方式，分别对应 Python 中并发的两种形式——threading 和 asyncio。

事实上，Python 的解释器并不是线程安全的，为了解决由此带来的 race condition 等问题，Python 便引入了全局解释器锁，也就是同一时刻，只允许一个线程执行。当然，在执行 I/O 操作时，如果一个线程被 block 了，全局解释器锁便会被释放，从而让另一个线程能够继续执行。

至于所谓的并行，指的才是同一时刻、同时发生。Python 中的 multi-processing 便是这个意思，对于 multi-processing，你可以简单地这么理解：比如你的电脑是 6 核处理器，那么在运行程序时，就可以强制 Python 开 6 个进程，同时执行，以加快运行速度，

- 并发通常应用于 I/O 操作频繁的场景，比如你要从网站上下载多个文件，I/O 操作的时间可能会比 CPU 运行处理的时间长得多。
- 而并行则更多应用于 CPU heavy 的场景，比如 MapReduce 中的并行计算，为了加快运行速度，一般会用多台机器、多个处理器来完成。

```python
import concurrent.futures
import requests
import threading
import time

def download_one(url):
    resp = requests.get(url)
    print('Read {} from {}'.format(len(resp.content), url))

# 这里我们创建了一个线程池，总共有 5 个线程可以分配使用。executer.map() 与前面所讲的 Python 内置的 map() 函数类似，表示对 sites 中的每一个元素，并发地调用函数 download_one()。
def download_all(sites):
    with concurrent.futures.ThreadPoolExecutor(max_workers=5) as executor:
        executor.map(download_one, sites)
#def download_all(sites):
#    with concurrent.futures.ThreadPoolExecutor(max_workers=5) as executor:
#        to_do = []
#        for site in sites:
#            future = executor.submit(download_one, site)
#            to_do.append(future)
            
#        for future in concurrent.futures.as_completed(to_do):
#            future.result()
def main():
    sites = [
        'https://en.wikipedia.org/wiki/Portal:Arts',
        'https://en.wikipedia.org/wiki/Portal:History',
        'https://en.wikipedia.org/wiki/Portal:Society',
        'https://en.wikipedia.org/wiki/Portal:Biography',
        'https://en.wikipedia.org/wiki/Portal:Mathematics',
        'https://en.wikipedia.org/wiki/Portal:Technology',
        'https://en.wikipedia.org/wiki/Portal:Geography',
        'https://en.wikipedia.org/wiki/Portal:Science',
        'https://en.wikipedia.org/wiki/Computer_science',
        'https://en.wikipedia.org/wiki/Python_(programming_language)',
        'https://en.wikipedia.org/wiki/Java_(programming_language)',
        'https://en.wikipedia.org/wiki/PHP',
        'https://en.wikipedia.org/wiki/Node.js',
        'https://en.wikipedia.org/wiki/The_C_Programming_Language',
        'https://en.wikipedia.org/wiki/Go_(programming_language)'
    ]
    start_time = time.perf_counter()
    download_all(sites)
    end_time = time.perf_counter()
    print('Download {} sites in {} seconds'.format(len(sites), end_time - start_time))

if __name__ == '__main__':
    main()

## 输出

Download 15 sites in 0.19936635800002023 seconds

```

当然，我们也可以用并行的方式去提高程序运行效率。你只需要在 download_all() 函数中，做出下面的变化即可：

```python
with futures.ThreadPoolExecutor(workers) as executor
=>
with futures.ProcessPoolExecutor() as executor: 
# 在需要修改的这部分代码中，函数 ProcessPoolExecutor() 表示创建进程池，使用多个进程并行的执行程序。不过，这里我们通常省略参数 workers，因为系统会自动返回 CPU 的数量作为可以调用的进程数。
```

```tex
最后给你留一道思考题。你能否通过查阅相关文档，为今天所讲的这个下载网站内容的例子，加上合理的异常处理，让程序更加稳定健壮呢？欢迎在留言区写下你的思考和答案，也欢迎你把今天的内容分享给你的同事朋友，我们一起交流、一起进步。
1. request.get 会触发：ConnectionError, TimeOut, HTTPError等，所有显示抛出的异常都是继承requests.exceptions.RequestException 
2. executor.map(download_one, urls) 会触发concurrent.futures.TimeoutError
3. result() 会触发Timeout，CancelledError
4. as_completed() 会触发TimeOutError

CPU-bound的任务主要是multi-processing，IO-bound的话，如果IO比较快，用多线程，如果IO比较慢，用asyncio，因为效率更加高
```

### 22 | 并发编程之Asyncio

诚然，多线程有诸多优点且应用广泛，但也存在一定的局限性：

- 比如，多线程运行过程容易被打断，因此有可能出现 race condition 的情况；
- 再如，线程切换本身存在一定的损耗，线程数不能无限增加，因此，如果你的 I/O 操作非常 heavy，多线程很有可能满足不了高效率、高质量的需求。

事实上，Asyncio 和其他 Python 程序一样，是单线程的，它只有一个主线程，但是可以进行多个不同的任务（task），这里的任务，就是特殊的 future 对象。这些不同的任务，被一个叫做 event loop 的对象所控制。你可以把这里的任务，类比成多线程版本里的多个线程。

值得一提的是，对于 Asyncio 来说，它的任务在运行时不会被外部的一些因素打断，因此 Asyncio 内的操作不会出现 race condition 的情况，这样你就不需要担心线程安全的问题了。

```python
import asyncio
import aiohttp
import time

async def download_one(url):
    async with aiohttp.ClientSession() as session:
        async with session.get(url) as resp:
            print('Read {} from {}'.format(resp.content_length, url))

async def download_all(sites):
    tasks = [asyncio.create_task(download_one(site)) for site in sites]
    await asyncio.gather(*tasks)

def main():
    sites = [
        'https://en.wikipedia.org/wiki/Portal:Arts',
        'https://en.wikipedia.org/wiki/Portal:History',
        'https://en.wikipedia.org/wiki/Portal:Society',
        'https://en.wikipedia.org/wiki/Portal:Biography',
        'https://en.wikipedia.org/wiki/Portal:Mathematics',
        'https://en.wikipedia.org/wiki/Portal:Technology',
        'https://en.wikipedia.org/wiki/Portal:Geography',
        'https://en.wikipedia.org/wiki/Portal:Science',
        'https://en.wikipedia.org/wiki/Computer_science',
        'https://en.wikipedia.org/wiki/Python_(programming_language)',
        'https://en.wikipedia.org/wiki/Java_(programming_language)',
        'https://en.wikipedia.org/wiki/PHP',
        'https://en.wikipedia.org/wiki/Node.js',
        'https://en.wikipedia.org/wiki/The_C_Programming_Language',
        'https://en.wikipedia.org/wiki/Go_(programming_language)'
    ]
    start_time = time.perf_counter()
    asyncio.run(download_all(sites))
    end_time = time.perf_counter()
    print('Download {} sites in {} seconds'.format(len(sites), end_time - start_time))
    
if __name__ == '__main__':
    main()

## 输出
Download 15 sites in 0.062144195078872144 seconds

```

```python
# 主函数里的 asyncio.run(coro) 是 Asyncio 的 root call，表示拿到 event loop，运行输入的 coro，直到它结束，最后关闭这个 event loop。事实上，asyncio.run() 是 Python3.7+ 才引入的，相当于老版本的以下语句：
loop = asyncio.get_event_loop()
try:
    loop.run_until_complete(coro)
finally:
    loop.close()

```

不知不觉，我们已经把并发编程的两种方式都给学习完了。不过，遇到实际问题时，多线程和 Asyncio 到底如何选择呢？

- 如果是 I/O bound，并且 I/O 操作很慢，需要很多任务 / 线程协同实现，那么使用 Asyncio 更合适。
- 如果是 I/O bound，但是 I/O 操作很快，只需要有限数量的任务 / 线程，那么使用多线程就可以了。
- 如果是 CPU bound，则需要使用多进程来提高程序运行效率。

### 23 | 你真的懂Python GIL（全局解释器锁）吗？

Python 的线程，的的确确封装了底层的操作系统线程，在 Linux 系统里是 Pthread（全称为 POSIX Thread），而在 Windows 系统里是 Windows Thread。另外，Python 的线程，也完全受操作系统管理，比如协调何时执行、管理内存资源、管理中断等等。

GIL，是最流行的 Python 解释器 CPython 中的一个技术术语。它的意思是全局解释器锁，本质上是类似操作系统的 Mutex。每一个 Python 线程，在 CPython 解释器中执行时，都会先锁住自己的线程，阻止别的线程执行。

所以说，CPython  引进 GIL 其实主要就是这么两个原因：

- 一是设计者为了规避类似于内存管理这样的复杂的竞争风险问题（race condition）；
- 二是因为 CPython 大量使用 C 语言库，但大部分 C 语言库都不是原生线程安全的（线程安全会降低性能和增加复杂度）。



下面这张图，就是一个 GIL 在 Python 程序的工作示例。其中，Thread 1、2、3 轮流执行，每一个线程在开始执行时，都会锁住 GIL，以阻止别的线程执行；同样的，每一个线程执行完一段后，会释放 GIL，以允许别的线程开始利用资源。

![image-20190701220559019](/Users/xiaoqi/Library/Application Support/typora-user-images/image-20190701220559019.png)

GIL 的设计，主要是为了方便 CPython 解释器层面的编写者，而不是 Python 应用层面的程序员。作为 Python 的使用者，我们还是需要 lock 等工具，来确保线程安全。比如我下面的这个例子：

```python
n = 0
lock = threading.Lock()

def foo():
    global n
    with lock:
        n += 1
```

### 24 | 带你解析 Python 垃圾回收机制

Python 程序在运行的时候，需要在内存中开辟出一块空间，用于存放运行时产生的临时变量；计算完成后，再将结果输出到永久性存储器中。如果数据量过大，内存空间管理不善就很容易出现 OOM（out of memory），俗称爆内存，程序可能被操作系统中止。

```python
def func():
    show_memory_info('initial')
    a = [i for i in range(10000000)]
    show_memory_info('after a created')

func()
show_memory_info('finished')

########## 输出 ##########

initial memory used: 47.19140625 MB
after a created memory used: 433.91015625 MB
finished memory used: 48.109375 MB

def func():
    show_memory_info('initial')
    global a
    a = [i for i in range(10000000)]
    show_memory_info('after a created')

func()
show_memory_info('finished')

########## 输出 ##########

initial memory used: 48.88671875 MB
after a created memory used: 433.94921875 MB
finished memory used: 433.94921875 MB

```

这是因为，函数内部声明的列表 a 是局部变量，在函数返回后，局部变量的引用会注销掉；此时，列表 a 所指代对象的引用数为 0，Python 便会执行垃圾回收，因此之前占用的大量内存就又回来了。

新的这段代码中，global a 表示将 a 声明为全局变量。那么，即使函数返回后，列表的引用依然存在，于是对象就不会被垃圾回收掉，依然占用大量内存。

不过，我想还是会有人问，如果我偏偏想手动释放内存，应该怎么做呢？你只需要先调用 del a 来删除一个对象；然后强制调用 gc.collect()，即可手动启动垃圾回收。

```python
import gc

show_memory_info('initial')

a = [i for i in range(10000000)]

show_memory_info('after a created')

del a
gc.collect()

show_memory_info('finish')
print(a)

########## 输出 ##########

initial memory used: 48.1015625 MB
after a created memory used: 434.3828125 MB
finish memory used: 48.33203125 MB

---------------------------------------------------------------------------
NameError                                 Traceback (most recent call last)
<ipython-input-12-153e15063d8a> in <module>
     11 
     12 show_memory_info('finish')
---> 13 print(a)

NameError: name 'a' is not defined

```

Python 使用**标记清除**（mark-sweep）算法和**分代收集**（generational），来启用针对循环引用的自动垃圾回收。你可能不太熟悉这两个词，这里我简单介绍一下。

- 先来看标记清除算法。我们先用图论来理解不可达的概念。对于一个有向图，如果从一个节点出发进行遍历，并标记其经过的所有节点；那么，在遍历结束后，所有没有被标记的节点，我们就称之为不可达节点。显而易见，这些节点的存在是没有任何意义的，自然的，我们就需要对它们进行垃圾回收。
- Python 将所有对象分为三代。刚刚创立的对象是第 0 代；经过一次垃圾回收后，依然存在的对象，便会依次从上一代挪到下一代。而每一代启动自动垃圾回收的阈值，则是可以单独指定的。当垃圾回收器中新增对象减去删除对象达到相应的阈值时，就会对这一代对象启动垃圾回收。事实上，分代收集基于的思想是，新生的对象更有可能被垃圾回收，而存活更久的对象也有更高的概率继续存活。因此，通过这种做法，可以节约不少计算量，从而提高 Python 的性能。

objgraph，一个非常好用的可视化引用关系的包。在这个包中，我主要推荐两个函数，第一个是 show_refs()，它可以生成清晰的引用关系图。

```python
import objgraph

a = [1, 2, 3]
b = [4, 5, 6]

a.append(b)
b.append(a)

objgraph.show_refs([a])

import objgraph

a = [1, 2, 3]
b = [4, 5, 6]

a.append(b)
b.append(a)

objgraph.show_backrefs([a])

```

