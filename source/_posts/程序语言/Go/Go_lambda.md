---
title: Go Programming Languages - Functional Programming
tags: [Go]
categories: Programming Languages
cover: images/ProgrammingLanguages/Go/cover.svg
excerpt: Golang Functional Programming in brief
---

## Functional Programming in GO: Closure (闭包)
```go
import "fmt"

func adder() func(int) int {
    sum := 0
    return func(v int) int {
        sum += v
        return sum
    }
}

func main() {
    a := adder()
    for i:=0; i<10; i++ {
        fmt.Printf("0 + 1 + ... + %d = %d\n", i, a(i))
    }
}
```
闭包与Python类似，里面有函数体，函数体里有局部变量和自由变量（e.g., `sum`）。

对应的Python闭包方式
```python
def adder():
    sum = 0

    def f(value):
        nonlocal sum
        sum += value
        return sum

    return f
```
其中`nonlocal`就是标准，表示该变量不是局部变量

## 缺点
但是 Go 中的函数式编程因为不像Python一样不用限制类型（泛型的缺失，duck typing），就会导致后续代码会很难维护

比如
```go
func main() {
    var list = []string{"Orange", "Apple", "Banana", "Grape"}
    // we are passing the array and a function as arguments to mapForEach method.
    var out = mapForEach(list, func(it string) int {
    	return len(it)
    })
    fmt.Println(out) // [6, 5, 6, 5]
}

// The higher-order-function takes an array and a function as arguments
func mapForEach(arr []string, fn func(it string) int) []int {
    var newArray = []int{}
    for _, it := range arr {
    	// We are executing the method passed
    	newArray = append(newArray, fn(it))
    }
    return newArray
}
```
如果想包装一下，给别人用，那就得用泛型了，不然别人要是想用`map[int]`，那不就得开发一个
```go
func mapInt64ForEach(arr []int64, fn func(it int64) int) []int {
    var newArray = []int{}
    for _, it := range arr {
    	// We are executing the method passed
    	newArray = append(newArray, fn(it))
    }
    return newArray
}
```

## 采用高阶函数的递归
借用 fibonacci 的实现我们简单地示例返回一个函数的方式来实现递归：
```go
package main

import "fmt"

func fibonacci() func() int {
    a, b := 0, 1

    return func() int {
        a, b = b, a+b
        return a
    }
}

func main() {
    f := fibonacci()

    for i := 0; i < 10; i++ {
        fmt.Println(f())
    }
}

// 依次输出：1 1 2 3 5 8 13 21 34 55
```

## 延迟计算 Delayed Calculating
使用高阶/匿名函数的一个重要用途是捕俘变量和延迟计算，也即所谓的惰性计算（Lazy evaluations）

```go
func doSth(){
  var err error
  defer func(){
    if err != nil {
      println(err.Error())
    }
  }()
  
  // ...
  err = io.EOF
  return
}

doSth() // printed: EOF
```
在 `defer` 的高阶函数中，捕俘了外部作用域中的 `err` 变量，doSth 的整个运行周期中对 `err` 的设定，最终能够在 `defer` 函数体中被正确计算得到。如果没有捕俘和延迟计算机制的话，高阶函数体中对 `err` 的访问就只会得到 `nil` 值，因为这是捕俘时刻 `err` 的具体值。换句话说，在高阶函数中对外部作用域的访问是动态地延迟地计算的。

