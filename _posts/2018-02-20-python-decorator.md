---
layout: post
title: Decorator & Pipeline
category: python
---
### Story behind
Mr. Xcc has been being fascinated by *Functional Programming*, he 
1. wrapped his functions perfectly, 
2. constructed them into *functor*  to 
3. pipeline the functions, 

{% highlight C linenos %}
Slices ms = al | rotate_atoms(get_quat(n, theta)) | make_protein_volume(em.apix, 3, size) | 
hydrate_volume() | make_slice_by_ah() | band_limit_slices(0.667);
/* take in al as the functor rotate_atoms()'s input, 
return data to functor make_protein_volume() ...*/
{% endhighlight %}

in which way he could call nested functions(or functors) quite elegantly :smirk:.

I got a little bit jealous of the elegance:confused:. Attempting to catch the essence of pipeline, I reviewed decorator in python.
### Decorator: take func, augment func
{% highlight Python linenos %}
#direct run func
def greet(name):
    return "hello " + name
greet_someone = greet 
print(greet("xcc"))

# define func inside other func
def greet(name):
    def get_message():
        return "hello "
    return get_message() + name
print(greet("xcc"))


# pass func to other func
def greet(name):
    return "hello " + name
def call_func(func, other_name="xcc"):
    return func(other_name)
print(call_func(greet))

# func return func
def compose_greet_func(name0):
    def get_message(name1):
        return name0 + " greet to " + name1
    return get_message
print(compose_greet_func("xcc")("xll"))

# decorator: take in a func, do some augmenting and return a greater func
def get_text(name):
    return "hello {0}".format(name)

def p_decorator(func):
    def func_wrapper(name):
        return "inside wrapper:{0}".format(func(name))
    return func_wrapper

my_get_text = p_decorator(get_text)
print(my_get_text("xcc"))

# syntactic sugar
def p_decorator(func):
    def func_wrapper(name):
        return "inside wrapper:{0}".format(func(name))
    return func_wrapper

@p_decorator
def get_text(name):
    return "hello {0}".format(name)
print(get_text("xcc"))
{% endhighlight %}

### Pipeline decorator


{% highlight Python linenos %}
# take in original function, return a class, which
# 0.init with args&kwargs, 
# 1.its __rrshift__() take parameter from >>, 
# then return mark(parameter, args, kwargs)) 
def pipe(original):
    class PipeInto(object):
        data = {'function': original}

        def __init__(self, *args, **kwargs):
            # *args is to unpack
            self.data['args'] = args
            self.data['kwargs'] = kwargs
            # now args and kwargs are tuple and dict
            print("args:", args)
        def __rrshift__(self, parameter):
            return self.data['function'](
                        parameter, 
                        *self.data['args'], 
                        **self.data['kwargs'])
    return PipeInto

@pipe
def mark(parameter, score):
    if parameter == "xcc":
        return parameter + " love xll: " + str(score)
    if parameter == "xll":
        return parameter + " love xcc: " + str(score)

print("xcc">>mark(10) )
# "xcc" here is the parameter, where as 10 belongs to args.
# In this way, parameter and args are taken separately 
# from the left side of >> and the ()
{% endhighlight %}



