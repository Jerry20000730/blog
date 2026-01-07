---
title: C# 基础 - 语法
tags: [C#]
categories: Programming Languages
cover: images/ProgrammingLanguages/CSharp/cover.svg
excerpt: C#基本语法和API
---

<!-- toc -->

# Using system & Console & hello world
system是c#程序最基本的模块，这个模块可以提供各种各样已经提前定义好的功能
```csharp
using System;

// namespace是当别人引用我们的代码的时候
// 可以使用 using HelloWorld
namespace HelloWorld
{
    class Program
    {
        static void Main(string[] args)
        {
            Console.WriteLine("Hello World!");
        }
    }
}
```

# Console工具类中常见方法

- Write(): 括号内参数显示在命令行中
- WriteLine(): 括号内参数显示在命令行中，显示完成后换行
- Read(): 只读取第一次按下键盘的按钮（包含字符，ascii代码等本身所有信息）
- ReadKey(): 只读取第一次按下键盘的按钮（ascii代码）
- ReadLine(): 读取用户输入，直到按下回车
- Clear(): 命令行所有内容清除

# 列表，index索引，范围
```csharp
string[] words = new string[]
{
    // index from start index from end
	 "The",  	// 0     ^9
	 "quick", 	// 1     ^8
	 "brown", 	// 2     ^7
	 "fox",  	// 3     ^6
	 "jumped", 	// 4     ^5
	 "over",  	// 5     ^4
	 "the",  	// 6     ^3
	 "lazy",  	// 7     ^2
	 "dog"  	// 8     ^1
};    			// 9 (or words.Length) ^0

// 第二个元素
Console.WriteLine(words[1]);
// 倒数第一个元素
Console.WriteLine(words[words.Length-1]);
// 倒数第一个元素(2)
Console.WriteLine(words[^1]);

// 范围索引，左闭右开
var list = words[0..3];
```
其中C#有两个类型专门用来储存index索引与范围：`Index`和`Range`

在C# 7出现`Span`它可以表示另一个数据结构里连续相邻的一串数据，并且它是内存安全的。C# 8 里面出现`Range`和`Index`, 还可以组合使用`Range`和`Index`

```csharp
string[] words = new string[]
{
    // index from start index from end
	 "The",  	// 0     ^9
	 "quick", 	// 1     ^8
	 "brown", 	// 2     ^7
	 "fox",  	// 3     ^6
	 "jumped", 	// 4     ^5
	 "over",  	// 5     ^4
	 "the",  	// 6     ^3
	 "lazy",  	// 7     ^2
	 "dog"  	// 8     ^1
};

Range range = 1..4;
Index index = ^1;
Range complexRange = 1..^1;
var newList1 = words[range];
var newList2 = words[index];
var newList3 = words[complexRange];
```

# 逻辑判断（基本与Java一致，不赘述）

## if - else
```csharp
// 基本与java一致
if（weather == good）{
    goSwimming();
} else {
	notGoSwimming();
}
```

## switch
```csharp
switch(expression){

    case  constant-expression :
        statement(s);
        break;
    case  constant-expression :
        statement(s);
        break;
	/* 可以有任意数量的 case 语句 */
    default : /* 可选的 */
        statement(s);
        break;
}
```

## ？:操作符
```csharp
condition ? consequent : alternative
```

# 循环（基本与Java一致，不赘述）

## for循环
```csharp
for (int counter = 0; counter < 10; counter = counter + 1) {
    Console.WriteLine("counter: " + counter);
}
```

## while循环
```csharp
int couter = 0
while（conter<5）{
    counter = counter + 1;
}
```

## do while循环
```csharp
int couter = 0
do {
   counter++; 
} while(counter < 5)
```

# 方法（Method）
## 方法定义（与Java有几处略有不同，不同之处红色字体标出）
```csharp
// Access Specifier: 访问修饰符
// Modifier: 声明修饰符
// Return Type: 返回类型
// Method Name: 方法名
// Parameter List: 参数列表
// Method Body: 方法主体
<Access Specifier> <Modifier> <Return Type> <Method Name> (Parameter List){
	Method Body
}
```

### 访问修饰符（Access Specifier）
定义了方法的访问属性，我们可以选择这个列表中的任意一个来描述方法的可见性<br />public: 公有方法，可以被外部调用<br />private: 私有，方法会被隐藏起来，其他class是不能调用的<br />Protected: 只能在它的类本身或者它的派生类中访问<br />Internal: 内部方法，同一个程序集（同名.cs文件）中的所有类都可以访问

### 声明修饰符 (modifier)
C#中共有8个声明修饰符：

- 静态类型（static）
- 抽象类型（abstract）
- 派生类重写的虚函数（virtual）
- 允许方法继承后重写的override（与Java写的位置不同）
- 隐藏基类成员继承的new
- 表示不能被继承的sealed
- 允许在同一个程序集分散定义的partial
- 用于声明外部实现的Extern

## 方法表达式
除了普通的、使用花括号的方法定义方式，他还给我们提供了一个更加简单的语法结构，这种语法结构就是箭头表达式<br />方法的签名保持不变，变化的是方法的主题部分
```csharp
public static int FindMax (int num1, int num2) => num1 > num2 ? Num1 : num2
```

## 值传参与引用传参
值传参不必多说
```csharp
// x 和 y 不会交换 => 值传参
public void swap(int x, int y)
{
	int temp;         
	temp = x; /* 保存 x 的值 */
	x = y;    /* 把 y 赋值给 x */
	y = temp; /* 把 temp 赋值给 y */
}
```

### 按引用传递参数
swap方法的两个参数前面加上关键词ref，实现引用传参
```csharp
// x 和 y 会交换 => 引用传参传参
public void swap(ref int x, ref int y)
{
	int temp;         
	temp = x; /* 保存 x 的值 */
	x = y;    /* 把 y 赋值给 x */
	y = temp; /* 把 temp 赋值给 y */
}
```

### 按输出传递参数
```csharp
public void getValue(out int x)
{
	int temp = 5;
	x = temp;
}

getValue(a);
```
这时候如果我们在调用getValue方法的同时，也加上out关键词，那么这个时候方法中x的数据就会突破限制，直接向参数a输出