---
layout: post
title: Code Log | Programming concepts | Vol 1
cover-img: ["assets/img/posts/Beach-Set.jpg"]
tags: [learn, 💻code]
---

## Awesome header-only C++ libraries

This [repo](https://github.com/p-ranav/awesome-hpp) contains the most popular libraries. 
The most popular are [argparse](https://github.com/p-ranav/argparse) & [cxxopts](https://github.com/jarro2783/cxxopts) for parsing arguments, [**Catch2**](https://github.com/catchorg/Catch2) for testing, [taskflow](https://github.com/taskflow/taskflow) for concurrency, [csv parser](https://github.com/ben-strasser/fast-cpp-csv-parser) & [simdjson](https://github.com/simdjson/simdjson) & [rapidjson](https://github.com/Tencent/rapidjson) for parsing data formats, [dlib](https://github.com/davisking/dlib) for ML, [**fmt**](https://github.com/fmtlib/fmt) for formatting (printing containers, etc), [concurrentqueue](https://github.com/cameron314/concurrentqueue/) for lock-free queue, [immer](https://github.com/arximboldi/immer) for immutable data structures and functional programming, [backward-cpp](https://github.com/bombela/backward-cpp) for clearer debug errors, [stb](https://github.com/nothings/stb) & [sokol](https://github.com/floooh/sokol) for geometry/graphics/games, [GuiLite](https://github.com/idea4good/GuiLite) for GUI creation,[cpp-httplib](https://github.com/yhirose/cpp-httplib) for HTTP/HTTPS functionalities, [pybind11](https://github.com/pybind/pybind11) for executing Python through C++, [spdlog](https://github.com/gabime/spdlog) for fast logging, [matplotlib-cpp](https://github.com/lava/matplotlib-cpp) for plotting with matplotlib framework, [nameof](https://github.com/Neargye/nameof) for reflection and name and type of function, [magic_enum](https://github.com/Neargye/magic_enum) for static reflection of enums, [cereal](https://github.com/USCiLab/cereal) for serealizing, [GSL](https://github.com/microsoft/GSL) for Mircosoft's Guidelines Support, [ranges](https://github.com/ericniebler/range-v3) which is now included in C++20, [crow](https://github.com/ipkn/crow) which is a microframework for web. 

## Compiler Explorer (godbolt), Compiler Flags, Libraries, etc

[Godbolt](https://godbolt.org/) is an insanely useful site to write snippets, run them and generally get an intuition of how the compiler works and transforms your code to assembely. The creator has two videos ([here](https://youtu.be/4_HL3PH4wDg) and [here](https://youtu.be/1u_ku_OJPDg)) to get you started. 

It's a good idea to start from scratch (press the button `More -> Reset code and UI`). 

The open a compiler and output box (you drag and drop them around and scale the letters). Then essential compiler flags `-Wall -Wextra` that should include all the other important flags. The use `-O1`/`-O2`/`-O3` to get the compiler optimise your code (a bit, mediocre or aggresively). You can also use `-std=c++14`/`-std=c++17`/`-std=c++20` to define specific version of the language.

You can also use `Catch2` library (use the libraries section in godbolt) and the `#include <catch2/catch_test_macros.hpp>` to write a funciton and quick tests for that function. Example follows:

```cpp
// select `Catch2 3.0.0-preview2` from Libraries section (upper right corner)
// select compiler `x86-64 clang 14.0.0`
// add compiler options `-Wall -Wextra -lCatch2Main -lCatch2`
#include <catch2/catch_test_macros.hpp>

// Type your code here, or load an example.
int square(int num) { return num * num; }

TEST_CASE("square root of 3", "[interger]") { REQUIRE(square(3) == 9); }
```

You can create permanent links for the code like [this](https://godbolt.org/#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:1,endLineNumber:10,positionColumn:1,positionLineNumber:10,selectionStartColumn:1,selectionStartLineNumber:1,startColumn:1,startLineNumber:1),source:'//+select+%60Catch2+3.0.0-preview2%60+from+Libraries+section+(upper+right+corner)%0A//+select+compiler+%60x86-64+clang+14.0.0%60%0A//+add+compiler+options+%60-Wall+-Wextra+-lCatch2Main+-lCatch2%60%0A%23include+%3Ccatch2/catch_test_macros.hpp%3E%0A%0A//+Type+your+code+here,+or+load+an+example.%0Aint+square(int+num)+%7B+return+num+*+num%3B+%7D%0A%0ATEST_CASE(%22square+root+of+3%22,+%22%5Binterger%5D%22)+%7B+REQUIRE(square(3)+%3D%3D+9)%3B+%7D%0A'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:50,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((g:!((h:compiler,i:(compiler:clang1400,filters:(b:'0',binary:'1',binaryObject:'1',commentOnly:'0',debugCalls:'1',demangle:'0',directives:'0',execute:'0',intel:'0',libraryCode:'0',trim:'1',verboseDemangling:'0'),flagsViewOpen:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,libs:!((name:catch2,ver:'300-preview2')),options:'-Wall+-Wextra+-lCatch2Main+-lCatch2',overrides:!(),selection:(endColumn:1,endLineNumber:1,positionColumn:1,positionLineNumber:1,selectionStartColumn:1,selectionStartLineNumber:1,startColumn:1,startLineNumber:1),source:1),l:'5',n:'0',o:'+x86-64+clang+14.0.0+(Editor+%231)',t:'0')),k:50,l:'4',m:50,n:'0',o:'',s:0,t:'0'),(g:!((h:output,i:(compilerName:'x86-64+clang+14.0.0',editorid:1,fontScale:14,fontUsePx:'0',j:1,wrap:'1'),l:'5',n:'0',o:'Output+of+x86-64+clang+14.0.0+(Compiler+%231)',t:'0')),header:(),l:'4',m:50,n:'0',o:'',s:0,t:'0')),k:50,l:'3',n:'0',o:'',t:'0')),l:'2',n:'0',o:'',t:'0')),version:4).

## Variadic Templates (used in a cache example)

The following code makes a key (identifier for a cache). The interesting thing is that the function can take any number of parameters (vectors of any type or plain types) and will use delimeters `.` and `-` to split the vectors and different arguments. To use any number of parameters in C++, we use variadic templates. 

```cpp
#include <string>
#include <vector>
#include <iostream>
#include <sstream>

// ========> generic_cache.h <======== 

/// Helper function to emit a value to a stream
template<typename T>
void toKey(std::ostringstream& os, const T& item)
{
    os << item;
}

/// Helper function to emit dot-separated vector of values to a stream
template<typename T>
void toKey(std::ostringstream& os, const std::vector<T>& itemVec)
{
    if (itemVec.size() == 1)
    {
        toKey(os, itemVec.front());
    }
    else
    {
        auto it = itemVec.begin(), end = itemVec.end(), last = end;
        --last;
        for (; it != end; ++it)
        {
            toKey(os, *it);
            if (it != last)
                os << ".";
        }
    }
}

/// Helper function to emit arguments from variadic pack - termination case
inline void emitProviderArg(std::ostringstream&)
{
}

/// Helper function to emit arguments from variadic pack.
template<typename ProviderArg, typename... Tail>
void emitProviderArg(std::ostringstream& os, ProviderArg arg, Tail... tail)
{
    // Get head
    toKey(os, arg);
    // argument delimiter
    os << "_";
    // Call recursively with the tail of pack
    emitProviderArg(os, tail...);
}

/**
 * Compose cache identifier from a generic variadic pack `ProviderArgs`.
 * Each variadic pack item could be a C++ scalar or vector (delimited by dots).
 * Pack args are delimited by `_`
 * Example:
 *    makeCacheIdentifier(std::vector<int>{42}, "foobar") -> "42_foobar_"
 */
template<typename... ProviderArgs>
std::string makeCacheIdentifier(ProviderArgs... providerArgs)
{
    std::ostringstream oss;
    emitProviderArg(oss, providerArgs...);

    std::string identifier{std::move(oss).str()};
    return identifier;
}

int main() {
    std::vector<int> vec = {2,3,4};
    std::cout << makeCacheIdentifier(std::vector({2,3,4}), "foo", std::vector({6,5})) << std::endl;
    return 0;
}
```

## Encoding / Decoding Essential Knowledge

It all started with ASCII, which used 1 byte per letter (8 bits and 2^8 = 256 combinations) but represented only 128 letters (from 0 to 127). The last bit could be used for anything, people were doing fancy stuff for different languages (and a mess started happening when sending messages from Greek to Hebrew for example). 
Unicode is a platonic character set (it assigns a number to each character and does not define how to store it). UTF-8, UTF-16, UTF-32 are encodings of these. UTF-8 uses variable-width encoding (and more specifically the english alphabet letter match ASCII numbering and need only 1 byte, while greek letter need 2+ bytes). There is also an issue with big/little endian that is fixed using a two byte prefix. 


* [C++ Character Encoding Explained](http://blog.petrovich.ch/index.php/2018/06/17/c-character-encoding-explained/)
* [Joel Spolsky - The Absolute Minimum Every Software Developer Absolutely, Positively Must Know About Unicode and Character Sets](https://www.joelonsoftware.com/2003/10/08/the-absolute-minimum-every-software-developer-absolutely-positively-must-know-about-unicode-and-character-sets-no-excuses/)


## Using pandoc and latex expresion to create slides with markdown

[Pandoc](https://pandoc.org/MANUAL.html) is a universal document converter. Take a markdown and make latex, pdf, pptx, etc. It's super useful and quite flexible.
Recently I was reading some interesting papers, specifically about OCO (online convex learning). The following is a paragraph about the problem, that serves as template of how to use markdown to write mathematical formulas. [Here](https://www.upyesp.org/posts/makrdown-vscode-math-notation/) and [here](https://www.cmor-faculty.rice.edu/~heinken/latex/symbols.pdf) are cheatsheet for math symbols in latex. Then using the command `pandoc -t beamer text.md -o presentation.pdf` you can produce awesome professional slides. Don't forget the following YAML at the header of the file for customization. 

```yaml
---
title: "Adaptive Online Non-stochastic Control Presentation"
date: "December, 2024"
author: "Georgios Giannakoulias"
institute: "For TU Delft"
theme: "Frankfurt"
output: 
  beamer_presentation:
    keep_tex: true
---
```

### OCO (online convex learning)

The OCO framework can be seen as a structured repeated game.
* There is a decision set: $\mathcal{K} \subseteq \mathbb{R}$. (convex set in Euclidean space) and at iteration $t$, the online player chooses $x_t \in \mathcal{K}$ .
* After the player has committed to this choice, a convex cost function $c_t \in \mathcal{F} : \mathcal{K} → R$ is revealed. The function is being chosen by an adversary. 
* The cost of any solution $x \in \mathcal{K}$ until time $T$ is $$C_{\mathcal{x}}(T) = \sum_{t=1}^T c_t(x_t)$$  
* An algorithm $\mathcal{A}$ maps a certain game history to a decision in the decision set. The goal is to create an algorithm $\mathcal{A}$ that minimizes regret $$Regret_{T}(\mathcal{A}) = C_{\mathcal{A}}(T) - \min_{x \in \mathcal{K}}{C_{x}(T)}$$



## Parsing input in competitive programming (Python/C++)

Parsing input files is essential in competitive programming, in platforms like [codeforces](https://codeforces.com/), [adventofcode](https://adventofcode.com/), etc. Here is a collection of tools/functions/ways to parse input in my favourite languages.

```cpp
// using argument for the file
int main(int argc, char * argv[]) {
    std::string input;
    if (argc < 2) {
        std::cout << "Provide the input file path as an argument" << std::endl;
        return 1;
    }
    input = argv[1];
    // ...
    return 0;
}

// you could also provide a default file name to use
```

```cpp
// std::getline(), std::string::substr and std::stoi
int main(int argc, char * argv[]) {
    std::string line;
    std::fstream file(input);

    std::vector<int> nums;
    while(std::getline(file, line)) {
        // imagine line like `893 432`
        std::size_t space_idx = line.find(' ');  // play with indices
        nums.push_back(std::stoi(line.substr(0, space_idx))); // [pos, pos+count)
        nums.push_back(std::stoi(line.substr(space_idx+1,line.size() - space_idx)));
        // assume indices [start, end), the formula is `substr(start, end-start);`
        // assume indices [start, end], the formula is `substr(start, end-start+1);`
    }
    // ...
}


// you can also use a delimiter with getline, for more fine grained parsing
#include <iostream>
#include <sstream>

int main(){
    // ...
    std::string input = "abc,def,ghi";
    std::istringstream ss(input);
    std::string token;

    while(std::getline(ss, token, ',')) {
        std::cout << token << '\n';
    }
    // ...
}


// using double loop for two delimeters
int main(){
    std::string line, token;
    std::ifstream file("text.txt");
    if(file.is_open()){
    while(std::getline(file, line)){   // get a whole line
        std::stringstream linestream(line);
            while(getline(linestream, token, ',')){
                // use token like (std::stoi(token))
            }
        }
    }
}

// using stringstream and >> 
std::string input = "...";
std::stringstream ss(input);
std::string word; // temporary token
std::vector<std::string> words;  
int wordcount = 0;
while(ss >> word){
    words.push_back(word);
    wordcount++;
}

```

This [link](https://marcoarena.wordpress.com/2016/03/13/cpp-competitive-programming-io/) is super useful as well.

```cpp
// reading lonely input
int N, M;
cin >> N >> M;

// read a sequence of number
// using the synergetic copy_n + istream_iterator + back_inserter trio
int length; cin >> length;
vector<int> sequence; sequence.reserve(length); // prepare vector to host “length” elements
copy_n(istream_iterator<int>(cin), length, back_inserter(sequence));


// with ranges the code streamlines
// bounded returns a range of exactly length elements
vector<int> boundedIn = view::bounded(istream_iterator<int>(cin), length);

// reading a matrix 
int rows, cols; cin >> rows >> cols; 
vector<vector<int>> matrix; matrix.resize(rows); 
for (auto& row : matrix)
{    
   row.reserve(cols); 
   copy_n(istream_iterator<int>(cin), cols, back_inserter(row)); 
}

// fixing leading ‘\n’ issue with cin and getline
cin >> N >> std::ws;
getline(cin, line);

// printing answers with cout, avoid using std::endl to avoid flushing and enhance performance 
cout << ans1 << " " << ans2 << "\n";

// When you need to print a sequence – like a vector – just use copy + ostream_iterator:
copy(begin(v), end(v), ostream_iterator<int>(cout)); // default separator is ' '

copy(begin(v), end(v)-1, ostream_iterator<int>(cout, ',')); // using comma seperator for all but last
cout << v.back(); // last

// in C++17, we have this smart syntax
copy(begin(v), end(v), make_ostream_joiner(cout, ','));

// print floating point numbers and the output is expected to be an approximation of a certain number of digits
cout << fixed << setprecision(2) << num1 << " " << num2;

// initialize before looping
cout << fixed << setprecision(2);
while (...)
{
    //...
    cout << ans; // formatted as you like
}
```

In python, the syntax is much more straightfoward and nice:
```py
# you can change the following to read through files
FILE = False # if needed change it while submitting

if FILE:
    sys.stdin = open('input.txt', 'r')
    sys.stdout = open('output.txt', 'w')

# generic functions
def get_int():
    return int(sys.stdin.readline())

def get_ints(dem=" "):
    return map(int, sys.stdin.readline().strip().split(dem))
# a, b = get_ints("1 2") --> a = 1, b = 2
# a, *rest = get_ints("1 2") --> a = 1, b = [2]

def get_string():
    return sys.stdin.readline().strip()

def get_strings(dem=" "):
    return map(str, sys.stdin.readline().strip().split(dem))


# using regex
re.split('; |, |\*|\n', string_to_split) # multiple delimiters


```



```py
# example from adventofcode 2021 day 4 (https://adventofcode.com/2021/day/4)
with open("file.txt") as data_file:
    nums, *boards = data_file.read().split("\n\n")
    nums = [int(n) for n in nums.split(",")]
    boards = [[int(n) for n in board.split()] for board in boards]
```


For complicated problems, regex is the life saviour.

## C++ Macros for consice clean code

Introducing the concept of ESMA (ESsential MAcros). This intorduces uniformity in a department, in terms of how to log erros (e.g. the line and filename, the names of the variables and values), throwing and catching errors with one-liners etc.

Introducing different kinds of throwing errors ([godbolt link](https://godbolt.org/#z:OYLghAFBqd5QCxAYwPYBMCmBRdBLAF1QCcAaPECAMzwBtMA7AQwFtMQByARg9KtQYEAysib0QXACx8BBAKoBnTAAUAHpwAMvAFYTStJg1DIApACYAQuYukl9ZATwDKjdAGFUtAK4sGe1wAyeAyYAHI%2BAEaYxBKS0gAOqAqETgwe3r56icmOAkEh4SxRMVLSdpgOqUIETMQE6T5%2BXLaY9rkM1bUE%2BWGR0XoKNXUNmc2DXT2FxRIAlLaoXsTI7BwA9KsA1Fg0DMHAG9hCALIAggobEIdKgnhiG6fIxEkzJhon6xsK8RV4NKK0tAAnhsmBs0FgNtkUgINiwmI9UK8TuYAMzBZDeCEmFFuQbEPbY7BI1HozGYDbYtxOPGYViE4lmNEMDFeLE4hQ0ukoolvEnMskU9kELCqZbxAj03mM0ms8mU1Di1JiSXIt4fbEAEU12pRWt1Or1euwG0wCjhAH1wZgoe0AHQIQVuA3O/Wuw0Ut7MNhfeHk01wikAdisbzxXgcGw8WGUSWhDCDIZOG2TGw%2BACVrcRTYwCOdQTbUhtgiDPgsluSrRsaPRbUj4l4IrQ8MgQEiU6nNm409gTgAVADyaaEbZTUZUsfaEEG6BAIDxe0hTAICFIRcEGybIReKMT7fbHzcWaX5NBYA4Y5jOVSZ42Wao0XxRg2RGfCHJXx%2BNEw6CrdHJZ/iJcEBvQx0BHPcOw2M9N0wG8GD6Yha15N5902DU%2B1VJMU2nWd5yfdBzWrTBsV3FNggICCUwImCSOJN54nxAA3Y9WxQlMPmUNMAEkADU%2B2NE43DcQ4hEHYc2OTHC5wIR99iIr1MCnYVcJkhdAOXGYwQEQZaIkvcPkwVQZPhCiiI2BSqyeFgS3Uh0IGXd8KnaEEqAIaINyYQZPgMBQEBmJCsIgj4uKoKsvABRdlxNBh0HOAB3QgHQUHyHQcjY4oQTxyVsotzizAhFhCdAApMQM9UTJEpKSGTaRYcwADYNgVaIlxISlKSUmcQGqo86rMRqklXNAGC8i8J1SBqNkY7cKqlFFtmCclDlOc03H7DVsGUfshAgTT/SYWcxqvAQIHNc0ADEuICbAztXM6Ai40IbvNF49I%2BJNhsYxg8EYZZYXhJ5n1QMEjzcksjrjdLEtfckzsu66zpBGLwMg%2B7Hue9K3yzGH/oRXKwTEehiro5EyuTD4MSSP0zXzeEAGsmGAckFJ9ZYSaRdU3RdQ0XWNfbLQwa1xoEW1kHieJHW5qW3Q9E4WcAv79oTEmIfaQ7BcvONOpU2TIpXNcKJg16ThALZCL/ag/wU7WQBYVAvogWyZmdpFV2oxaICN4lg1KrVkLOZTpN11XUlneTWEUqS8P2J2tJGiVeR9vThq85IAC9MHNCjskFDU9dtHZqM8ghzVQKgIHMMxVkrmaUd%2BC4c4NT5A%2Bj2cGEbwMnU7yEklzzV8/oIxl12jYAFoNi4Y321K0iIPywq9d0wKgz95f5%2BIeNbNtBQGzxR2kmsSel99kmPnO7tjSEXtuxOI4Nn7ZRsDTPtB0qwOetqybmuIVriHanEbYfzpP1JqCghraQoiHAQk1pre1nreTABUN6gMljiCkZgzAmAAKxWAwagtwU1bQETMv/AhldWx4NIYQ92IR8HoMwVgrUGDj5lXZmTSClMlAmhpouZADMmbmQjqzYi/tOY8y5hI8RfMabmkMmKdoy4nhxWiPaVB4j1Hc1lvLX03CAwz1PusbAqhWDxHoBsRQjN2AbF7AgJRJYpK0FQMAZssjiBPGIFDKKLBwqOFMX6NxJBYSmgUJYjmmwDjHBOOaXsAAJNM/YADqNtHHOOQK49xpAwmUUopXJMKTmwmgCR4hAnkmrIGQIsb8tZmFqnCdk6eGCEkID/BsEpMUFwKFQGwZcexqlmFrrUrJJpbQZSXCPZ0tSUzYIsJ0tgRFRbiwoSiVEKzGEbDyU4gpD5AklPOKgcplTiobCaS0tp%2BAnxCC6T0owfT2aeiEQrckWAah0GVv7NyLBTHHkpAQQE3wLInGIMAVcvz/kRw2LaSFgLgAKBVFHVST5hqiAIBAaFDVJq1GBesoFsL%2BoNUhbaEEOLjb6L0lVaOnJrIHx3CjXuVCpL8GIHFWoYEcRou5BATFAzl4QEYqgPA6BNJSWCCkMQeAM7EHNE2HSOJyL0iTsvdsGhVwQDpTiSkUEOCaroQykgzLiCsrcNC2FHLMUKDmBsDQ/lIUnxpXpde8YkjbxkrtFhq9faQW0X9Z5TA6Ds0ZAtWhy0omxPiUk7AAANYSyhexcX7KEVcBLNL1JTTkrBTo9KKNQHFA4UbNqxvjRAfas4fV0FnEisZaaM2KtTbW4Nq11qbW2iPM6fFzQP17GdCApBk1TPTSjWttbW1RJOGmAA4kIM6Ls2IfBsXY0Ekbo0FoTZ4h0lYCwwlAiCCKDF7YCu/GuRlcJnJKATlhD4pTF35rjaEEZJSUX%2BQ2KEVAYM0pXpjTe58fzyTeK8iUr6JYU4yXDEQP%2BM7Ng1DpqaexCL9ijJLpirYmAdhVP9fNZDi0IkrVDYk80XFzoQE4tgDUq533LsTZCzS2Dq3tnroR7sGoqPBkHSx5M1GB0pnrTh8NeaP3xruuaNto6J1TpIhSftekT7gesbY7NJYyOfvrkRvOmBmLeGPM%2BYgXhMCrgSlFddwt4xbsJiOD4u7GL7p/MEI9S5CynpBOcBT8a71jMfc%2B19b5c1Ls/aCn9XgdLSf/SeOOYYHCBMg9B0E0dMZLnNIhwNqH/ZZLSsogm8YEu4yeOcRlsJqpaRYHbeMcjrTOV8%2BcBk6GUNYaiWmOQoRY1HBuk/eJaYIBJuq9EuJiSbZaZuGwdJJABNCfHZOl6aGMv1tq/VrijXzTNcHHhgjymKO2l7ax9be52N6S411hJi36PEdXFJXrjh%2BvbLIBsYd5phOjZJZMu22NtkKFNj/IwmdzurnttEKgji4oDYu14GK33fv/dXPktJ52snBDUwKuLQKfA5jdl031DBQcbkYMAZcaOFglzLuaV7TNxsYaDZEwTJwAhyBugt7jEAO03vJ6R3j5GIWUY22z2tW2eV0/jeTwU50NgACpuehF56bHbYbLhM5vauVEwveeV3xvBAECursdq7auTSV2btTqoxJzCqIJuk74hTqnaZzRTYa01tMLXad8ZFwEFba32ebb1%2B2etxvKftrNzTuXDvm5dRO3gM7RShsjpG6Jt6mwHXnEB1gYgQIFxqe00WUKp7VytC4VmuKeYiuihK6kQlnwvDixILmFGHwyuviXE1L6bj90bAiKgTwqwBdNW%2BD/UDYCTT7B/jm8UF2qqKgEGITJkeG/oFoBEWc4QARMEbJgHiYhtOF7SsVof8YMS0nj8CFJ5w0pEVWDBJGP5MVnlhWPhSAVULl82IZEx9Bns34c8kYADA2CCFNiYeqGggMGymncfud3cnT3BbC3GbK3G3JmAgfsdfMQXaWub/J/NfZyFgBQUACkRAnBKgVQWgEuZA1ILPFRLwW0AgBZeIEASQeqLgbBPOKAmZWgGA9oOAzSPAc4JXWgL/DQO5UmPODhRxLhJWBWfhZmB5X0Emcif6YIcZBVdsKSNALwCiKhetNaDaLaHablFGSbOrS3Oba3BbfDCADQPuXUCeGXDBOQEIVQb4BwA9TAAARy8DFV%2BRrmPjH0HyYI4NlUEEJBBHiDwAzB3lwLEwr1k2zzXHMkMkNkWhRncKVE8KpG8O5F8P8NNB8Vzg2CwSXnbCYAUOBksXSKAJNy93N20PAN0JtyYD8ICJ8Q0PtUQQXi4LtR4KRA4DmFoE4CwV4D8A4C0FIFQE4CdEsGsFLEWD%2BlRB4FIAIE0FaLmDphACwWVXaI4EkC6OmL6M4F4Ge2VSmJ6NaNIDgFgBgEQBQC6T8PoDIAoAgDQE%2BT/BiGACkDMD4DoDcmIGewgCn12NIAiGCFqEBE4AmO%2BOYGIEBH7AiG0Ccn%2BN4GuPf2gIYCBDWKwAiC8GADcEJme24F4CwDhCMHEE%2BPwCzEqC%2BnRN6MMgqAUJWAmPIlaDWKbAiB/mBLHDWNUhYEhNIFr0byUA1EwGxOAE3FAF2LmB%2B0ZgUB4h%2BjihgMYFZP4EEBEDEHYCkBkEEEUBUHUE%2BN0GaAMCMBQGsGsH0DwAiGe0gDmAVHaHRN4C%2BzrywENN2haDaFDiLRihGCaFIECEWimH6GaA3TSE8EaCyEM0mAQgGFaCciqCGHqB9NGFtJDIEE6DqADKKA9NsDDKdIGDDPjOmEnnmFGPlLaI6NWM%2BP6K1VUAAA56pR56pJANhgBykJ5JBbQzALhcBCBAlxiZheAditBnZSA5iFj9BOAVjSAWTezujejCzNiQBtjpi5gDjjjSSKlQNyBKAzVlBDBWghBMo4puiJjrizjbMBAVyQhaB1zs0RyoTTjbiQB7jJBHidzbjQgI4NjSBbzzj%2BwFDjzNy1i5zjVHy5zqhzlHzpThB/h5TpBALlS1A1j1T9BDBjAdTLA9SDT4BjT18zS%2Bja98QrSkLSBA82Bewm9aBrS5hOlszUzzkDy1yNytz2ysx2BlU4of5yCMTcyOBOjSBTz1iOAjEyTQMNgSyyyKyqyaypB6yLhBjcEbADh8AeLWz2ypy5g3wmA49KBmKByhzlV2KxzbAJzJi5Luz5jFjOAUR8zRzHyOyZi%2ByOAzBjLzTTLdLa9khnBJAgA%3D)):
```cpp
// defining ESMAs (ESsential MAcros)
// specifically a code position macro
#include <string>
#include <iostream>
#include <sstream>
#include <stdexcept>
#include <optional>

// ========> esma_codeposition.h <======== 
namespace esma {
struct CodePosition {
    // Represents a position in a source code file.
public:
    // CREATORS
    CodePosition(std::string path, int line);
        // Create a 'CodePosition' referring to the specified file 'path' and
        // 'line' number.

    // DATA
    std::string d_file;
    int         d_line;

private:
    // PRIVATE ACCESSORS
    std::string filename(std::string path) const;
        // extract file name from a path (the section after last slash).
        // If full path ends with slash the whole path is returned.
};

std::ostream& operator<<(std::ostream& os, const CodePosition& v);

#define ESMA_CODEPOS() esma::CodePosition(__FILE__, __LINE__)
    // A convenience macro to create a CodePosition with the __FILE__ and
    // __LINE__ where the macro is called.

}  // close esma package namespace


// ========> esma_codeposition.cpp <======== 
namespace esma {

CodePosition::CodePosition(std::string path, int line)
: d_file(filename(std::move(path)))
, d_line(line)
{}

std::string CodePosition::filename(std::string path) const
{
    const size_t pos = path.find_last_of("/");
    if (pos == std::string::npos || pos == path.length() - 1)
    {
        return path;
    }
    return path.substr(pos+1);
}

// FREE STREAM OPERATOR
std::ostream& operator<<(std::ostream& os, const CodePosition& v)
{
    return os << "[" << v.d_file << ":" << v.d_line << "]";
}

}  // close esma package namespace

// ========> esma_exceptionthrower.h <======== 
namespace esma {

///Example Usage: Throw a std::logic_error with multiple error message
//  ESMA_THROW(std::logic_error,
//              "A logic error has occured."
//              "While handing something.");
//
//  e.what() ==
//   [somefile.cpp:###] A logic error has occured. While handing Somthing."


namespace detail {

template<typename Arg, typename ...Args>
std::string concat(Arg&& arg, Args&&... args)
{
    std::ostringstream os;
    os << std::forward<Arg>(arg);
    (void) std::initializer_list<int>{
        0, (os << ' ' << std::forward<Args>(args), 0)...};
    return os.str();
}
} // namespace detail

#define ESMA_THROW(EXCEPTION, ...)                           \
    throw EXCEPTION(esma::detail::concat(       \
                            ESMA_CODEPOS() __VA_OPT__(,)     \
                            __VA_ARGS__))
  // Throw a EXCEPTION, with code position and all provided information set
  // as EXCEPTION.what(). Note the EXCEPTION type must have a constructor
  // takes a string what_arg defined.

#define ESMA_THROW_IF(PRED, EXCEPTION, ...) \
    if (PRED) {                             \
        ESMA_THROW(EXCEPTION, __VA_ARGS__); \
    }
  // Throw a EXCEPTION if PRED evaluate true, with code position and all
  // provided information set as EXCEPTION.what(). Note the EXCEPTION type must
  // have a constructor takes a string what_arg defined.


// the we can define macros for most common exception types 
#define ESMA_RUNTIME_ERROR(...) ESMA_THROW(std::runtime_error, __VA_ARGS__)

#define ESMA_RUNTIME_ERROR_IF(PRED, ...)                                       \
    ESMA_THROW_IF(PRED, std::runtime_error, __VA_ARGS__)

// more erros: range_error, overflow_error, underflow_error, logic_error
// invalid_argument, domain_error, length_error, out_of_range

#define ESMA_VALUE_OR_THROW(OPTIONAL, EXCEPTION, ...)                                               \
    (OPTIONAL ? *OPTIONAL : ESMA_THROW(EXCEPTION, #OPTIONAL " is null" __VA_OPT__(, ) __VA_ARGS__)) \

#define ESMA_VALUE_OR_RUNTIME_ERROR(OPTIONAL, ...)                            \
    ESMA_VALUE_OR_THROW(OPTIONAL, std::runtime_error, __VA_ARGS__)
    // returns underlying value if set, else throws an exception.  supports
    // types that override bool/* operators, eg raw ptr, std::optional,
    // bdlb::NullableValue.  the exception clearly logs the file/line and arg's
    // name.
    //
    // examples:
    // assignment: `const int val = ESMA_VALUE_OR_RUNTIME_ERROR(getOptional());`
    // exception msg: `[fxlt_exceptionthrower.u.t.cpp:461] getBslOptional() is null`


}  // close esma package namespace

int main() {
    std::cout << ESMA_CODEPOS();

    ESMA_RUNTIME_ERROR_IF(0 == 1, "Unexpected equality");

    // std::optional<int> apiResult; // throws in next line
    std::optional<int> apiResult = 5;
    auto age = ESMA_VALUE_OR_RUNTIME_ERROR(apiResult);

    return 0;
}
```

[Godbolt link](https://godbolt.org/#z:OYLghAFBqd5QCxAYwPYBMCmBRdBLAF1QCcAaPECAMzwBtMA7AQwFtMQByARg9KtQYEAysib0QXACx8BBAKoBnTAAUAHpwAMvAFYTStJg1DIApACYAQuYukl9ZATwDKjdAGFUtAK4sGIMwCcpK4AMngMmAByPgBGmMQgAMxBAA6oCoRODB7evv6p6ZkCYRHRLHEJybaY9o4CQgRMxAQ5Pn6B1bVZDU0EJVGx8UlBCo3NrXkdo739ZRXDAJS2qF7EyOwcAPSbANRYNAzhwDvYQgCyAIIKOxCnSoJ4YjuXyMTpCyYaF9s7CimYyDwNFEtFoAE8dkwdmgsDs0hk6gwdiwmK9UJ8LuZEuFkN5YSZEm5RsQjgTsBisTi8ZgdgS3E5iZhWGSKV8fgSACKc7mJLm8nl8vnYHaYBQogD6MMw8KKDAAdAhaYSBSr%2BWrBbSvsw2H9UTTRSjaQB2KxfYleBw7DxYZSFRHG00XHbOnY/ABK0uIosYBGuUJl9vCkN%2BKzWNKlOxo9DlGJSXhitDwyBAGJdrt2bjd2AuABUAPJuoSpl3WlR2rIQUboEAgYlHOFMAgIUg7cIEHaJiIfRKOtNpn5uL2NmlQsAcUu2hFZMc7L1UeIkow7IjLhA0v4AoF4TDoSN0GljlKNhAzwzoYt99M7MedzAzhiDYgxr4Xq8c3OYr5pqs1utL9DilGmAEr2LptpeLoAbeIGshcKQkgAbsOKZfi6PzKG6ACSABqubChcbhuKcQgFkWqHOj%2BtYEIuxxAdqmCVgQ1ZUTRDZNgs0ICKMMHkX2PyYKo1Gou2QE7PRkZvCwwZHk2NxNuuAL2kwVAEPEHZMKMvwGAoCALM%2BToQT8mFUJGXigmxiquNcADuhCKgo2mKvJOzWQgng0jJip4NcXoEKsEToPpJhGnyjoYpR6TUUyLDmAAbDsqD/MQjYkHSdKMcxkVDjFZjxekLZoAwmkTuWAhxTsCHdmFL5mIk%2BzhDSpyXOKbh5hy2DKHmQgQBxBpMDWJVTgIEDiuKABimEhNgo0tqNISYZE03ih8vE/E6hUIYw24MOsyKom8y6oNCQ6qcGg2yi5dmrjSo0TVNo2Qgw56rbsc0LUtLlrl6117WirbXCC9CBbBwUcs6Py4uk%2Bpiv6qIANZMMANL0bq6wg2yuyqoKWM8sKfWShg0qlfKyApCkSpuDj6pY5qFwo0eu19Q6sHnYiA2E5OsoZb%2B1H1p5LbgbeK0XCAeyAfu1D7vR3MgCwqCbRAnkLMrGItlBDUQELFImqDsGUX%2Bxys1kNZ0awDH67zS5K5xRUENroHOoVmkZAAXpg4rtvCSpg55coHFBGkEOKqBUBA5hmJs4dVa%2BQI3F7Aq/ExPM0TWDDx0alMZ3C6Te5yFlyvQRhNj1OwALQ7FwwtpsFDt9r5/kWTxBnOrrvH18QSK%2Bwo8bEor6TWJXTet5%2B3y7GNWbCkIOZZhcZw7HmyjYG6uYFuFScgFl0XlYl8QpcQaWEjLm/MrlCUKAVXHtkbZWn5V9uvu3SI5wfbi0mYZgmAArFY78U3SFVygAqJF%2Bb8zApl/iAhCgDxS3j/oSUBX8uTvyHiFEGIVwa7EhkoEUMMGzIARkjMSZtUbARfF8cCKJwglxrq%2BSiaAvDthAU1C4LU2odS6j1IebdMB%2BQ7jsDQKCuRfA4EsWgnBP68D8BwLQpBUCcEppYawIZVi7SxDwUgBBNAiKWHDEAn8ND6E4JISRWjZGcF4AoEABjNHSJEaQOAsAYCIBQKgFgKR9xkAoBANAbiPEgGAFIMwfA6CqWIJYiAMRTExHCE0MEnB1HROYMQMEeYYjaEUvE3gPi2CCDzAwcEpisAxC8MANwYhaCWO4LwLAKIjDiFsaQfAXoHB4E2pUmRAkAQMI2OotsNRTGJhiMlZJpZTG8xYJk0gm1iAxChhyTAtTgCdlALYpYVADDAAUNhbc1k8z/Ckeo/gggRBiHYFIGQghFAqHUA03QXB9CGGMNYaw%2Bg8AxEsZAJYiVESVN4PLBceAsAfJ6p0RSxsICuAmH4e5oQGpzCGPcgMWQoV6CRcUOFj49B2DBfUMYLRPBtCxTUHFDAejNFmJi%2B50xxgEryFSvFFLygIqWAoUM6wJCiPESYhpciOA7FUAADliqXWKkgdjAGQMgCukg5RmBuLgQgJA36JErrwGxWhlakF0fowxHBjGkAmTqqRMjeUWKsRorRSwHHOM6cgBhJByCUCaJs5QhgahCDctZA5WTXHuIMIiV1ERaAetQF60xPi/VDACZIIJEaPGRDNuY0gcb6DEDzAwkNYaGm2ouMQTZSbbUNHwFI3gRzhAgjOdIMtVy1CmLuQ8owKBnmWFee8%2BAXyUg/KTf84gJIgXttIMQLwDw2A5lQJ4YFLK2VnNsExcIgb3Weu9Ror07ADHWWSikTJnKOASNIMav5nBsCqC6UQYg/KhUirFRKqVUhZU3AUT/GwJx8BnuVaqi1qylhriYFgBIIKxF6t4IagxB6zEcDNdYy1Oi9EGMA4kblJqk3qu0bqswiHD0Qc/RqpY0yMjOEkEAA%3D%3D%3D) for the following code:
```cpp
// defining ESMAs (ESsential MAcros)
// specifically a code position macro
#include <string>
#include <iostream>

// ========> esma_codeposition.h <======== 
namespace esma {
struct CodePosition {
    // Represents a position in a source code file.
public:
    // CREATORS
    CodePosition(std::string path, int line);
        // Create a 'CodePosition' referring to the specified file 'path' and
        // 'line' number.

    // DATA
    std::string d_file;
    int         d_line;

private:
    // PRIVATE ACCESSORS
    std::string filename(std::string path) const;
        // extract file name from a path (the section after last slash).
        // If full path ends with slash the whole path is returned.
};

std::ostream& operator<<(std::ostream& os, const CodePosition& v);

#define ESMA_CODEPOS() esma::CodePosition(__FILE__, __LINE__)
    // A convenience macro to create a CodePosition with the __FILE__ and
    // __LINE__ where the macro is called.

}  // close esma package namespace


// ========> esma_codeposition.cpp <======== 
namespace esma {

CodePosition::CodePosition(std::string path, int line)
: d_file(filename(std::move(path)))
, d_line(line)
{}

std::string CodePosition::filename(std::string path) const
{
    const size_t pos = path.find_last_of("/");
    if (pos == std::string::npos || pos == path.length() - 1)
    {
        return path;
    }
    return path.substr(pos+1);
}

// FREE STREAM OPERATOR
std::ostream& operator<<(std::ostream& os, const CodePosition& v)
{
    return os << "[" << v.d_file << ":" << v.d_line << "]";
}

}  // close esma package namespace

int main() {
    std::cout << ESMA_CODEPOS();

    return 0;
}

```
## hungarian notation

There are two version. "Systems Hungarian" is the useless version because it's just typing the type of a variable into the name. Modern editors can show the type automatically. And there are so many types in general, it's useless. The useful version is "Apps Hungarian", and it's about semantic information, that means noting safe vs unsafe strings, `max`, `min` values, `index` of arrays, pointers to stuff, maps / sets / ranges of stuff. [This post](https://stackoverflow.com/questions/1848614/where-can-i-find-a-cheat-sheet-for-hungarian-notation) is useful. I like to have conventions like these in my interview questions. 

`arr_prices` or `rng_prices` for arrays (or `prices` is usual), `map_name_2_age` for maps, `idx_price` or `i` or `i_price`. I like also `max_price` or `mx_price`, `cur_price`, `prev_price`. I use `lo`, `hi` for limits, or `lim_prices`, `len_prices` for length. I use `dif_prices` for difference, `cnt_prices` or `n_prices` for counters, `ptr_prices` for pointers, `tmp_price` for temporary price (save value to assign later). For Hungarian notation, there are popular equivelants like `Sav`, `Prev`, `Cur`, `Next`, `Nil`, `Min`, `Max`. Also for C++, it's useful to have `g_` for global variables, `m_` for class members. I also like `isXXX` (like `isValid`) for functions that return booleans, `getXX` / `setXX` (like `getPrice`) for functions that return values or set specific fields.

## dangling reference problem

I had the following thing (simplified version) when doing some tests with an object that contained references (`name&` in `Person`) and a function creating a prepopulated dummy version of an object.

```cpp
// the DANGLING REFERENCE problem
#include <string>
#include <iostream>


struct Person {
    const std::string& name;    // references are evil!!
                                // usually when this is really expensive you think of passing by reference, but now you don't have control of the lifetime!!!
    int age;
};

Person makeDefaultPerson(){
    const std::string& dummy_name = "Bob"; // you create dummy_name here
    int dummy_age = 23;
    return Person{
        .name = dummy_name,
        .age = dummy_age
    };
    // the dummy name is deleted here!! but there is still a reference to this!!
}

int main() {

    const auto p = makeDefaultPerson();
    std::cout << "Person has age " << p.age << std::endl;
    std::cout << "Person has name " << p.name << std::endl; // you are using name that does not exist
    
    return 0;
}
```

The solution to the problem would be to make copies. But sometimes this is expensive.

The most straightforward solution is using shared pointer:
```cpp
// solution with shared pointer!!
#include <string>
#include <iostream>
#include <memory>

struct Person {
    std::shared_ptr<std::string> name; 
    int age;
};

Person makeDefaultPerson(){
    const std::string dummy_name = "Bob";
    // auto dummy_name2 = std::make_shared<std::string>("Bob"); // could also initialise as unique pointer to string from the beginning

    int dummy_age = 23;
    return Person{
        .name = std::make_shared<std::string>(std::move(dummy_name)), // the shared pointer of Person object will increase the reference counter of string "Bob"
            // using move to avoid unnecessary copy!!
        // .name = dummy_name2,
        .age = dummy_age
    };
    // the dummy_name2 shared pointer going out of scope will decrease the shared pointer's reference counter of string "Bob"
    // but the counter is still positive, string "Bob" is still referenced, hence it won't be deleted
}

int main() {

    const auto p = makeDefaultPerson();
    std::cout << "Person has age " << p.age << std::endl;
    std::cout << "Person has name " << *p.name << std::endl;
    
    return 0;
}
```

Having to initialise a variable like `make_shared` is problematic. Because frequently other people wrote the code before me and later down the line I may introduce `shared_ptr`.
The easy way to solve it is to do an unnecessary copy, but sometimes this is really expensive (for large objects).
The smart way to remember is moving the object (and maybe introducing some const conversion magic):



Another solution is using `unique_ptr`. Imo, really unpredictable and unintuitive to use, the benefit is marginal. (it's difficult to pass as arguments to functions, it's difficult to transfer ownership if something is `const`, bad error messages, ...).

```cpp
// solution with unique pointers
#include <string>
#include <iostream>
#include <memory>
#include <utility>

struct Person {
    std::unique_ptr<std::string> name; 
    int age;
};

Person makeDefaultPerson(){
    auto dummy_name = std::make_unique<std::string>("Bob"); // you have to initialise using a smart_pointer
                // otherwise, you are doommed to make an unnecessary copy (or you will have to use weird `movea)
    int dummy_age = 23;
    // std::unique_ptr<std::string> dummy_name2 = std::move(dummy_name);
    return Person{
        .name = std::move(dummy_name), // the shared pointer of Person object will increase the reference counter of string "Bob"
        .age = dummy_age
    };
    // the dummy_name shared pointer going out of scope will decrease the shared pointer's reference counter of string "Bob"
    // but the counter is still positive, string "Bob" is still referenced, hence it won't be deleted
}

int main() {

    const auto p = makeDefaultPerson();
    std::cout << "Person has age " << p.age << std::endl;
    std::cout << "Person has name " << *p.name << std::endl; // you are using name that does not exist
    *p.name = "Tony";
    std::cout << "Person has name " << *p.name << std::endl; // you are using name that does not exist
    
    return 0;
}
```

## unique pointers and transferring ownership, const pointer of T vs pointer of const T

There is a different between `const pointer of T vs pointer of const T`. 
We use so often `const auto&` as a view of a variable in C++, but it needs to be used carefully with pointers.
This example gives the chance to showcase how awful error messages C++ has.

```cpp
#include <string>
#include <iostream>
#include <memory>
#include <utility>


int main() {
    const std::unique_ptr<std::string> cup_s = std::make_unique<std::string>("Ken"); 
    std::cout << "Const unique pointer of string " << *cup_s << std::endl; // you are using name that does not exist
    *cup_s = "Barbie";
    std::cout << "Const unique pointer of string updated to " << *cup_s << std::endl; // you are using name that does not exist
    // cup_s = std::make_unique<std::string>("John"); // ERROR: "discards qualifiers" aka CONSTNESS ERROR (you said it's const, aka it will not change, but now you try to change it)

    std::unique_ptr<const std::string> up_cs = std::make_unique<const std::string>("Ken"); 
    // std::unique_ptr<const std::string> up_cs = std::make_unique<std::string>("Ken"); // Also possible, casting from string to const string is possbile here
    std::cout << "Unique pointer of const string " << *up_cs << std::endl;
    // *up_cs = "Barbie"; // ERROR: "no match for 'operator='", such a bad error message :(
        // it's actually a CONSTNESS ERROR 
    up_cs = std::make_unique<const std::string>("Mary");    
    std::cout << "Unique pointer of const string updated to " << *up_cs << std::endl; 

    auto up_cs2 = std::move(up_cs); // Transferring ownership of unique pointers
    // std::cout << "Unique pointer of const string " << *up_cs << std::endl; // Segmentation Fault aka accessing wrong memory
        // but nobody prohibits you from using this BROKEN unique pointer!!!
    
    // auto up_cs3 = std::move(cup_s); // COMPLIATION ERROR: moving a const pointer fails
        // moving means that you are changing the state of `cup_s`, but that violates CONSTNESS

    return 0;
}
```

## lambdas: declaring them, assigning them to variables etc.

```cpp
#include <string>
#include <iostream>
#include <cassert>
#include <functional>
#include <algorithm>

int getSizeA(const std::string& s){
    return s.size();
}

// you can pass lambda functions as arguments to other functions
int getSumA(int a, const std::string& s, const std::function<int(const std::string&)>& string2num){
    return a + string2num(s);
}

// you can also have a default argument for the function
int getSumB(int a, const std::string& s, 
    const std::function<int(const std::string&)> string2num = std::function{[](const std::string& s) -> int {
        return s.size();
    }}
){
    return a + string2num(s);
}

// in C++20 and forward, you can have auto arguments in functions!!

int main() {
    std::function<int(const std::string&)> getSizeB = [](const std::string& s){
        return s.size();
    };

    // no need to declare the input parameters twice
    // you can also declare the return type with cool syntax
    auto getSizeC = [](const std::string& s) -> int {
        return s.size();
    };

    assert((4 == getSumA(1, "hey", getSizeC)));
    assert((4 == getSumB(1, "hey"))); // using the function with default lambda


    // using a different string2num conversion
    auto getAs = std::function{[](const std::string& s)-> int {
        return std::count_if( s.begin(), s.end(), []( char c ){return c =='A';});
    }};
    assert((10 == getSumB(7, "BANANA", getAs))); 

    // this shows the manual labour you need to do in C++
    // although versatile, it is not expressive out of the box and not concise when writing code


    // More notes:
    // * lambdas are anonymous, you need to assign them to a variable to reference them
    // * you can pass scope variables inside the `[]`, that can be accessed in the body of the function
    // * std::function cannot take defaults. It would be complicated to implement, without particular benefit. default parameters don't make much sense for lambdas, 
    //   as they are short lived, and used for particular reasons. 
    // * however, for a lambda argument, you can also define a default lambda (see above)

    return 0;
}
```

## c++ naming convention with auto

In C++17 and above, it's equivelantly efficient to write the following ([link](https://stackoverflow.com/questions/39536814/c-using-auto-to-declare-classes-as-variables-inside-functions)):
```cpp
Dog obj1{args...};
auto obj2 = Dog{args...};
auto obj2 = Dog(args...); // could also do this

// also 
auto obj3 = std::make_unique<Dog>(args...);
```

Using auto always is generally more consistent. You can avoid problems with weird constructor sytnax.
You should use intellisense to view the types of variables.

## c++: returing references

You can write functions that return references. Remember to pass relevant input as simple reference (not const ref) and to use `auto&` when calling the function. 
In general, `const ref` variables are useful when just viewing an object and simple `ref` functions are used when we need to update member data of the variable. 

> Note that in the functional programming paradigm, we never update an existing object. We alwyas copy, update a field and then reassign. Functions never have side effects to inputs parameters. In the contrary, when passing by `ref` (and not `const ref`) in C++, the user can change whatever he wants, and you just trust that he operates in the correct manner (not corrupting your data, etc).

```cpp
// copy to https://godbolt.org/
#include <string>
#include <iostream>
#include <cassert>

struct Person {
    std::string name;
    int age;
};

struct Deal {
    Person buyer;
    Person seller;
    int amount;
};

Person getBuyer(const Deal& deal){
    return deal.buyer;
}

Person& getBuyerRef(Deal& deal){
    return deal.buyer;
}

int main() {
    // useful way initializing simple structs
    auto p1 = Person{
        .name = "Mike",
        .age = 22
    };

    auto dealA = Deal{
        // .buyer = Person{
        .buyer = {
            .name = "Mike",
            .age = 22
        },
        .seller  = {
            .name = "Bob",
            .age = 25
        },
        .amount = 43
    };

    auto& buyerA = getBuyerRef(dealA);
    // checking references actually are referring to same objects (not just equality)
    assert((&buyerA == &dealA.buyer)); // same Buyer object

    // see this in action
    assert((buyerA.name == dealA.buyer.name));
    buyerA.name = "Mary";
    assert((buyerA.name == dealA.buyer.name)); // both variables changed name

    const auto buyerB = getBuyer(dealA);
    assert((&buyerB != &dealA.buyer)); // different objects

    return 0;
}
```

## python acronyms

[EAFP](https://docs.python.org/3/glossary.html#term-EAFP): Easier to ask for forgiveness than permission. This coding style is assumes the existence of valid keys or attributes and catches exceptions if the assumption proves false. 
[LBYL](https://docs.python.org/3/glossary.html#term-LYBL): Look before you leap. It's the opposite of the previous coding style. This coding style explicitly tests for pre-conditions before making calls or lookups.
[REPL](https://docs.python.org/3/glossary.html#term-REPL): Read-eval-print loop. It's another name for the interactive interpreter shell. [Replit](https://replit.com/) and other tools take the name from it. Originally developed for the LISP language (the mother of functional programming) [according to wiki](https://en.wikipedia.org/wiki/Read%E2%80%93eval%E2%80%93print_loop).


## VScode like a pro

Firstly, with `CMD + SHIFT + P` I can bring the Command Palette and search for all utilities. This is the smartest thing in VSCode.
In Command Palette type `Shortcuts` to view the provided shortcuts. 
You can also view a few cheat sheets before reading below for my personal favourties. 

* I often want to see the implementation of the function i view. In Command Palette type "Go To Implementation" (shortcut: `CMD + Click`)
* I often want to see where the function is called. In Command Palette type "Find All References" (shortcut: `CMD + SHIFT + F12` or `CMD + F12`).
* 
