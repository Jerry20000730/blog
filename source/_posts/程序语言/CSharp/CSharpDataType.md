---
title: C# 基础 - 内置类型
tags: [C#]
categories: Programming Languages
cover: images/ProgrammingLanguages/CSharp/cover.svg
excerpt: C#基本数据类型和内置引用类型
---

### 基本数据类型
| 类型 | 含义 | 取值范围 | 精度 |
| --- | --- | --- | --- |
| sbyte | 有符号8位整数 | -128到127 | 8位 |
| byte | 无符号8位整数 | 0到255 | 8位 |
| short | 有符号16位整数 | -32768到32767 | 16 |
| ushort | 无符号16位整数 | 0到65535 | 16 |
| int | 有符号32位整数 | -2147483648到2147483647 | 32 |
| uint | 无符号32位整数 | 0到4294967295 | 32 |
| long | 有符号64位整数 | -9223372036854775808到9223372036854775807 | 64 |
| ulong | 无符号64位整数 | 0到18446744073709551615 | 64 |
| float | 单精度32位浮点值 | -3.4 x 1038 到 +3.4 x 1038 | 32 |
| double | 双精度64位浮点值 |  | 64 |
| decimal | 表示128位数据类型 | ( 7.9 x 1028 - 7.9 x 1028) / (100 - 28) | 128 |
| bool | 表示一个布尔值 | true / false, 1 byte | 8 bit |
| char | 表示一个UTF-16字符 | U+0000 到 U+FFF | 16位 |

### 内置类型
#### 内置值类型

| c#类型关键字 | .NET类型 |
| --- | --- |
| bool | System.Boolean |
| byte | System.Byte |
| sbyte | System.SByte |
| char | System.Char |
| decimal | System.Decimal |
| double | System.Double |
| float | System.Single |
| int  | System.Int32 |
| uint | System.UInt32 |
| nint | System.IntPtr |
| nuint | System.UIntPtr |
| long | System.Int64 |
| ulong | System.UInt64 |
| short | System.Int16 |
| ushort | System.UInt16 |

#### 内置引用类型
| c#类型关键字 | .NET类型 |
| --- | --- |
| object | System.Object |
| string | System.String |
| dynamic | System.Object |
<!--more-->