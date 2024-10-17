---
layout: post
title: Writing Pythonic Code
cover-img: ["assets/img/posts/code-wide.png"]
tags: [💻code, python]
---

<!-- # Writing Pythonic Code -->

We are covering concepts like list/set/dict comprehensions, yields and generators, lambdas and decorators.
Usually they can improve readability, because:
* they are usually consice
* they make they intention of the developer more clear (it's a more specific tool, hence you know what the usecase is)

## Comprehensions and Generators

#### list comprehension

```py
# usually we write this
powers_of_two = []
for exp in range(25):
    if exp != 2:
        powers_of_two += [2 ** exp]

print(powers_of_two)


# the pythonic way: list comprehension
powers_of_two = [2 ** exp for exp in range(25) if exp != 2]

print(powers_of_two)
```

* comprehensions are syntactic sugar to make oneliners of classic procedure
* notice that they have the exact same part but reorganised (for loop, if statetment, assignment)


```py
# set comprehension
powers_of_two = {2 ** exp for exp in range(25) if exp != 2}
print(powers_of_two)

# dict comprehension
powers_of_two = {f"2**{exp}": 2 ** exp
                 for exp in range(25) if exp != 2}
print(powers_of_two)
```

#### generators

Similarly, we can create generators (essentially iterators, used in for loops)



```py
def powers_of_two_gen():
    for exp in range(25):
        if exp != 2:
            yield 2 ** exp

powers_of_two = powers_of_two_gen()
print(powers_of_two)
# <generator object powers_of_two_gen at 0x7f77465c3150>

print([i for i in powers_of_two])
# [1, 2, 8, 16, ...]
```

Calling next with the generator iterator (automatically done by the for loop) executes the generator function
* Execute up to the next `yield` statement
    * For the first call, start at the beginning of the function
    * Throw `StopIteration` if the function exits (`return` or the end of the function) before encountering a `yield`
* Store the function state (local variables and last executed `yield`)
* Return the value specified by the yield statement

```py
# another yielding function
def boo():
    print("AAA")
    yield 1
    print("BBB")
    yield 2
    print("CCC")
    yield 3
    print("DDD")

next(boo)
next(boo)
next(boo)
next(boo) # throws error
```


* they are good for sequential processing and memory efficiency

```py
import sys

powers_of_two_list = [2 ** exp for exp in range(25) if exp != 2]
sys.getsizeof(powers_of_two_list)

powers_of_two_gen = (2 ** exp for exp in range(25) if exp != 2)
sys.getsizeof(powers_of_two_gen)

powers_of_two_list = [2 ** exp for exp in range(50) if exp != 2]
sys.getsizeof(powers_of_two_list)

powers_of_two_gen = (2 ** exp for exp in range(50) if exp != 2)
sys.getsizeof(powers_of_two_gen)
```

* `sys.getsizeof()` only gives size of pointers inside the list. This function is NOT recursive.

Explain the different sizes for powers_of_two_list and powers_of_two_gen.

* `powers_of_two_list` stores all list item in memory
* `powers_of_two_gen` stores the code to generate the items as they are requested

How would the output change if we use range(50) instead of range(25)?
* `powers_of_two_list` will double in size
* `powers_of_two_gen` remains the same size

What are the performance implications?
* The comprehension incurs some delay when it is executed to create all the list items and populate the list, whereas the generator does not create any items until they are requested.
* The generator has a smaller memory footprint, which can result in fewer cache misses, which can improve performance.



#### builtins to use with generators

tons of builtins that can be used nicely with generators:
`sum()`,`all()`,`any()`,`zip()`, `enumerate()`

```py
[(n for n[0] not in "aeiou" else n.upper()) for n  in names]


# modify inplace
[(n for n[0] not in "aeiou" else n.upper()) for n, i in enumerate(names)]
[(n for n[0] not in "aeiou" else )]

```

Even more:
`itertools.cycle()` - makes infinate generator

## Higher Order Functions

#### Lambdas

* Lambdas are Anonymous (unnamed) functions
* syntax: `lambda [parameter_list]: [expression]`
* Only one expression (no statements). The result of evaluating the expression is returned.
* Often used to pass a function as the argument for sorting, filtering, and mapping functions.

```py
# intrepreter
>>> m = lambda x, y: x*y
>>> type(m)
<class 'function'>
```
```py
>>> nums = [3, 1, 5, 2, 7]
>>> result = map(lambda x: x * x, nums)
>>> list(result)
[9, 1, 25, 4, 49]

>>> nums = [3, 1, 5, 2, 7]
>>> result = sorted(nums, key=lambda x: -x)
>>> result
[7, 5, 3, 2, 1]
```


> For python specifically everything is an object. As such, a function is an object which is callable (aka supports the `()` operation)

Like any other object, they
* can be passed as an argument to a function
* can be returned from a function
* have  "special"/"magic" functions (e.g. `__repr__`)

```py
>>> def fn():
...     pass

>>> type(fn)
<class 'function'>
>>> dir(fn)
['__annotations__', '__call__', '__class__', '__closure__', '__code__', '__defaults__', '__delattr__', '__dict__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__get__', '__getattribute__', '__globals__', '__gt__', '__hash__', '__init__', '__init_subclass__', '__kwdefaults__', '__le__', '__lt__', '__module__', '__name__', '__ne__', '__new__', '__qualname__', '__reduce__', '__reduce_ex__', '__repr__', '__setattr__', '__sizeof__', '__str__', '__subclasshook__']
```

Functions can be passed as arguments to other functions:
```py
def apply_func(f, f_arg):
    f(f_arg)
def fn(arg):
    print(arg)
apply_func(fn, 'Hello from function fn()') # Hello from function fn()
```

Function object returned from a function:
```py
def give_me_a_func():
    def returned_fn():
        print('Inside the function returned_fn()')
    return returned_fn
f = give_me_a_func()
f() # Inside the function returned_fn()
```


#### closures

An inner (nested) function, including objects from the enclosing scope, returned by its outer function creates a closure
```py
def create_greeting(greeting):

    def print_message(name):
        # Inner function using variable
        # from outer function
        print(f"{greeting}, {name}!")

    # Inner function returned
    return print_message


# writing the same using lamda
def create_greeting(greeting):
    return lambda name: print(f"{greeting}, {name}!")
# this is a function returning a function (common usecase)

>>> hello = create_greeting("Hello")
>>> hello("Harry")
Hello, Harry!
>>> hello("Ron")
Hello, Ron!

>>> hola = create_greeting("Hola")
>>> hola("Hermione")
Hola, Hermione!
```

* Usually the variables declared inside a function are garbage-collected after the function returns.
* But in this example the inner function is using the `greeting` variable.
* When the inner function is returned it carries the `greeting` variable with it stopping it from being garbage collected and preserving its state. This is called **closing over the variable**.
* `greeting` will be garbage collected when the nested function (`hello`) is garbage collected (when it goes out of scope)


* lamdas in C++ are different, you put in square brackets the state you capture
* in python, this happens implicitely

sidenote about writing functions in closures, they cannot be unittested
```py
def f():
    def step1(): # you cannot test this one
        pass
    pass

# better do this
def _f_step1():
    # ...

def f():
    _f_step1()
    # ...

```



#### decorators (closures with lambdas)

Simple usecase: Let's say we want a function that does tracing (it's a generic functionality that would be added in multiple functions)
* adding hardcoded: prints is not reusable, and not easy to reverse
* the pythonic solution is **decorators**

this is the idea of a decorator:
* it's a function `decorator_f`
* input: an original funciton `original_f`
* output: a new (modified original) function `wrapper_f`

```py
def decorator_f(original_f):    # decorator function
    def wrapper(*args, **kwargs):
        # do something before
        ret = original_f(*args, **kwargs)
        # do something after
        return ret
    return wrapper
```

> decorators' main usecase: having same behaviour with the original function  `original_f`, plus some extra functionality (typically before or after the execution of `original_f`)

```py
def trace(func):
    def wrapper(*args, **kwargs):
        print(f"Calling: {func.__name__} with {args} and {kwargs}")
        ret = func(*args, **kwargs)
        print(f"Exiting: {func.__name__} with {ret}")
        return ret
    return wrapper

def add(a,b):
    return a+b

add_with_tracing = trace(add)

add(3, 7)
add_with_tracing(3, 7)
```


* use the pie sytnax to write more readable code, people can see that you add a decorator easily
```py
# the following two are equivelant

# use decorator without pie sytnax
def add(a,b):
    return a+b
add = trace(add) # it's easy to forget or not notice (and then you don't know where is a specific kind of functionality)

# use decorator with pie sytnax
@trace
def add(a, b):
    return a+b
```

> Memoisation is a perfect usecase, they have it implemented already in `functools`
```py
def fib(n):
    if n in (0,1): return n
    return fib(n-1) + fib(n-2)

fib(35) # this takes a while
fib(50) # this takes way long

@functools.lru_cache
def fib(n):
    if n in (0,1): return n
    return fib(n-1) + fib(n-2)

# here we save the intermediate results (this is GOLDDDDD)
fib(35) # instant
fib(50) # instant
```

When decorating a function you might wish to retain meta-information.
A decorated function’s `__repr__`, `__name__`, and `__doc__` gets over-written (the latter changes the result of help()).
To do that we use the `wraps` decorator from the functools library module.
`wraps` decorator is a function that helps me write decorators (meta!!). Decorators with arguments like `wraps` are slightly different because they’re a function that returns a decorator.

```py
>>> sub
<function trace.<locals>.wrapper at 0x7ffefb809d08
>>> sub.__name__
wrapper
>>> sub.__doc__

from functools import wraps

def trace(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        pass
```

### LRU advanced

take the decorator

```py
@trace
@functools.lru_cache
def fib(n):
    if n in (0, 1): return n
    return fib(n-1) + fib(n-2)

@functools.lru_cache    # the order matters
@trace
def fib(n):
    if n in (0, 1): return n
    return fib(n-1) + fib(n-2)
```

## partial, operator

it's execution of a function, but you hard-code some inputs of the function

```py
def add(a,b):
    return a+b

inc = lambda x: add(1, x)
inc = partial(add, 1) # equivelant but nicer syntax

operator.getitem([1,4,9,16,25], 3)

sorted(example, key=lambda l: l[-1])
sorted(example, key=operator.itemgetter(-1)) # equivelant

# python is a functional language
```


### go over an example

```py
def trace(func):
    print("Calling trace!!")

def add(x,y):
    return x+y
add = trace(add)

@trace
def sub(x,y):
    return x-y

# the previous is equivelant to the following:
# def sub(x,y):
#     return x-y
# sub = trace(sub)

add(3, 4)
add(3, 4)
add(3, 4)

sub(8, 3)
sub(8, 3)
sub(8, 3)
sub(8, 3)
sub(8, 3)
```

How many times are the functions `add`, `sub`, `wrapper` and `trace` are called?
* 3 calls to `add`
* 5 calls to `sub`
* 8 calls to `wrapper`
* 2 calls to `trace` (only call them when I want to transform a function) (each time I apply a decorator)

#### homemade memoization

Memoization in python would look like this:
```py
# memo = {} # all of the functions would have the same cache
def memoize(func):
    memo = {} # <-- this is the correct position
    def func(*args, **kwargs):
        # memo = {} # each time you call the function, you would reset a cache and delete it
        nonlocal memo  # non-local to refer to upper scope
        if args not in memo: # args is already a tuple of arguments, which is hashable
            res = func(*args)   # * operator "unpacks" the tuple
            memo[args] = res
        return memo[args]
    return func

```

One thing to note:
```py
# this is caching when we use the memoization function!!! (not the function on which we call memoization)
@lru_cache
def memoize(func):
    @wraps
    def wrapper():
        # ...
    return func

def memoize(func):
    @wraps
    @lru_cache
    def wrapper():
        # ...
    return func
```
