---
title: 【C++】如何在C++中实现单例模式
tags: [C++ singleton]
categories: Programming Languages
cover: images/ProgrammingLanguages/CPlusPlus/Singleton/cover.png
excerpt: C++实现单例模式为常考题。
---

## 1. 懒汉式单例模式（Lazy Singleton）
懒汉式单例牧师只有在第一次使用的时候才创建实例

### 1.1 实现代码
```cpp
#include <iostream>
#include <mutex>

class Singleton {
public:
    // 获取唯一实例的静态方法
    static Singleton* getInstance() {
    
        std::call_once(initFlag, []() {
            instance = new Singleton();
        });
        return instance;
    }

    // 删除复制构造函数和赋值操作符，防止复制实例
    Singleton(const Singleton&) = delete;
    Singleton& operator=(const Singleton&) = delete;

    // 一个测试方法
    void showMessage() {
        std::cout << "Hello from Singleton!" << std::endl;
    }

private:
    // 私有构造函数，防止外部实例化
    Singleton() {}
    // 静态成员变量保存唯一实例
    static Singleton* instance;
    // std::call_once需要的标志，用于初始化一次性操作
    static std::once_flag initFlag;
};

// 静态成员变量初始化
Singleton* Singleton::instance = nullptr;
std::once_flag Singleton::initFlag;

int main() {
    Singleton* singleton = Singleton::getInstance();
    singleton->showMessage();

    return 0;
}
```
其中，`getInstance`方法：使用`std::call_once`来确保`instance`只被初始化一次，即使在多线程环境下也是安全的。

## 2. 饿汉式单例模式
饿汉式单例模式在类加载的时候就创建好一个静态实例，不管之后是否会用到这个实例。在多线程下是线程安全的。

### 2.1 实现代码
```cpp
#include <iostream>

class Singleton {
public:
    // 获取唯一实例的静态方法
    static Singleton* getInstance() {
        return instance;
    }

    // 删除复制构造函数和赋值操作符，防止复制实例
    Singleton(const Singleton&) = delete;
    Singleton& operator=(const Singleton&) = delete;

    // 一个测试方法
    void showMessage() {
        std::cout << "Hello from Singleton!" << std::endl;
    }

private:
    // 私有构造函数，防止外部实例化
    Singleton() {}

    // 静态成员变量保存唯一实例
    static Singleton* instance;
};

// 静态成员变量初始化
Singleton* Singleton::instance = new Singleton();

int main() {
    Singleton* singleton = Singleton::getInstance();
    singleton->showMessage();

    return 0;
}
```

## Reference
1. 【字节跳动】如何在C++中实现单例模式？@Lynn77-QAQ [https://www.bilibili.com/video/BV1NWs4ezEn5/?spm_id_from=333.1007.top_right_bar_window_history.content.click&vd_source=243ce9423fb82c395b4e9e6bf321ae0c](https://www.bilibili.com/video/BV1NWs4ezEn5/?spm_id_from=333.1007.top_right_bar_window_history.content.click&vd_source=243ce9423fb82c395b4e9e6bf321ae0c)