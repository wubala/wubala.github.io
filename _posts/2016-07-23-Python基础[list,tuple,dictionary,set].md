---
layout: post
title: Python基础[list,tuple,dictionary,set]
category: Python
tags: [Python]
---

# Python基础[list,tuple,dictionary,set]
> 作者：iTech
出处：http://itech.cnblogs.com/ 
经整理修改与完善，仅供初学者学习。

## list

### list 基础

```linux
>>> a = ['money', 'money', 'money', 100000000]
>>> a
['money', 'money', 'money', 100000000]
>>> a[3]
100000000
>>> a[-1] = a[-1] * 2
>>> a[-1]
200000000
>>> ['i', 'want'] + a
['i', 'want', 'money', 'money', 'money', 200000000]
>>> a
['money', 'money', 'money', 200000000]
>>> a[:0] = ['i', 'want']
>>> a
['i', 'want', 'money', 'money', 'money', 200000000]
>>> a[2:4] = []
>>> a
['i', 'want', 'money', 200000000]
>>> len(a)
4
>>> a[:]= []
>>> a
[]
>>> 
```

从上面的实例中可以可以看到：
list类型的定义a = ['money', 'money', 'money', 100000000]，也可以从set定义list入s = {1,2,3} 和l = list(s)。

* 元素的访问
* 元素的修改
* list的链接
* 元素的插入
* 元素的删除
* 求list的长度
* list的清空。

### list类成员函数

```linux
>>> a = [10, 20, 100, 3, 60]
>>> print a
[10, 20, 100, 3, 60]
>>> a.append(79)
>>> print a
[10, 20, 100, 3, 60, 79]
>>> b = [44, 88]
>>> a.extend(b)
>>> print a
[10, 20, 100, 3, 60, 79, 44, 88]
>>> a.insert(0, 500)
>>> print a
[500, 10, 20, 100, 3, 60, 79, 44, 88]
>>> a.remove(79)
>>>print  a
[500, 10, 20, 100, 3, 60, 44, 88]
>>> a.index(3)
4
>>> a.pop()
88
>>> a.count(10)
1
>>> a.sort()
>>> print a
[3, 10, 20, 44, 60, 100, 500]
>>> a.reverse()
>>>print a
[500, 100, 60, 44, 20, 10, 3]
>>> a[2,5] #切片
[60, 44, 20, 10]
```

注意：

* append用来在结尾增加一个元素。
* extend用来在结尾增加一个list,也可以使用“+”来实现。
* insert在指定的index处插入一个元素。
* remove删除指定值的元素。
* index查找给定值的index。
* pop删除且返回list的最后一个值。
* count获得给定值在list重复的个数。
* sort排序。
* reverse反序。

### 作为栈

```linux
>>> mystack = [3,5,1]
>>> mystack
[3, 5, 1]
>>> mystack.append(3)
>>> mystack
[3, 5, 1, 3]
>>> mystack.append(2)
>>> mystack
[3, 5, 1, 3, 2]
>>> mystack.pop()
2
>>> mystack.pop()
3
>>> mystack
[3, 5, 1]
>>> 
```

### list作为队列

```linux
>>> myqueue = ['tom', 'tom2']
>>> myqueue
['tom', 'tom2']
>>> myqueue.append('tom3')
>>> myqueue
['tom', 'tom2', 'tom3']
>>> myqueue.pop(0)
'tom'
>>> myqueue
['tom2', 'tom3']
>>> 
```

### list高级

```linux
#list to another list
numbers = [1,2,3,4,5]
squares = [number*number for number in numbers]
print(squares)
[1,4,9,16,25]

numbers_under_4 = [number for number in numbers if number < 4]
print(numbers_under_4)
[1,2,3]

squares_under_4 = [number*number for number in numbers if number < 4]
print(squares_under_4)
[1,4,9]

#list and for, not foreach
strings = ['a', 'b', 'c', 'd', 'e']
for index, string in enumerate(strings):
    print (index, string)
'0 a 1 b 2 c 3 d 4 e'

#list any and all
numbers2 = [1,2,3,4,5,6,7,8,9]
if all(number < 10 for number in numbers2):
    print ('Success!')
'Success!'
numbers3 = [1,10,100,1000,10000]
if any(number < 10 for number in numbers3):
    print ('Success!')
'Success!'

#mutiply list
mat = [[1,2,3],[4,5,6]]
for i in [0,1,2]:
    for row in mat:
        print(row[i],end=" ")
    print()
1 4 
2 5 
3 6
print(list(zip(*mat)))
[(1, 4), (2, 5), (3, 6)]

#list union by set
numbers4 = [1,2,3,3,4,1]
print(set(numbers4))
#{1,2,3,4}
if len(numbers4) == len(set(numbers4)):
    print ('List is unique!')
```

注意：

* list 到list的映射。
* any和all在list中的使用。 
* list与for的结合，相当于高级语言中的foreach功能。
* 对list的enumerate与for的结合，相当于高级语言中的for功能。
* list的嵌套使用，相当于多维数组。
* del相当于list本身的remove，都可以用来删除list中的元素。

## tuple

### tuple常用操作

```linux
>>> t = 12, 33, 50
>>> t[2]
50
>>> t
(12, 33, 50)
>>> u = (t, 60)
>>> u
((12, 33, 50), 60)
>>> len(u)
2
>>> x,y,z = t
>>> x
12
>>> y
33
>>> z
50
>>> [3*i for i in t]
[36, 99, 150]
>>> t.count(50)
1
>>> t.index(50)
2
```

### tuple向list的转化

```linux
>>> a = (1,2,3,4)
>>> b = [x for x in a]
>>> print b
[1, 2, 3, 4]
```

试想一下，如果是这样呢：

```linux
>>> a = (1,2,3,4)
>>> b = (x for x in a)
>>> print b
<generator object <genexpr> at 0x00000000029976C0>
>>> type(b)
<type 'generator'> #这里b变为构造器类型
```

注意：

* tuple相当于是**不可修改**的list，所以同时也不具有list的很多修改的方法，例如append，insert，remove等；但是tuples仍然可以使用count，index等查找方法。
* 使用 t2 = (2,4,8)来定义tuple，也可以使用list来定义tuple如 l = [1,2,3] 和 t = tuple(l)。


##  set

```linux
>>> basket = {'apple', 'orange', 'apple', 'pear', 'orange', 'banana'}
>>> print(basket)
{'orange', 'pear', 'banana', 'apple'}
>>> a = [1,2,30,45,69]
>>> b = set(a)
>>> b
{1, 2, 45, 30, 69}
>>> 'pear' in basket
True
>>> c = {x for x in 'abracadabra' if x not in 'abc'}
>>> c
{'r', 'd'}
>>> a = {30, 20,1}
>>> a
{1, 20, 30}
>>> a - b
{20}
>>> a | b
{1, 2, 69, 45, 20, 30}
>>> a & b
{1, 30}
>>> a ^ b
{2, 69, 45, 20}
>>> 
```

注意：

* set的定义使用{}。
* 可以将list使用set显示的转化为set。
* 可以用in来判断指定元素是否存在于set中。
* set于for结合使用，相当于foreach。
* set可以进行 / , & , ^ , -操作。

##  dictionary

```linux
>>> d = {1 : 'tom', 2 : 'bill'}
>>> d
{1: 'tom', 2: 'bill'}
>>> d[3] = 'god'
>>> d
{1: 'tom', 2: 'bill', 3: 'god'}
>>> d[2]
'bill'
>>> del d[2]
>>> d
{1: 'tom', 3: 'god'}
>>> list(d.keys())
[1, 3]
>>> list(d.values())
['tom', 'bill', 'god']
>>> 1 in d
True
>>> dict([('sape', 4139), ('guido', 4127), ('jack', 4098)])
{'sape': 4139, 'jack': 4098, 'guido': 4127}
>>> {x: x**2 for x in (2, 4, 6)}
{2: 4, 4: 16, 6: 36}
>>> dict(sape=4139, guido=4127, jack=4098)
{'sape': 4139, 'jack': 4098, 'guido': 4127}
>>> 
```
注意：

* dictionary使用{1 : 'tom', 2 : 'bill'}来定义，也可以使用dict([('sape', 4139), ('guido', 4127),(['jack', 4098)])来从list定义dictionary。
* []=来增加新的值。
* del来删除指定的key和value。
* in用来查找key是否存在。
* keys()用来查看所有的keys。
* valuse()用来查看所有的values。

```python
#dictionary
keyGenDict={1:'blue',4:'fast',2:'test'}
print (keyGenDict.keys())
print (keyGenDict.values())
print (keyGenDict.items())

keys=list(keyGenDict.keys())
keys.sort()
for key in keys:
    print(keyGenDict[key])
    
for key, value in keyGenDict.items():
    print(key,value)
```

注意：

* dictionary的keys，values和items的使用。 


##  other

```linux
>>> a = [1,7,9,2]
>>> for x in a:
    print(x, end = " ")

1 7 9 2 
>>> s = {3,6,9,1}
>>> for i in s:
    print(i, end = " ")

1 3 6 9 
>>> d = {'aa':1, 'bb':2, 'cc':3}
>>> for x in d:
    print(x,end = ' ')

aa cc bb 
>>> for k,v in d.items():
    print(k,v)

aa 1
cc 3
bb 2
>>> for x in enumerate(a):
    print(x,end = ' ')

(0, 1) (1, 7) (2, 9) (3, 2) 
>>> questions = ['name', 'quest', 'favorite color']

>>> answers = ['lancelot', 'the holy grail', 'blue']

>>> for q, a in zip(questions, answers):
     print('What is your {0}?  It is {1}.'.format(q, a))

What is your name?  It is lancelot.
What is your quest?  It is the holy grail.
What is your favorite color?  It is blue.
>>> for f in sorted(s):
    print(f,end=' ')

1 3 6 9 

>>> 
```

注意：

* dictionary的items（）可以在遍历时同时获得key和value。
* enumerate可以在遍历时增加index。
* zip可以将多个list绑定到一个遍历中。
* sorted可以在遍历时排序。
 






　
