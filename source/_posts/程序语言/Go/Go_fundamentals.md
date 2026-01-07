---
title: Go Programming Languages - Fundamentals
tags: [Go]
categories: Programming Languages
cover: images/ProgrammingLanguages/Go/cover.svg
excerpt: Go programming language is a grammar-sensitive language, meaning that the language attached great importance on grammar. Go supports gabbage collection. Go does not support inheritance and overloading, but supports interface. Go supports concurrency. Go does not support casting. Go does not support _assert (third-party packages available)_, Go does not support static variable.
---

## Table of Contents
<!-- toc -->
<!-- - [Overview](#overview)
- [Environment Variable](#environment-variable)
- [Basic Command](#basic-command)
- [Go Fundamentals](#go-fundamentals)
  - [Hello World](#hello-world)
  - [Input from stdin](#input-from-stdin)
  - [Variable](#variable)
    - [Initialization](#initialization)
  - [Type](#type)
      - [Basic Types](#basic-types)
  - [Constant](#constant)
    - [Initialization](#initialization-1)
  - [Condition Statement](#condition-statement)
    - [For loop](#for-loop)
  - [If/else](#ifelse)
  - [Switch](#switch)
  - [Array](#array)
    - [Initialzation](#initialzation)
    - [Slices](#slices)
      - [Difference between slice and array:](#difference-between-slice-and-array)
      - [Iterating over slice:](#iterating-over-slice)
  - [Functions in Go](#functions-in-go)
    - [Typical Function](#typical-function)
    - [Multiple Returns](#multiple-returns)
    - [Named Returns](#named-returns)
    - [Variadic Functions](#variadic-functions)
    - [Defer Keyword](#defer-keyword)
    - [Type Function and Returning Functions](#type-function-and-returning-functions)
    - [Receiver](#receiver)
  - [Pointers](#pointers)
  - [Pass by value & Pass by reference](#pass-by-value--pass-by-reference) -->
  

# Overview
The go programming language has the features in C, Java and Python. Go programming language is a grammar-sensitive language, meaning that the language attached great importance on grammar. Go supports gabbage collection. Go does not support inheritance and overloading, but supports interface. Go supports concurrency. Go does not support casting. Go does not support _assert (third-party packages available)_, Go does not support static variable.


# Environment Variable

1. `GOROOT`: the place where you install the Go
2. `GOPATH`: the place where you store the source code
   1. src: source code
   2. pkg: dependent package

3. 国内用户建议设置goproxy: export GOPROXY=https://goproxy.cn

# Basic Command
| **_command_** | **_content_** |
| --- | --- |
| bug | start a bug report |
| build | compile packages and dependencies |
| clean | remove object files and cached files |
| doc | show documentation for package or symbol |
| env | print Go environment information |
| fix | update packages to use new APIs |
| fmt | gofmt (reformat) package sources |
| generate | generate Go files by processing source |
| get | add dependencies to current module and install them |
| install | compile and install packages and dependencies |
| list | list packages or modules |
| mod | module maintenance |
| run | compile and run Go program |
| test | test packages (go test will scan for all files ending with *_test.go, so put test and src files together) |
| tool | run specified go tool |
| version | print Go version |
| vet | report likely mistakes in packages |

# Go Fundamentals
## Hello World
The first and foremost thing for a programming language is to understand the basic hello_world.go
```go
package main

import "fmt"

func main() {
    fmt.Println("hello world")
}
```
Three things to notice:

- `package main`indicates where the package is in, this is the entry point for go programs
   - There is only one main function in one package
- `import "fmt"`indicates importing a package
- `func main` like many other languages, the main function
   - However, the main does not need input arguments - like Python
   - uses `os.Args`to obtain arguments

## Input from stdin
```go
//take input like scanf in C
_, err = fmt.Scanf("%d %s", &x, &y)
fmt.Println("You Entered x:", x, " and y: ", y, " Error: ", err)

//take input like cin >> in c++
var x int = 1337
var y string = "string value"

_, err := fmt.Scan(&x, &y)
fmt.Println("You Entered x:", x, " and y: ", y, " Error: ", err)

//take input with white spaces
var z string = "string"

scanner := bufio.NewScanner(os.Stdin)
scanner.Scan()
z = scanner.Text()
fmt.Println("You Entered z:", z)
```

## Variable
### Initialization
There are two ways to define a variable

- Implicitly initialize
```go
var a int
// equivalent to var a int = 0
```

- Define initial value but without stating the variable type
```go
var a = 1
```

- Define initial value plus variable type
```go
var a int = 1
```

- (new) convenient way without var
```go
a := 1
// equivalent to var a := 1
```

- 使用`var()`集中定义变量
```go
var (
    a int
    b string
    c []float32
    d func() bool
    e struct {
        x int
    }
)
```

- 编译器智能决定变量
```go
// only can be used in a function
var a, b, i, s1, s2 = true, false, 3, "Hello", "World"
```

### Explicit conversion (强制类型转换)
- Go中，类型转换是强制的（没有隐式转换）
```go
var a, b int = 3, 4
// wrong, math.Sqrt returns float64
// also, a * a + b * b is int, not float64
var c int = math.Sqrt(a * a + b * b)
// correct, must explicitly convert
var c int = int(math.Sqrt(float64(a*a + b*b)))
```

## Type
### Basic Types

1. `bool`
2. `string`
3. `int`,`int8`,`int16`,`int32`,`int64`,`uint`,`uint8`,`uint16`,`uint32`,`uint64` 
4. `byte` // alias for `uint8`
5. `rune` // char in java, alias for `int32`
   - official：rune is an alias for int32 and is equivalent to int32 in all ways. It is used, by convention, to distinguish character values from integer values.
6. `float32`, `float64`
7. `complex64`, `complex128`

## Constant
### Initialization

1. Full version
```go
const a int = 1
```

2. Without stating the type
```go
const a = 1
```
In Go programming language, the initialization cannot be like the following:
```go
const a int // not legal
a = 1 // not legal
```

### Enum (No enum explicitly in Go)
```go
const (
  cpp = 1
  java = 2
  python = 3
  golang = 4
)
```

For simplicity, we can use `iota` keyword for auto increment
```go
const (
  cpp = iota
  java
  python
  golang
)
```

For more advanced usage:
```go
// e.g., we want to create b, kb, mb, gb, tb, pb...
const (
  b = 1 << (10 * iota)
  kb
  mb
  gb
  tb
  pb
)
```

## Condition Statement
### For loop
```go
package main

import "fmt"

func main() {
    
    for j:=1 ; j<10 ; j++ {
        fmt.Println("j")
    }

}
```
 here `j:=1`is the alias for `var int j = 1`
In java, the type can be written in the for loop condition
```java
for (int i=0; i<10; i++) {
	// statement here
}

```
For go, it does not allow type to be written in the condition, but you can use more stupid way of declaring the variable
```java
var j = 1
for j=1; j<10; j++ {
    // statement here
}
```
Also, for go, **there is no `**while**` statement**, meaning that if you want to do while statement, the way to do that is to limit the condition to just one:
```java
var j = 1
for j < 10 {
    //statement here
	j = j + 1
}
```
Others:
```java
var j = 1
// infinite loop
for {
    //statement here
	j = j + 1
    if j >= 10 {
        break
    }
}
        
```

For also allowed for three ways that substitute the `while`

1. ignore initial state
```go
func convertToBin(v int) string {
  result := ""
  for ; v > 0 ; v /= 2 {
    result = strconv.Itoa(v % 2) + result
  }
  return result
}
```

2. ignore initial and final state
```go
for scanner.Scan() {
  fmt.Println(scanner.Text())
}
```

3. ignore initial and final state, and update condition
```go
// equals to while (true)
for {
  fmt.Println("abc")
}
```

## If/else
Similar to most of the language, with a few exceptions:

1. Can exclude `()`, but cannot exclude `{}`, the left bracket `{` must be on the same line with `if` statement
2. **Go doesn't have Ternary Operator ( x < 0 ? A : B )**

```go
package main

import (
  "fmt"
  "io/iotuil"
)

func main() {
  const filename = "abc.txt"
  // normal way
  contents, err := ioutil.ReadFile(filename)
  if (err != nil) {
    fmt.Println(err)
  } else {
    fmt.Printf("%s\n", contents)
  }
}
```
Another way to write this would be:

```go
package main

import (
  "fmt"
  "io/ioutil"
)

func main() {
  const filename = "abc.txt"
  // equivalent to contents, err := ioutil.ReadFile(filename)
  // if (err != nil) {
  // ...
  if contents, err := ioutil.ReadFile(filename); err != nil {
    fmt.Println(err)
  } else {
    fmt.Printf("%s\n", contents)
  }
}
```

## Switch
Switch statements in go propgramming language doesn't require break; they will break by default, fallthrough keyword used to go to NEXT statement even if condition doesn't match, `fallthrough` is like a continue so statement that after it will also executed. however a workaround is to use `labels` and `goto`
```go
switch var1 {
    case var1:
    case var2:
        fallthrough
    case var3:
        f()
    default:
        ...
}
```
Example:
```go
func eval(a, b int, op string) {
  var result int
  switch op {
  case "+":
    result = a + b
  case "-":
    result = a - b
  case "*":
    result = a * b
  case "/":
    result = a / b
  default:
    panic("unsupported operator:" + op)
  }
  return result
}
```

Note:

1. Switch will automatically "break" (we do not need to write break)

2. `fallthrough` = continue running the following code without breaking

## Array

### Initialzation
ways of declaring initialization
```java
// first one
var a [3] int
var balance [10] float32
arr2 := [3]int{1, 3, 5}
arr3 := [...]int{2, 4, 6, 8, 10}

// two-dimension
var x [3][2] int
```

### iterate over array
```go
// first method
for i := 0; i<len(arr3); i++ {
  fmt.Println(arr3[i])
}

// second method: using range
for i := range arr3 {
  fmt.Println(arr3[i])
}

// if we want to directly access the value
for i, v := range arr3 {
  fmt.Println(i, v)
}
```

### Slices
Slices are of dynamic size
```go
letters := []string{"a", "b", "c", "d"}
    
/*  using make -> make([]T, len, cap) */
var s []byte
s = make([]byte, 5, 5)
//OR
s := make([]byte, 5)

// both equiavlent to: s == []byte{0, 0, 0, 0, 0}
```

- 左开右闭
- A slice does not store any data, it just describes a section of an underlying array. Changing the elements of a slice modifies the corresponding elements of its underlying array. **Other slices that share the same underlying array will see those changes**.
- Slicing a slice changes pointers of the underlying array, so it is as efficient as manipulating array indices, size and capacity of the new slice are changed too.
- Example usage:
```go
names := [4]string{"John","Paul","George","Ringo",}
fmt.Println(names)   //[John Paul George Ringo]
a := names[0:2]
b := names[1:3]
fmt.Println(a, b)    //[John Paul] [Paul George]
b[0] = "XXX"
fmt.Println(a, b)    //[John XXX] [XXX George] 
fmt.Println(names)   //[John XXX George Ringo]
```

#### Difference between slice and array
```go
//This is an array literal:
[3]bool{true, true, false}

//And this creates the same array as above, then builds a slice that references it:
[]bool{true, true, false}
```

#### Iterating over slice
```go
for i, v := range arr {	//do stuff }
for _, v := range arr {	//do stuff }
for i, _ := range arr {	//do stuff }
```

## Map
Map format: `map[k]v`

```go
m := map[string]string {
  "name": "ccmouse",
  "course": "golang",
  "site", "imooc",
  "quality": "notbad"
}
```

### Create a map
```go
// m2 == empty map
m2 := make(map[string]int)
// initialize the space
m2 := make(map[string]int, 5)
```

### Traversing the map (using range)
```go
for k, v := range m {
  fmt.Println(k, v)
}
```

### Get Element
Unlike Python when accessing non-exist key, it will pop exceptions, Go will not, and it will return two values on accessing the value through keys, 1) the value itself, and 2) a bool of whether the element exists
```go
// ok will be whether the key exists
courseName, ok := m1["course"]

if courseName, ok := m1["course"]; ok {
  fmt.Println("causeName")
} else {
  fmt.Println("key does not exist")
}
```

### Delete Element
The delete() function is a direct and effective way to eliminate a key−value pair from a map. By specifying the key to delete, you can promptly remove the corresponding data from the map.
```go
//  delete(mapName, key)
name, ok := m["name"]
delete(m, "name")
```

### Map's Key

- Map uses hashmap as its data structure, so other than **slice**, **map**, **function**, other type can be used as keys.
- Sturct data without **slice**, **map** and **function** can also be the key

## Functions in Go

### Typical Function
```go
  // return void
  func add(x int, y int)  {
    fmt.Println("Hello, World!")
  }

  //-------arguments-----return------
  func add(x int, y int)  int {
    return x + y
  }

  //-----same type arguments-----------
  func add(x, y int)  int {
    return x + y
  }
```

### Multiple Returns
```go
func swap(x, y string) (string, string) {
    return y, x
}

//in main
func main() {
	a, b := swap("hello", "world")
	fmt.Println(a, b) //prints "world hello"
}
```

### Named Returns
You can declare return variables and name them at the beginning, they are returned in the end.
```go
//Returns x,y at the end.
func split(sum int) (x, y int) {
    x = sum * 4 / 9
    y = sum - x
    // by default, it returns x and y
    return
    //return a,b  <- u can override default return of x,y.
}
```

### Variadic Functions
The rightmost argument can be a list of variable size (_slice_) of data.
`...` three dots means you are passing a variable size of data
```go
func average(x int, values ...int) float64 {
    //print values
    fmt.Println("Single argument value: ", x)
    fmt.Println("Variable argument values: ", values)

    //calculate average
    total := 0
    for _, value := range values {
      total += value
    }

    return float64(total) / float64(len(values))
}

func main() {
    avg := average(10,20,30,40,50)
    println("Average:", avg)
}
```

### Defer Keyword
`Defer` used before a functions executes the function at the end of the scope of it.

- usually used to close opened files/buffers so you open the file and closes it using defer in the next line to keep things clean. 
- they're executed as a **stack**.
```go
fmt.Println("One")
defer fmt.Println("Four")
defer fmt.Println("Three")
fmt.Println("Two")

//Prints One Two Three Four
```

### Type Function and Returning Functions

1. Functions can be assigned to variables `func0 := func() int {x++; return x}`
2. Functions that are returned from another functions has its own scope per returned function
```go
package main

func incrementGlobalX() int  {
  x++
  return x
}

func wrapper() func() int {
  x := 0
  return func() int {
    x++
    return x
  }
}

func main() {
    fn := wrapper()
	fmt.Print(wrapper())
    fmt.Print(wrapper())
    // print 12
}
```
```go
package main

var x = 0

func main() {
  //local x
  x := 0

  func0 := func() int {x++; return x}
  func1 := incrementGlobalX //without ()
  func2 := wrapper()
  func3 := wrapper()

  println(func0(), " : func0 (local x)")
  println(func1(), " : func1 (global x)")
  println(func2(), " : func2 (per func scope x1)")
  println(func3(), " : func3 (per func scope x2)")
  println("Second Increment")
  println(func0(), " : func0 (local x)")
  println(func1(), " : func1 (global x)")
  println(func2(), " : func2 (per func scope x1)")
  println(func3(), " : func3 (per func scope x2)")
}

func incrementGlobalX() int  {
  x++
  return x
}

func wrapper() func() int {
  x := 0
  return func() int {
    x++
    return x
  }
}
```

### Receiver
Receiver is the way you create a method for a specific type/struct
```go
type rect struct {
    width, height int
}

// (r *rect) is called rceiver
func (r *rect) area() int {
    return r.width * r.height
}

r := rect{2,3}
areaX := r.area()
fmt.Println(areaX)
```

## Pointers
Similar to C/C++
```go
var value int = 1000
var pointer *int = &value
println(value)                	//1000
println(pointer)              	//0xfffffffff
println(*pointer)             	//1000
(*pointer)++		  			//1001
*pointer = *pointer + 10	    //1011
println(*pointer)				//1011
println(*pointer + *pointer)  	//1011 + 1011 = 2022
```

Difference?

1. Pointer cannot do arithmetic calculation: you can change what pointer points to, but you cannot change the pointer itself

## Pass by value & Pass by reference

1. Every thing is passed by value except **arrays, slices, maps and channels** which some calls r**eference types**, these types are passed by reference ( they internally have pointers, so no copying of the actual data happens when passing them) . 
2. Unlike C, where the address of a local variable will be recollected after the function returns, it's perfectly OK to return the address of a local variable (like Java); the storage associated with the variable survives after the function returns.

## Reference
1. https://github.com/sherifabdlnaby/Golang-study-notes
2. Coursera - Functions, Methods, and Interfaces in Go, University of California, Irvine.
3. 慕课网 Google资深工程师深度讲解Go语言 由浅入深掌握Go语言
