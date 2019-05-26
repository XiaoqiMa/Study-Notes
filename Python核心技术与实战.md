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
- 

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

