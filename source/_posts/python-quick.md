---
title: python 语法整理
date: 2018-06-19 10:48:21
categories: python
tags:
    - python
    - 语法整理 
---


## 1. Basic Syntax

comments

```python

#  line comment


"""
comments1
comments1
comments1
"""

```



```python
myvar = 3
myvar += 2
myvar -=1

mystr = "hello"
mystr += "world"
print(mystr)

mystr1, mystr2 = myvar, mystring

```

## 2. Data types

data types

```python
sample = [1, ["another", "list"], {"a","b"}]
mylist = ["list item 1", 2, 34]

mylist[0] = "new list item"  #change item
mylist[-1] = 311  #change last item

mydict = {"key1":"item1", 2:3, "pi":3.14}
mydic["pi"] = 3.15  #change dictrionary value 

mytuple = (1,2,43)
myfunc = len
print(myfunc(mytuple))

```

array range

```python
mylist = ["list item 1", 2, 34]
mylist[:]   # ["list item 1", 2, 34]
mylist[0:2]  # ["list item 1", 2]
mylist[-3:-1]  # ["list item 1", 2]

mylist[::2] # ["list item 1", 34]
```

## 3. String

```python

mystr = """sdfsdfsdf
sdfsdf
"""

print("This %(verb)s a %(noun)s." % {"noun": "test", "verb": "is"})


name = "Cindy"
"Hello, {}!".format(name)
print(f"Hello, {name}!")
# hello Cindy

```

## 4. Flow control statements

```python
>>> print(range(10))
range(0, 10)
>>> rangelist = list(range(10))
>>> print(rangelist)
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]


for number in range(10):
    # Check if number is one of
    # the numbers in the tuple.
    if number in (3, 4, 7, 9):
        # "Break" terminates a for without
        # executing the "else" clause.
        break
    else:
        # "Continue" starts the next iteration
        # of the loop. It's rather useless here,
        # as it's the last statement of the loop.
        continue
else:
    # The "else" clause is optional and is
    # executed only if the loop didn't "break".
    pass # Do nothing

if rangelist[1] == 2:
    print("The second item (lists are 0-based) is 2")
elif rangelist[1] == 3:
    print("The second item (lists are 0-based) is 3")
else:
    print("Dunno")

while rangelist[1] == 1:
    print("We are trapped in an infinite loop!")

```

## 5. Functions

```python

# Same as def funcvar(x): return x + 1
funcvar = lambda x: x + 1
>>> print(funcvar(1))
2

# an_int and a_string are optional, they have default values
# if one is not passed (2 and "A default string", respectively).
def passing_example(a_list, an_int=2, a_string="A default string"):
    a_list.append("A new item")
    an_int = 4
    return a_list, an_int, a_string

>>> my_list = [1, 2, 3]
>>> my_int = 10
>>> print(passing_example(my_list, my_int))
([1, 2, 3, 'A new item'], 4, "A default string")
>>> my_list
[1, 2, 3, 'A new item']
>>> my_int
10
```


## 6. Classes

```python

class MyClass(object):
    common = 10
    def __init__(self):
        self.myvariable = 3
    def myfunction(self, arg1, arg2):
        return self.myvariable

    # This is the class instantiation

>>> classinstance = MyClass()
>>> classinstance.myfunction(1, 2)
3
# This variable is shared by all instances.
>>> classinstance2 = MyClass()
>>> classinstance.common
10
>>> classinstance2.common
10
# Note how we use the class name
# instead of the instance.
>>> MyClass.common = 30
>>> classinstance.common
30
>>> classinstance2.common
30
# This will not update the variable on the class,
# instead it will bind a new object to the old
# variable name.
>>> classinstance.common = 10
>>> classinstance.common
10
>>> classinstance2.common
30
>>> MyClass.common = 50
# This has not changed, because "common" is
# now an instance variable.
>>> classinstance.common
10
>>> classinstance2.common
50

# This class inherits from MyClass. The example
# class above inherits from "object", which makes
# it what's called a "new-style class".
# Multiple inheritance is declared as:
# class OtherClass(MyClass1, MyClass2, MyClassN)
class OtherClass(MyClass):
    # The "self" argument is passed automatically
    # and refers to the class instance, so you can set
    # instance variables as above, but from inside the class.
    def __init__(self, arg1):
        self.myvariable = 3
        print(arg1)

>>> classinstance = OtherClass("hello")
hello
>>> classinstance.myfunction(1, 2)
3
# This class doesn't have a .test member, but
# we can add one to the instance anyway. Note
# that this will only be a member of classinstance.
>>> classinstance.test = 10
>>> classinstance.test
10

```


## 7. Exceptions

```python
def some_function():
    try:
        # Division by zero raises an exception
        10 / 0
    except ZeroDivisionError:
        print("Oops, invalid.")
    else:
        # Exception didn't occur, we're good.
        pass
    finally:
        # This is executed after the code block is run
        # and all exceptions have been handled, even
        # if a new exception is raised while handling.
        print("We're done with that.")

>>> some_function()
Oops, invalid.
We're done with that.

```

## 8. Important

```python

import random
from time import clock

randomint = random.randint(1, 100)
>>> print(randomint)
64

```

## 9. File I/O

```python
import pickle
mylist = ["This", "is", 4, 13327]
# Open the file C:\\binary.dat for writing. The letter r before the
# filename string is used to prevent backslash escaping.
myfile = open(r"C:\\binary.dat", "wb")
pickle.dump(mylist, myfile)
myfile.close()

myfile = open(r"C:\\text.txt", "w")
myfile.write("This is a sample string")
myfile.close()

myfile = open(r"C:\\text.txt")
>>> print(myfile.read())
'This is a sample string'
myfile.close()

# Open the file for reading.
myfile = open(r"C:\\binary.dat", "rb")
loadedlist = pickle.load(myfile)
myfile.close()
>>> print(loadedlist)
['This', 'is', 4, 13327]

```


