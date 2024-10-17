---
layout: post
title: knowledge sharing
cover-img: ["assets/img/posts/Beach-Set.jpg"]
tags: [💻code, c++]
---

<!-- # knowledge sharing -->

## revision of basic concepts

#### The Rule of Three/Five/Zero

* if you need one of the following, you need all
  * Copy constructor
  * Destructor
  * Copy assignment operator
  * Move constructor
  * Move assignment operator

* this is also inlcuded in the [core guidelines](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#Rc-zero)

```cpp
#include <string>
#include <iostream>

// functions that need to be called at ction and dtion
// for some business reason
void registerForPrices(const std::string& ykString){
  std::cout << "registering " << ykString << std::endl;
}
void deregisterForPrices(const std::string& ykString){
  std::cout << "deregistering " << ykString << std::endl;
}

// cmdyfuture.h
class CmdyFuture
{
public:
  CmdyFuture(const std::string& ykString);
  CmdyFuture(const CmdyFuture& original);
  CmdyFuture& operator=(CmdyFuture copy);
  ~CmdyFuture();

private:
  std::string d_ykString;

friend void swap(CmdyFuture& a, CmdyFuture& b);
};

// cmdyfuture.cpp
CmdyFuture::CmdyFuture(const std::string& ykString)
: d_ykString(ykString)
{
  registerForPrices(d_ykString);
}

CmdyFuture::~CmdyFuture()
{
  deregisterForPrices(d_ykString);
}

CmdyFuture::CmdyFuture(const CmdyFuture& original)
: d_ykString(original.d_ykString)
{
  registerForPrices(d_ykString);
}

CmdyFuture& CmdyFuture::operator=(CmdyFuture copy)
{
  swap(*this, copy); // swap the old ykString
  return *this;
  // the dtor will be called here for old ykString
}

void swap(CmdyFuture& a, CmdyFuture& b)
{
  std::swap(a.d_ykString, b.d_ykString);
}

int main(){
  // usage
  CmdyFuture c("EQUITY");
  c = CmdyFuture("COMDTY"); // this is why you need rule of three
  return 0;
}
```

#### RAII

Resource Acquisition Is Initialization
* Essentially in C++ we should acquire all resource in Ctrs (start of lifecycle) and we release/freeup all resources in Dtrs (end of lifecycle)
* is the simplest, most systematic way of preventing leaks (that's why there is no garbage collection in C++)

[RAII in official docs](https://en.cppreference.com/w/cpp/language/raii):
* encapsulate each resource into a class, where
  * the constructor acquires the resource and establishes all class invariants or throws an exception if that cannot be done,
  * the destructor releases the resource and never throws exceptions;
* always use the resource via an instance of a RAII-class that either
  * has automatic storage duration or temporary lifetime itself, or
  * has lifetime that is bounded by the lifetime of an automatic or temporary object

* the benefit is that we don't have to think about allocation of memory anymore and compenets are reusable
  * e.g. if we use `vector` of weird stucts, it's gonna deallocate everything when we stop using it
  * e.g. if we have custom classes that do weird allocations, we can still use them inside other objects (such as `vector`)

#### Inheritance and Polymorphism

* inheritance: it's all about having one common interface / API and trigger different behaviours dynamically

* overloading is polymorphism, same name for different functions (you look at the arguments to understand which to call)

> note that you need virtual functions which use Vtable and that makes runtime a bit slower
> !!always have a virtual destructor!!
> there is an alternative for static polymorphism in C++11 is `variant`

```cpp
#include <string>
#include <iostream>
#include <memory>
#include <ctime>
#include <cstdlib>

/*
          Person
        /       \
    Grandpa     Youngster
                /     \
          Millenial    CmdyFuture
*/

// Person
class Person
{
public:
    Person(const std::string& name)
    : d_name(name)
    {}
    virtual ~Person(){} // !!! always use that
    virtual std::string greeting() const = 0;

    std::string d_name;
};

class Grandpa : public Person
{
public:
    Grandpa(const std::string& name, int pension)
    : Person(name), d_pension(pension)
    {
    }
    std::string greeting() const override {
        return "Well, hello sir! Did you read the newspaper today?";
    }

    int d_pension;
};

class Youngster : public Person
{
public:
    Youngster(const std::string& name, int pocketMoney)
    : Person(name), // calls the base class ctor
    d_pocketMoney(pocketMoney)
    {}
    int d_pocketMoney;
};

class Millenial : public Youngster
{
public:
    Millenial(const std::string& name, int pocketMoney)
    : Youngster(name, pocketMoney) // calls the base class ctor
    {}
    std::string greeting() const override {
        return "What's up man? What's your insta?";
    }
};

class GenZ : public Youngster
{
public:
    GenZ(const std::string& name, int pocketMoney)
    : Youngster(name, pocketMoney) // calls the base class ctor
    {}
    std::string greeting() const override {
        return "Don't talk to me, I'm watching TikToks now!!";
    }
};

std::unique_ptr<Person> getRandomPerson(){
    int r = (rand() % 3); // either 0, 1, 2
    if (r == 0){
        return std::make_unique<Grandpa>("Uncle George", 5000);
    }
    else if (r == 1){
        return std::make_unique<Millenial>("MC GG", 120);
    }
    else { // if (r == 0
        return std::make_unique<GenZ>("Ginfluencer", 5);
    }
}

int main(){
    srand(time(0));  // Initialize random number generator.

    for (int i = 0; i < 10; i++){
        std::unique_ptr<Person> u_p = getRandomPerson();
        std::cout << u_p->greeting() << std::endl;
    }

    return 0;
}
```

* Inheritance lets us inherit attributes and methods from another class. Polymorphism uses those methods to perform different tasks. This allows us to perform a single action in different ways.
* Polymorphism is like duck-typing, you get same behaviour

> always put virtual Destructor in classes with at least one virtual functions !!! (otherwise you delete the dynamic/derived-class-type with the dtor of the static/base-class type and it leads to undefined behaviour, resource leaks, ...)

#### algorithms, iterators and functional programming coding style

the main purpose of iterators is to decouple conainters and algorithms (it's a generalisation of position for algorithms)
* iterators: implementations differ per compiler: some use pointers, some use indices,
* they are useful abstraction and common API for making the algorithms generic (`begin`, `next`, etc.., same API)
* `const_iterator` is used for read only (we CAN mutate the iterator, but CANNOT mutate the object that the iterator is pointing to)
> note that C++ does not provide good structures for `trees`

* Stepanov: the creator of the iterators
* there are family's of iterators and algorithms make requirements on these iterators
* they make some implicit requirements for the underlying containers

conatiners and iterators they provide:

| STL CONTAINER | ITERATOR SUPPORTED |
| ------- | -------- |
| Vector |  Random-Access |
| List | Bidirectional |
| Dequeue | Random-Access |
| Map | Bidirectional |
| Set | Bidirectional |
| Stack | N/A |

Examples:
* `for_each` ([cppreference](https://en.cppreference.com/w/cpp/algorithm/for_each)): the algorithms only needs the forward move (hence requires `LegacyForwardIterator` or a "stronger" iterator)
* `sort` ([cppreference](https://en.cppreference.com/w/cpp/algorithm/sort)): the algorithm only needs the forward move (hence requires `LegacyRandomAccessIterator`)
* table of iterators to examine and note: https://en.cppreference.com/w/cpp/iterator

some resources for mastering algorithms:
* https://hackingcpp.com/cpp/cheat_sheets.html
* https://github.com/gibsjose/cpp-cheat-sheet/blob/master/Data%20Structures%20and%20Algorithms.md

> using algorithms helps writing more functional style code

> in C++20 we have [constraints and concepts](https://en.cppreference.com/w/cpp/language/constraints)

## tips and fun facts

#### tools

> use [spaces](https://spaces.dx.bloomberg.com/) to have a out-of-the-box environment, no weird setups etc.

> always use [cppreference](https://en.cppreference.com/w/cpp/language/types) for official documentation (this is the correct resource)

> favourite tool to quickly test code is [compiler explorer](https://gcc.godbolt.org/)

#### sanitizers

* use sanitizers (checks for buggy code)
    * Address Sanitizer (ASan): use-after-free, double-free, buffer (stack, heap and global buffer) overflows and underflows, along with other memory errors ([link](https://microblink.com/be-wise-sanitize-keeping-your-c-code-free-from-bugs/))
    * Memory Sanitizer (MSan): uninitialized memory reads
    * Undefined Behavior Sanitizer (UBSan): use of null pointers, division by zero, signed integer overflow and other undefined behavior
> always use static analysis tools and sanitizers

#### C++11/14/17 mentionalbe features

* use keyword `auto`
* smart pointers, move semantics
* use `override` in derived classes for functions that exist also in base class
* use `delete` to stop the usage of ctors, dtors (before we used to make them private)
* `= 0` for pure virtual functions that have no implementation
* `optional` is good way to handle Nullable
* use `explicit` in ctors to avoid implicit converstion being made
* use `[[fallthrough]]`, `[[noreturn]], [[maybe_unused]]` attributes to make code readable (or a comment) (it is good to distinguish if there is a bug or an actual thing the developer intended to do) [link here](https://en.cppreference.com/w/cpp/language/attributes)

#### compiler errors

* always add this flags when compiling `-Wall -Wextra -Wpedantic` (these warning are useualy errors)
* [good warnings](https://github.com/cpp-best-practices/cppbestpractices/blob/master/02-Use_the_Tools_Available.md#gcc--clang)
* in `CMakeLists.txt` the following warnigns should always exist: `configure_compiler_options(-Wall -Wextra -Wshadow -Wnon-virtual-dtor -pedantic)`

#### Thoughts on performance:

* in order to have fast code, you need data locality (things that are close to each other in memory are quick to access as well, because you already brought them from RAM)
* even if you need to read one byte, you will get a cache line (at least 64 bytes)

examples:
* linked-lists vs vectors, [benchmarking vector](https://dzone.com/articles/c-benchmark-%E2%80%93-stdvector-vs)
* using `make_shared`, `make_unique` for smart pointers,
```cpp
std::shared_ptr<Dog> sptr (new Dog("azor", 3));  // this makes allocation two times
std::shared_ptr<Dog> sptr = make_shared<Dog>("azor", 3); // this makes allocation one time
auto sptr = make_shared<Dog>("azor", 3); // even better
```

#### Guidelines for pointers:

* always prefer stack instead of heap (automatic and fast allocation of resources)
* always use smart pointers (don't use `new/delete`),
* first option is `unique_ptr` (`shared_ptr` only make sense in multithreaded applications, it's often used in prod code to not think about lifetime of things, but in general it's harder to reason about)
* Raw pointers should be used as observing pointers (could be thought as references with NULL state) (sometimes are mandatory because of old code)

> If you only care about the object, pass it by reference (forget about the pointer)

> Many people use `shared_ptr` to handle custody automatically and not worry about lifetimes (inefficient since you have things at the heap and it's harder to think about owneship)

## good resources to learn C++

* [herb sutter blog](https://herbsutter.com/gotw/), best way to learn in the long run, has really good examples and it's free
* scott meyers has good sections about containers and algorithms
* [core guidelines](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#Rc-zero)
