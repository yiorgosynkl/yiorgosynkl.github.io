---
layout: post
title: Learn C++ | Overview
cover-img: ["assets/img/posts/Beach-Set.jpg"]
tags: [coding, c++]
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

## Todos

* accessor to database
* CMakeLists for most projects guide
* services vs tasks

