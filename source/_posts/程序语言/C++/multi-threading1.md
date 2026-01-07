---
title: 【C++】C++11 Thread线程库的基本使用
tags: [C++ Multithreading]
categories: Programming Languages
cover: images/ProgrammingLanguages/CPlusPlus/Multithreading/cover.png
excerpt: C++11 Thead线程库的基本使用，包括如何创建线程、启动线程、等待线程完成以及如何分离线程。
---

# 创建线程
C++11中，我们可以使用函数指针、函数对象或lambda表达式来实现。创建线程的基本语法如下：

```cpp
#include <thread>

std::thread t(function_name, args...);
```

- `function_name` 是线程入口点的函数或可调用对象

- `args...` 是传递给函数的参数

创建线程后，我们可以使用`t.join()`等待线程完成，或者使用`t.detach()`分离线程，让它在后台运行。

Example:
```cpp
#include <iostream>
#include <thread>
void print_message() {    
    std::cout << "Hello, world!" << std::endl;
}

int main() {
    std::thread t(print_message);    
    t.join();    
    return 0;
}
```

# 传递参数
使用函数参数、全局变量、引用都可以向线程传递参数
```cpp
#include <iostream>
#include <thread>
void print_message(const std::string& message) {
    std::cout << message << std::endl;
}

void increment(int& x) {
    ++x;
}

int main() {
    std::string message = "Hello, world!";    
    std::thread t(print_message, message);    
    t.join();    
    
    int x = 0;    
    std::thread t2(increment, std::ref(x));    
    t2.join();    
    std::cout << x << std::endl;    
    return 0;
}
```

需要注意的是，当我们使用引用传递参数时，我们需要使用 `std::ref` 来包装引用，否则编译器会报错。

在 C++ 中，`std::thread` 构造函数模板接受可调用对象和参数时，会复制传递给它们的参数，防止因多个线程同时修改同一数据而可能引起的访问冲突或数据竞争。然而，在某些情况下，你实际上希望线程直接作用于原始变量或对象，而不是副本，这里的 `increment` 函数设计为修改传递给它的原始整数


# 等待线程完成
使用`t.join()`方法来等待线程完成

```cpp
#include <iostream>
#include <thread>

void print_message(const std::string& message) {    
    std::cout << message << std::endl;
}

int main() {    
    std::thread t1(print_message, "Thread 1");    
    std::thread t2(print_message, "Thread 2");    
    t1.join();    
    t2.join();    
    std::cout << "All threads joined" << std::endl;    
    return 0;
}
```

# 分离线程
与 `join` 不同，我们可能不需要等待线程完成，而是希望它在后台运行

```cpp
#include <iostream>
#include <thread>

void print_message(const std::string& message) {    
    std::cout << message << std::endl;
}

int main() {    
    std::thread t(print_message, "Thread 1");    
    t.detach();    
    std::cout << "Thread detached" << std::endl;    
    return 0;
}
```

# `joinable()`
joinable()方法返回一个布尔值，如果线程可以被join()或detach()，则返回true，否则返回false。如果我们试图对一个不可加入的线程调用join()或detach()，则会抛出一个std::system_error异常。

```cpp
#include <iostream>
#include <thread>
void foo() {
    std::cout << "Thread started" << std::endl;
}
int main() {
    std::thread t(foo);
    if (t.joinable()) {
        t.join();
    }
    std::cout << "Thread joined" << std::endl;
    return 0;
}
```

# 线程函数中的数据未定义错误

## 传递临时变量

```cpp
#include <iostream>
#include <thread>
void foo(int& x) {
    x += 1;
}
int main() {
    std::thread t(foo, 1); // 传递临时变量
    t.join();
    return 0;
}
```
我们创建了一个名为`t`的线程，将`foo`函数以及一个临时变量`1`作为参数传递给（int引用）。这样会导致在线程函数执行时，临时变量`1`被销毁，从而导致未定义行为。

## 传递指向局部变量的指针或引用：

```cpp
#include <iostream>
#include <thread>
void foo(int* ptr) {
    std::cout << *ptr << std::endl; // 访问已经被销毁的指针
}
int main() {
    int x = 1;
    std::thread t(foo, &x); // 传递指向局部变量的指针
    t.join();
    return 0;
}
```

## 

# Reference
1. [C++11 Thread线程库的基本使用](http://www.seestudy.cn/?list_9/31.html)