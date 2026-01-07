---
title: Go Programming Languages - OOP
tags: [Go]
categories: Programming Languages
cover: images/ProgrammingLanguages/Go/cover.svg
excerpt: Go supports OOP (object oriented programming)
---

# Go's support about OOP

Go only supports **encapsulation**, does not support **inheritance** and **polymorphism**. Go only has struct, does not has class (like C programming language)

## struct: how to create an instance

```go
type treeNode struct {
    value int
    left, right *treeNode
}

func main() {
    var root treeNode

    root := treeNode{value: 3}
    root.left = &treeNode{}
    root.right = &treeNode{5, nil, nil}
    // unlike C++, we do not need `->`
    root.right.left = new(treeNode)

    nodes = []treeNode {
        {},
        {5, nil, nil},
        {6, nil, &root},
    }
}
```

## factory function (a difference between Go and C++)

```go
// we do not need to care
// if treeNode here is a temporary variable
// or variable on the stack
func createNode(value int) *treeNode {
    return &treeNode{value: value}
}

func main() {
    root.left.right = createNode(3)
}
```

Question: was the variable created on stack or heap (a question that is important in C++ languages)
Answer: not important! The compiler will decide if it is allocated on stack or heap

## Define methods for struct
```go
// the parenthesis after func is called `receiver`
func (node treeNode) print() {
    fmt.Print(node.value)
} 

func (node *treeNode) setValue(value int) {
    node.value = value
}

// if we want to use
node.print()
node.setValue()
```
The difference is, no matther what you decide: treeNode or treeNode *, both of them can be used in struct methods as needed. For example, even if you input an address of treenode:

```go
func (node treeNode) print() {
    fmt.Print(node.value)
}

pnode := &root
pnode.print()
```

This will also print root value because even if we pass an address, it will convert to a copy of the tree and then print it. The compiler is very smart and can convert according to the definition of the function.

The difference between `treeNode` and `treeNode *` is that the `treeNode` does not allow changing the object (it creates a copy), while `treeNode *` can change the value of the object.

