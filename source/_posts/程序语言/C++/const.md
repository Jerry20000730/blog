---
title: Tips when defining global constant in header file
tags: [C++]
categories: Programming Languages
cover: images/ProgrammingLanguages/CPlusPlus/cover.png
excerpt: Defining a constant in C++ to be visible in several source files. There are several ways of doing that, but you could make mistakes if not careful enough.
---

## Options of defining global constant in header file
1. `#define GLOBAL_CONST_VAR 0xFF`
2. `const int GLOBAL_CONST_VAR = 0xFF;`
3. `extern const int GLOBAL_CONST_VAR;` and in one source file uses `cosnt int GLOBAL_CONST_VAR = 0xFF;`

According to _Effective C++_, ITEM 2: 

> Prefer `const`, `enum`, and `inline` to `#define`. 

So option 1 is not preferred (which I particularly like to use). In my project, I have a header file that uses three constant string.

```C++
// util.hpp
const char * CLIENT_CONNECT_CONFIRM = "CLIENT_CONFIRM";
const char * SERVER_CONNECT_CONFIRM = "SERVER_CONFIRM";
const char * PLAYER_CONNECT_CONFIRM = "PLAYER_CONFIRM";
```

However, when I run the makefile, it has the error goes like:

```
/usr/bin/ld: player.o:/home/fg96/ECE650/HW3/util.hpp:14: multiple definition of `CLIENT_CONNECT_CONFIRM'; util.o:/home/fg96/ECE650/HW3/util.hpp:14: first defined here
```

Which clearly indicates that the `CLIENT_CONNECT_CONFIRM` are regarded not as the constant, but variable, but why? Reason is that `const char * ...` is not constant, because `const char *` means that the value pointer points to cannot change, but the value of the pointer can change (very silly mistake).

The following definition will be just fine:

```C++
// all const pointers pointing to const values
const char * const CLIENT_CONNECT_CONFIRM = "CLIENT_CONFIRM";
const char * const SERVER_CONNECT_CONFIRM = "SERVER_CONFIRM";
const char * const PLAYER_CONNECT_CONFIRM = "PLAYER_CONFIRM";

// const pointers is also fine
char * const CLIENT_CONNECT_CONFIRM = "CLIENT_CONFIRM";
char * const SERVER_CONNECT_CONFIRM = "SERVER_CONFIRM";
char * const PLAYER_CONNECT_CONFIRM = "PLAYER_CONFIRM";
```

Since C++17, `inline` specifier can also be used on variable. According to the cppreference:

> An inline function or variable(since C++17) with external linkage (e.g. not declared static) has the following additional properties: There may be **more than one definition** of an inline function or variable(since C++17) in the program as long as each definition appears in a different translation unit and (for non-static inline functions and variables(since C++17)) all definitions are identical. For example, an inline function or an inline variable(since C++17) may be defined in a header file that is **included in multiple source files**.

But we are not allowed to use c++17 so ...

Another possibility would be to use namespace
```C++
namespace {
    char * CLIENT_CONNECT_CONFIRM = "CLIENT_CINFIRM";
}
```

## Reference

1. [Defining global constant in C++](https://stackoverflow.com/questions/2268749/defining-global-constant-in-c)
2. [头文件中定义const全局变量应注意的问题](https://blog.csdn.net/ace_fei/article/details/8587403)