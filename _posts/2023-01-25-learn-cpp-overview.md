---
layout: post
title: Learn C++ | Overview
cover-img: ["assets/img/posts/Beach-Set.jpg"]
tags: [💻code, c++]
---

Here are resources for basic concepts about c++. 

## List of lessons


## Cheatsheet



#### [Interfaces in C++](https://stackoverflow.com/questions/318064/how-do-you-declare-an-interface-in-c)

In general, interafces don't exist. 
You just declare a class with no implementation ( = 0; at the `.h` file) and let the derived classes (the ones that inherit) actually define the behaviour.
An example [here](https://stackoverflow.com/questions/4057774/instantiation-of-objects-in-conditionals-c).
It's better to use shared pointers and it would look like this:

```cpp
class Base { ... }
class DerivedAlpha : public Base { ... }   // A is a Base
class DerivedBeta : public Base { ... }   // B is a Base

...

std::shared_ptr<Base> p0 = std::make_shared<Base>(...);
std::shared_ptr<Base> p1 = std::make_shared<DerivedAlpha>(...);
std::shared_ptr<Base> p2;
if (condition)
{
  p2 = std::make_shared<DerivedAlpha>(...);
}
else 
{
  p2 = std::make_shared<DerivedBeta>(...);
}
```

## Tips and Tricks

#### Quick way to find the type of variable at compile time

Sometimes we use auto or access an internal type. A quick dirty way to get the job done, following [this](https://stackoverflow.com/questions/39634609/get-type-of-expression-at-compile-time) stack overflow post is to define the `show_type_abort` in the header file:
```cpp
// -------- define in .h file --------
template<typename T>
void show_type_abort_helper()
{
    return __PRETTY_FUNCTION__; // returning in void functions forces compiler to fail and abort
}
#define show_type_abort(x) show_type_abort_helper< decltype(x) >()


// -------- use in .cpp file like --------
std::vector< int > s{1, 2, 3};
show_type_abort(s);  // usage
show_type_abort(s[0]);  // can be called multiple times

// -------- you get an error message like --------
// test.cpp:7:12: In instantiation of ‘void show_type_abort_helper() [with T = int]’:
//                                                                             ^^^ (the type I want)
// test.cpp:7:12: error: return-statement with a value, in function returning 'void' [-fpermissive]
```

## Todos

* accessor to database
* CMakeLists for most projects guide
* services vs tasks
