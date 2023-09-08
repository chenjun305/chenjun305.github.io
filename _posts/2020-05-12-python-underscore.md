---
layout: post
title: python underscore
date: 2020-05-12
description: 
tags: python
categories: programming
---
## Single underscore(单下划线)

* 作为变量名，表示后续代码不使用，（golang语言也有类似的用法）

```
 _ = "abc"
 print(_)

 for _ in range(10):
     print("in for loop")
```

* Interactive 环境下表示最后一个表达式的值

```
>>> 1+2 
3 
>>> print(_)
3
```

* 用于分隔数字，方便阅读

```
>>> num = 900_000_000
>>> print(num)
900000000
```

* django中用于国际化翻译字符串的方法

```
from django.http import HttpResponse
from django.utils.translation import gettext as _ 

def my_view(request):
    output = _("something to translate")
    return HttpResponse(output)
```

### Leading underscore（下划线前缀）
私有，内部变量或方法（convention)

```
_name = "inernal name"

def external_func():
    return "public"

def _internal_func():
    return "private"
```


### Trailing underscore
避免与python关键字冲突

```
class_ = "MyClass"
```

### Leading Double underscore
name mangling, 避免与子类同名方法冲突。
python解释器会更改该方法名字，前面加上`_Classname`。

```
class Person():
    
    def external_func(self):
        return "public"

    def __internal_func(self):
        return "leading double underscore"

class Subclass(Person):
    def __internal_func(self):
        return "in subclass"

print(dir(Person))
```

上面的代码执行结果如下:

```
python test.py
['_Person__internal_func', '_Subclass__internal_func', '__class__', '__delattr__', '__dict__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__', '__gt__', '__hash__', '__init__', '__init_subclass__', '__le__', '__lt__', '__module__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__setattr__', '__sizeof__', '__str__', '__subclasshook__', '__weakref__', 'external_func']
```

可以看到`__internal_func`方法变成了两个方法，分别加上了`_Person`和`_Subclass`前缀。

### Double underscore(Dunder/magic method)

前后都有双下划线。

* `_init_.py`文件，当被import时，`__init__.py` 将会执行, 方便import。
* 类构造方法`__init__` ,
* 用于context管理的`__enter__`进入context，`__exit__`退出context时调用（一般用于资源清理）。

```
class Open_File():

    def __init__(self, destination):
        pass

    def __enter__(self):
        pass

    def __exit__(self, exc_type, exc_val, traceback):
        pass
```

* `__name__`变量

```
def main():
    print("execute script")

if __name__ == "__main__":
    main()
```

* python的运算符重载，也是通过这些Dunder method实现的

1. 算术运算符：
- 加法运算符：`+`，对应的魔术方法是`__add__(self, other)`
- 减法运算符：`-`，对应的魔术方法是`__sub__(self, other)`
- 乘法运算符：`*`，对应的魔术方法是`__mul__(self, other)`
- 除法运算符：`/`，对应的魔术方法是`__truediv__(self, other)`
- 取模运算符：`%`，对应的魔术方法是`__mod__(self, other)`
- 等等...

2. 比较运算符：
- 相等运算符：`==`，对应的魔术方法是`__eq__(self, other)`
- 不等运算符：`!=`，对应的魔术方法是`__ne__(self, other)`
- 大于运算符：`>`，对应的魔术方法是`__gt__(self, other)`
- 小于运算符：`<`，对应的魔术方法是`__lt__(self, other)`
- 大于等于运算符：`>=`，对应的魔术方法是`__ge__(self, other)`
- 小于等于运算符：`<=`，对应的魔术方法是`__le__(self, other)`
- 等等...

3. 赋值运算符：
- 赋值运算符：`=`
- 加法赋值运算符：`+=`，对应的魔术方法是`__iadd__(self, other)`
- 减法赋值运算符：`-=`，对应的魔术方法是`__isub__(self, other)`
- 乘法赋值运算符：`*=`，对应的魔术方法是`__imul__(self, other)`
- 除法赋值运算符：`/=`，对应的魔术方法是`__idiv__(self, other)`
- 等等...

4. 移位运算符：
- 左移运算符可：`<<`, 对应的魔术方法是`__lshift__(self, other)`
- 右移运算符可：`>>`, 对应的魔术方法是`__rshift__(self, other)`
- 等等...
