---
layout: post
title: Decorator
category: Python
---

## Decorators in Python

### `@property`

How to use `@property`:

{% highlight Python linos %}
class Celsius:
    def __init__(self, temperature = 0):
        self._temperature = temperature

    def to_fahrenheit(self):
        return (self.temperature * 1.8) + 32

    @property
    def temperature(self):
        print("Getting value")
        return self._temperature

    @temperature.setter
    def temperature(self, value):
        if value < -273:
            raise ValueError("Temperature below -273 is not possible")
        print("Setting value")
        self._temperature = value
{% endhighlight %}


Source code:
{% highlight Python linos %}
class Property(object):
    "Emulate PyProperty_Type() in Objects/descrobject.c"

    def __init__(self, fget=None, fset=None, fdel=None, doc=None):
        self.fget = fget
        self.fset = fset
        self.fdel = fdel
        if doc is None and fget is not None:
            doc = fget.__doc__
        self.__doc__ = doc

    def __get__(self, obj, objtype=None):
        if obj is None:
            return self
        if self.fget is None:
            raise AttributeError("unreadable attribute")
        return self.fget(obj)

    def __set__(self, obj, value):
        if self.fset is None:
            raise AttributeError("can't set attribute")
        self.fset(obj, value)

    def __delete__(self, obj):
        if self.fdel is None:
            raise AttributeError("can't delete attribute")
        self.fdel(obj)

    def getter(self, fget):
        return type(self)(fget, self.fset, self.fdel, self.__doc__)

    def setter(self, fset):
        return type(self)(self.fget, fset, self.fdel, self.__doc__)

    def deleter(self, fdel):
        return type(self)(self.fget, self.fset, fdel, self.__doc__)
{% endhighlight %}

### functools.wraps()

{% highlight Python linos %}
def logged(func):
    def with_logging(*args, **kwargs):
        print func.__name__ + " was called"
        return func(*args, **kwargs)
    return with_logging

@logged
def f(x):
    """does some math"""
    return x + x * x
# same as f = logged(f)
print(f.__name__) # 'with_logging'
{% endhighlight %}

Now we use `wraps()` to keep the `func`'s `__name__` and `__doc__`:

{% highlight Python linos %}
from functools import wraps
def logged(func):
    @wraps(func)
    def with_logging(*args, **kwargs):
        print func.__name__ + " was called"
        return func(*args, **kwargs)
    return with_logging

@logged
def f(x):
    """does some math"""
    return x + x * x

print(f.__name__)  # prints 'f'
print(f.__doc__)  # prints 'does some math'
{% endhighlight %}

### Appendix1: `__set__` and `__get__` of Descriptor
{% highlight Python linos %}
class Celsius(object):
    def __init__(self, value=0.0):
        self.value = float(value)
    def __get__(self, instance, owner):
        return self.value
    def __set__(self, instance, value):
        self.value = float(value)
c = Celsius()
c = 3 # calls c.__set__
print(c) #calls c.__get__
{% endhighlight %}

### Appendix2: functools.partial()
A typical use of partial() is `basetwo = partial(int, base=2)`
Source code:
{% highlight Python linos %}
def partial(func, *args, **keywords):
    def newfunc(*fargs, **fkeywords):
        newkeywords = keywords.copy()
        newkeywords.update(fkeywords)
        return func(*(args + fargs), **newkeywords)
    newfunc.func = func
    newfunc.args = args
    newfunc.keywords = keywords
    return newfunc
{% endhighlight %}


How to use it:
{% highlight Python linos %}
from functools import partial
basetwo = partial(int, base=2)
basetwo.__doc__ = 'Convert base 2 string to an int.'
basetwo('10010') # 18
{% endhighlight %}



