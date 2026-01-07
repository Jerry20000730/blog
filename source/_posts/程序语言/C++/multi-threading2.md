---
title: 【C++】C++11 互斥量解决多线程数据共享问题
tags: [C++ Multithreading]
categories: Programming Languages
cover: images/ProgrammingLanguages/CPlusPlus/Multithreading/cover.png
excerpt: 在多个线程中共享数据时，需要注意线程安全问题。如果多个线程同时访问同一个变量，并且其中至少有一个线程对该变量进行了写操作，那么就会出现数据竞争问题
---

## 数据共享问题分析
在多个线程中共享数据时，需要注意线程安全问题。如果多个线程同时访问同一个变量，并且其中至少有一个线程对该变量进行了写操作，那么就会出现数据竞争问题。数据竞争可能会导致程序崩溃、产生未定义的结果，或者得到错误的结果。

_为了避免数据竞争问题，需要使用同步机制来确保多个线程之间对共享数据的访问是安全的_。

```cpp
#include <iostream>
#include <thread>
int shared_data = 0;
void func() {
    for (int i = 0; i < 100000; ++i) {
        shared_data++;
    }
}
int main() {
    std::thread t1(func);
    std::thread t2(func);
    t1.join();
    t2.join();
    std::cout << "shared_data = " << shared_data << std::endl;    
    return 0;
}
```

上述代码中，由于 shared_data 变量是全局变量，因此在两个线程中共享。对于这种共享的情况，需要使用互斥量等同步机制来确保多个线程之间对共享数据的访问是安全的。如果不使用同步机制，就会出现数据竞争问题，导致得到错误的结果。

常见的同步机制包括
1. 互斥锁
2. 条件变量
3. 原子操作

### 互斥锁
互斥量（mutex）是一种用于实现多线程同步的机制，用于确保多个线程之间对共享资源的访问互斥。互斥量通常用于保护共享数据的访问，以避免多个线程同时访问同一个变量或者数据结构而导致的数据竞争问题。

互斥量提供了两个基本操作：
- `lock()`
- `unlock()`

当一个线程调用 `lock()` 函数时，如果互斥量当前没有被其他线程占用，则该线程获得该互斥量的所有权，可以对共享资源进行访问。如果互斥量当前已经被其他线程占用，则调用 `lock()` 函数的线程会被阻塞，直到该互斥量被释放为止。

```cpp
#include <iostream>
#include <thread>
#include <mutex>
int shared_data = 0;
std::mutex mtx;
void func(int n) {
    for (int i = 0; i < 100000; ++i) {
        mtx.lock();
        shared_data++;        
        std::cout << "Thread " << n 
        << " increment shared_data to " << shared_data << std::endl;
        mtx.unlock();
    }
}
int main() {
    std::thread t1(func, 1);
    std::thread t2(func, 2);

    t1.join();
    t2.join();    
    std::cout << "Final shared_data = " << shared_data << std::endl;    
    return 0;
}
```

### `lock_guard` 和 `std::unique_lock`
`std::lock_guard` 是 C++ 标准库中的一种互斥量封装类，用于保护共享数据，防止多个线程同时访问同一资源而导致的数据竞争问题。

std::lock_guard 的特点如下：
- 当构造函数被调用时，该互斥量会被自动锁定。
- 当析构函数被调用时，该互斥量会被自动解锁。
- `std::lock_guard` 对象不能复制或移动，因此它只能在局部作用域中使用。

```cpp
#include <iostream>
#include <thread>
#include <mutex>

int shared_data = 0;
std::mutex mtx;

void func(int n) {
    for (int i = 0; i < 100000; ++i) {
        std::lock_guard<std::mutex> lock(mtx); // 使用 lock_guard 自动管理锁
        shared_data++;        
        std::cout << "Thread " << n 
                  << " increment shared_data to " << shared_data << std::endl;
        // lock_guard 在作用域结束时自动解锁
    }
}

int main() {
    std::thread t1(func, 1);
    std::thread t2(func, 2);

    t1.join();
    t2.join();    

    std::cout << "Final shared_data = " << shared_data << std::endl;    
    return 0;
}

```

`std::unique_lock` 是 C++ 标准库中提供的一个互斥量封装类，用于在多线程程序中对互斥量进行加锁和解锁操作。它的主要特点是可以对互斥量进行更加灵活的管理，包括延迟加锁、条件变量、超时等。

`std::unique_lock` 提供了以下几个成员函数：
- `lock()`：尝试对互斥量进行加锁操作，如果当前互斥量已经被其他线程持有，则当前线程会被阻塞，直到互斥量被成功加锁。
- `try_lock()`：尝试对互斥量进行加锁操作，如果当前互斥量已经被其他线程持有，则函数立即返回 false，否则返回 true。
- `try_lock_for(const std::chrono::duration<Rep, Period>& rel_time)`：尝试对互斥量进行加锁操作，如果当前互斥量已经被其他线程持有，则当前线程会被阻塞，直到互斥量被成功加锁，或者超过了指定的时间。
- `try_lock_until(const std::chrono::time_point<Clock, Duration>& abs_time)`：尝试对互斥量进行加锁操作，如果当前互斥量已经被其他线程持有，则当前线程会被阻塞，直到互斥量被成功加锁，或者超过了指定的时间点。
- `unlock()`：对互斥量进行解锁操作。
