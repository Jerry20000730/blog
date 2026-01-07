---
title: Go Programming Languages - Goroutines
tags: [Go]
categories: Programming Languages
cover: images/ProgrammingLanguages/Go/cover.svg
excerpt: A goroutine is a lightweight thread managed by Go runtime
---

## How to write Goroutine
任何函数只需加上`go`就能送给调度器运行，不需要像python,js一样区分是否为异步函数。

Very basic Goroutine usage:
```go
func main() {
    for i:=0; i<1000; i++ {
        go func(i int) {
            for {
                fmt.Printf("hello from goroutine %d\n", i)
            }
        }(i)
    }
    time.Sleep(time.Millisecond)
}
```

注意 不能这么写
```go
func main() {
    for i:=0; i<1000; i++ {
        go func() {
            for {
                fmt.Printf("hello from goroutine %d\n", i)
            }
        }
    }
    time.Sleep(time.Millisecond)
}
```
因为循环变量并不支持被延迟/动态计算

## What is Co-routine (协程)

- lightweight thread (轻量级线程)
- 编译器/解释器/虚拟机层面的多任务
- 多个协程可能在一个或多个线程上运行
- 子程序（Subroutines）是协程的特例，协程更加广泛

## Goroutine可能得切换点

- I/O, select
- channel
- 等待锁
- `runtime.Gosched()`