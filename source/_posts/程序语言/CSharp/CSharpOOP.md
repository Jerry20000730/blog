---
title: C# 基础 - 面向对象
tags: [C#]
categories: Programming Languages
cover: images/ProgrammingLanguages/CSharp/cover.svg
excerpt: C#面向对象概念
---

<!-- toc -->

# 面向对象（object-oriented programming)
class高内聚、低耦和：功能相关事物应该放在同一个集合中形成一个模块，这就叫做高内聚，而这些模块又应该是相互独立的，不同的模块之间应该保持一个低耦合的状态。

## 构造方法（constructor）和方法重载（Method Overloading）
```csharp
访问修饰符 class名() 
{
	代码逻辑，用于初始化数据等
}
```
```csharp
public Point(int x, int y)
{
    this.x = x;
    this.y = y;
}
```
```csharp
public Point()
{
    x = 15;
    y = 5;
}

public Point(int x, int y)
{
    this.x = x;
    this.y = y;
}
```

## C#严格界定字段（Fields）、属性（Properties）
在Java里，一般对于Private我会用Idea自带的重构自己生成getter函数和setter函数，这些函数属于成员函数，在C#里这些被定义为属性（Properties）<br />属性：[https://docs.microsoft.com/zh-cn/dotnet/csharp/programming-guide/classes-and-structs/properties](https://docs.microsoft.com/zh-cn/dotnet/csharp/programming-guide/classes-and-structs/properties)

### 属性定义的基本模式
```csharp
public class TimePeriod
{
    private double _seconds;

    public double Hours
    {
        get { return _seconds / 3600; }
        set
        {
            if (value < 0 || value > 24)
                throw new ArgumentOutOfRangeException(nameof(value),
                      "The valid range is between 0 and 24.");

            _seconds = value * 3600;
        }
    }
}
```
`value`关键字用于定义由 `set` 或 `init` 访问器分配的值，`TimePeriod`类表示时间间隔。 在内部，该类将时间间隔以秒为单位存储在名为 `_seconds` 的私有字段中。 名为 `Hours`的读-写属性允许客户以小时为单位指定时间间隔。`get` 和 `set`访问器都会执行小时与秒之间的必要转换。 此外，`set`访问器还会验证数据，如果小时数无效，则引发ArgumentOutOfRangeException<br />在C#中，定义字段会使用小写，private的话加一个下划线（如上面的`_seconds`），属性会使用大写（如上面的`Hours`）

### 表达式主体定义
属性访问器通常由单行语句组成，这些语句只分配或只返回表达式的结果。 可以将这些属性作为 expression-bodied 成员来实现。 => 符号后跟用于为属性赋值或从属性中检索值的表达式，即组成了表达式主体定义。
```csharp
public class SaleItem
{
    string _name;
    decimal _cost;

    public SaleItem(string name, decimal cost)
    {
        _name = name;
        _cost = cost;
    }

    public string Name
    {
        get => _name;
        set => _name = value;
    }

    public decimal Price
    {
        get => _cost;
        set => _cost = value;
    }
}
```

### 7.2.3 自动实现属性
通过使用自动实现的属性，既能简化代码，还能让 C# 编译器透明地提供支持字段
```csharp
public class SaleItem
{
    // 在这里编译器会自动创建一个private变量_name
    public string Name
    { get; set; }

    public decimal Price
    { get; set; }
}
```

## const，readonly和writeonly
从`getter`、`setter`的角度来说来说，`readonly`就是只有`getter`的属性，而`writeonly`就是只有`setter`的属性
```csharp
// readonly
// 初始化可以在构造器，也可以在下面的属性
public int T { get; } = 5;

// writeonly
private int _s;
public int S { set {_s = value;} }
```
对于变量和字段，c#还提供了两个更加底层，更加接近编译级别的语法结构，就是使用 const 或者 readonly这两个声明修饰符。
```csharp
public  const  int  a = 99;
public readonly int b;

```
其中：const是常量，readonly是变量。也就是说const必须在声明同时赋值，而readonly可以在声明暂时不赋值，当class初始化的时候再进行赋值。const是在代码编译前就能定下来，所以他性能比readonly要高出不少。

## 对象索引（普通索引[点击此处](#SDDo8)）
```csharp
public class Point
{
    // 成员变量x轴，使用属性
    private int _x;
    public void SetX(int value); ...
    public void GetX() => this.x;
    // 成员变量y轴
    private int y;
    public void SetY(int value); ...
    public void GetY() => this.y;
    // 成员变量z轴
    public int Z { get; set; }
    // 时间维度
    public int T { get; }
    // 第五维度
    private int _s;
    public int S { set { _s = value; } }
    // 第六维度
    const int a = 99;
    // 第七维度
    public readonly int b;

    // 加入第八维度数据
    // 第八维，字符串数组
	private string[] gamma = new string[]
    {
        // index from start index from end
        "The",  // 0     ^9
        "quick", // 1     ^8
        "brown", // 2     ^7
        "fox",  // 3     ^6
        "jumped", // 4     ^5
        "over",  // 5     ^4
        "the",  // 6     ^3
        "lazy",  // 7     ^2
        "dog"  // 8     ^1
    };    // 9 (or words.Length) ^0

    // 对于这些数据，我们可以通过给他们建立class级别的索引进行快速访问。
    // 建立类级别的索引需要使用关键词this，加上他的index信息，
    // index信息为整数，当然也可以是string等其他类型
    // 因为接下来我们需要从外部访问，所以声明为public；
    // 而每个索引所对应的数据是字符串类型，所以索引返回类型为string。
    public string this[int index]
    {
        get
        {
            return gamma[index];
        }
        set
        {
            gamma[index] = value;
        }
    }

    public Point()
    {
        this._x = 15;
        y = 10;
        T = 5;
        a = 15;
        b = 1000;
    }
}
```
此时进行索引
```csharp
// 获取point数组第一个，只是此时是成员变量
Console.WriteLine(point[0]);
// 修改point数组第一个
point[0] = "hello";
```
类索引Index的语法结构<br />![image.png](https://cdn.nlark.com/yuque/0/2023/png/2434979/1697550613539-7de6558a-e599-4b0d-8873-659352acd889.png#averageHue=%23fefdfd&clientId=u0a656533-1d11-4&from=paste&height=315&id=u84f94525&originHeight=377&originWidth=669&originalType=binary&ratio=1.1979166269302368&rotation=0&showTitle=false&size=70064&status=done&style=none&taskId=uaf2bf636-915a-4fda-8e43-7502c6951ee&title=&width=558.4695837425426)<br />注意点：

- 创建索引需要使用this关键词
- 索引可以用于快速访问一组数据的某一项
- 索引的使用需要通过方括号
- 索引不能使用static、ref和out来修饰

### 索引重载
```csharp
public int this[string name]
{
	get
	{
		return Array.IndexOf(_g, name);
	}
}
```
## Partial类
C#中有一个别致的功能叫做`partial`，它可以用来定义`局部类`或者`局部方法`。通过局部类型可以实现将一个类、结构、接口甚至是方法分成几个独立的代码片段，或者放在几个不同的.cs文件中，编译器在编译的时候，会找出这些独立的片段，并在编译的最后合并。`partial`类能让类变得更容易管理，有效降低我们单个文件的代码复杂性，让代码更容易被维护<br />参考[上面代码](https://www.yuque.com/jerry-rxyaq/pxp1nr/feex8c?inner=bTeFd)，这个class目前有九个成员变量，再加会显得非常的繁琐，使用partial可以解决这个问题
```csharp
public partial class Point
{
    public int D { get; set; }

    public void printDelta()
    {
        Console.WriteLine("我是第九维");
    }
}
```
 这样在main方法中访问point的`printDelta`这个方法了（这么神奇？）
```csharp
point.D = 1;
point.printDelta();
```

### 分开两个文件
局部类的好处就是能把一个类卸载两个独立的文件中，这样更加翻盖你我们对代码的管理<br />命名时，还是以class的类名称开头，但是我们会给文件中加一个中间名称，方便与主文件区分，`Point.Delta.cs`

### 使用范围

1. 类型特别大，不适合放在一个文件中实现
2. 一个类型中的一部分代码为自动化工具生成的代码，不宜与我们自己编写的代码混合在一起
3. 一个类需要多个人同时编写的时候

### 注意事项

1. 只适用于类、接口、结构，不支持委托和枚举（partial只有和class、struct、interface放在一起时才表示局部类型）
2. 同一个类型的各个部分必须有修饰符partial
3. 使用局部类型时，一个类型的各个部分必须位于相同的命名空间中
4. 一个类型的各个部分必须同时被编译
5. 一个类型的各部分的访问修饰符必须保证一致性
6. 局部类型具有累加效应

## 松耦合设计思想
### 耦合 Coupling
![](https://cdn.nlark.com/yuque/0/2023/png/2434979/1697553596378-99b86590-843a-4aa7-84f3-5a0906ac7899.png#averageHue=%23fafafa&clientId=u0def8650-6ca1-4&from=paste&id=ucd05e4b0&originHeight=924&originWidth=2396&originalType=url&ratio=1.1979166269302368&rotation=0&showTitle=false&status=done&style=none&taskId=u2c769bd0-20ba-49ea-8411-40c204d7be3&title=)<br />随着系统的复杂性增加，修改最底层的class A会引发系统级别的代码调整，这种代码的处理方式就叫做高耦合。所有的class都紧密的结合在一个类，牵一发则动全身

#### 低耦合/松耦合
类之间存在关系，但是独立存在，两者之间为引用关系。

## UML类关系 - 依赖、关联、聚合、组合、泛化

### 依赖（Dependency）
依赖关系用虚线加箭头表示，类A要完成某项功能引用了类B，则类A依赖类B。依赖在代码中主要体现为类A的某个成员函数的返回值，形参，局部变量或静态方法调用<br />![依赖](https://cdn.nlark.com/yuque/0/2023/png/2434979/1697554084155-7b3db16f-df2c-4eb2-a230-78b48758d105.png#averageHue=%23e6fdf0&clientId=u0def8650-6ca1-4&from=paste&id=ucde7754a&originHeight=54&originWidth=290&originalType=url&ratio=1.1979166269302368&rotation=0&showTitle=true&status=done&style=none&taskId=u466bb816-beb7-4de0-8c90-339dde40c70&title=%E4%BE%9D%E8%B5%96 "依赖")
```csharp
public class Computer {
	public static void start(){
        Console.WirteLine("电脑正在启动");
	} 
};

public class Student {
	//返回值构成依赖
	public Computer program();
	
    //形参构成依赖
	public void program(Computer computer);

	public void playGame() {
        //局部变量构成依赖
		Computer computer = new Computer();
		...
		//静态方法调用构成依赖
		Computer.star();
	}
};
```

### 关联（Association）
关联关系使用实线加箭头表示，类之间的关系比依赖要强。学生与老师是关联的，学生可以不用电脑，但是学生不能没有老师。如下图所示：<br />发生关联关系的两个类，类A成为类B的属性，而属性是一种更为紧密的耦合，更为长久的持有关系。 在代码中的表现如下：
```csharp
public class Teacher {
    ...
};

public class Student {
    public Teacher teacher;  //成员变量
    public void study();
};
```
与依赖的区别：

1. 发生依赖关系的两个类都不会增加属性。其中的一个类作为另一个类的方法的参数或者返回值，或者是某个方法的变量而已。
2. 从关系的生命周期来看，依赖关系是仅当类的方法被调用时而产生，伴随着方法的结束而结束。关联关系当类实例化的时候产生，当类对象销毁的时候关系结束。相比依赖，关联关系的生存期更长

![单项关联](https://cdn.nlark.com/yuque/0/2023/png/2434979/1697554245758-28d486b3-3561-4849-8f80-487d432576d8.png#averageHue=%23e6fdf0&clientId=u0def8650-6ca1-4&from=paste&id=u25cc0f03&originHeight=54&originWidth=290&originalType=url&ratio=1.1979166269302368&rotation=0&showTitle=true&status=done&style=none&taskId=uff453855-52f6-4c2a-831d-373aae1fc79&title=%E5%8D%95%E9%A1%B9%E5%85%B3%E8%81%94 "单项关联")<br />![多项关联](https://cdn.nlark.com/yuque/0/2023/png/2434979/1697554266110-51a5c7a1-59ea-4a88-a9f5-cb170068ab8e.png#averageHue=%23e6fdf0&clientId=u0def8650-6ca1-4&from=paste&id=u71a1daa5&originHeight=54&originWidth=290&originalType=url&ratio=1.1979166269302368&rotation=0&showTitle=true&status=done&style=none&taskId=uaf44972f-97ec-4e66-b407-e9c972e7011&title=%E5%A4%9A%E9%A1%B9%E5%85%B3%E8%81%94 "多项关联")<br />![自身关联](https://cdn.nlark.com/yuque/0/2023/png/2434979/1697554285083-9debce1a-1bc8-4e8b-8866-93eab63e8628.png#averageHue=%23fffff2&clientId=u0def8650-6ca1-4&from=paste&id=ue2fac199&originHeight=74&originWidth=140&originalType=url&ratio=1.1979166269302368&rotation=0&showTitle=true&status=done&style=none&taskId=uab126df2-de62-4b33-9b52-1c577ff636e&title=%E8%87%AA%E8%BA%AB%E5%85%B3%E8%81%94 "自身关联")<br />![多维关联](https://cdn.nlark.com/yuque/0/2023/png/2434979/1697554299319-991b8f78-2e2e-4303-8b78-3ad7f06590ec.png#averageHue=%23fffaef&clientId=u0def8650-6ca1-4&from=paste&id=u5db61b16&originHeight=148&originWidth=328&originalType=url&ratio=1.1979166269302368&rotation=0&showTitle=true&status=done&style=none&taskId=u418bfd36-f021-4e76-a99b-e46d2125e29&title=%E5%A4%9A%E7%BB%B4%E5%85%B3%E8%81%94 "多维关联")

### 聚合（Aggregation）
聚合关系使用实线加空心菱形，用来表示集体与个体之间的关联关系。例如班级与学生之间存在聚合关系，类图表示如下：<br />![聚合](https://cdn.nlark.com/yuque/0/2023/png/2434979/1697554643618-475d0b7c-cff0-4f54-968b-3efbfcd1454a.png#averageHue=%23d1e2f2&clientId=u0def8650-6ca1-4&from=paste&id=uf4e72930&originHeight=93&originWidth=330&originalType=url&ratio=1.1979166269302368&rotation=0&showTitle=true&status=done&style=none&taskId=u697fb082-ac40-487d-8b06-06066403fbc&title=%E8%81%9A%E5%90%88 "聚合")<br />聚合关系在代码上与关联关系表现一致。
```csharp
public class Student {
    ...
};

public class Class {
    private Student _student;  //成员变量
    public Class(Student student) {
        _student = student
    }
};
```
### 组合/复合 Composition
复合关系使用实线加实心菱形表示。用来表示个体与组成部分之间的关联关系。例如学生与心脏之间存在复合关系，类图表示如下：<br />![复合](https://cdn.nlark.com/yuque/0/2023/png/2434979/1697554814646-75243e85-62ae-4ef2-891d-1c7bb6a46a16.png#averageHue=%23d3e4f3&clientId=u0def8650-6ca1-4&from=paste&id=ua0e13d40&originHeight=97&originWidth=338&originalType=url&ratio=1.1979166269302368&rotation=0&showTitle=true&status=done&style=none&taskId=u686acf88-1f71-46e5-9f40-e34527f9164&title=%E5%A4%8D%E5%90%88 "复合")<br />组合关系在代码上与关联关系表现一致。
```csharp
public class Heart {
    ...
};

public class Student {
    private StudeHeartnt _heart;  //成员变量
    public Student() {
        _heart = new Heart（）;
    }
};
```
### 聚合（Aggregation）v.s. 组合（Composition）

1. 聚合关系没有组合紧密。<br />学生不会因为班级的解散而无法存在，聚合关系的类具有不同的生命周期；而学生如果没有心脏将无法存活，组合关系的类具有相同的生命周期。
> 这个从构造函数可以看出。聚合类的构造函数中包含另一个类的实例作为参数，因为构造函数中传递另一个类的实例，因此学生可以脱离班级体独立存在。组合类的构造函数包含另一个类的实例化。因为在构造函数中进行实例化，因此两者紧密耦合在一起，同生同灭，学生不能脱离心脏而存在。

2. 信息的封装性不同
- 在聚合关系中，客户端可以同时了解Classes类和Student类，因为他们是独立的。
- 在组合关系中，客户端只认识Student类，不知道Heart类的存在，因为心脏类被严密地封装在学生类中

**理解聚合与复合的区别，主要在于聚合的成员可独立，复合的成员必须依赖于整体才有意义。**

### 泛化 (Generalization)
泛化指的是类与类之间的继承关系和类与接口之间的实现关系。<br />![继承](https://cdn.nlark.com/yuque/0/2023/png/2434979/1697555254853-865923ff-6c08-498b-b72a-7f0b284945b2.png#averageHue=%23fefefe&clientId=u0def8650-6ca1-4&from=paste&id=ue9dada77&originHeight=189&originWidth=361&originalType=url&ratio=1.1979166269302368&rotation=0&showTitle=true&status=done&style=none&taskId=u80ed295c-dbb3-4f0b-9624-d067c1baad8&title=%E7%BB%A7%E6%89%BF "继承")<br />![接口](https://cdn.nlark.com/yuque/0/2023/png/2434979/1697555277159-3faff9ba-cc6c-4416-ae4d-2a1117a1bd0f.png#averageHue=%23cfe1f1&clientId=u0def8650-6ca1-4&from=paste&id=ua4cfc590&originHeight=226&originWidth=352&originalType=url&ratio=1.1979166269302368&rotation=0&showTitle=true&status=done&style=none&taskId=u06e8fa16-e357-449f-ae59-89170abf271&title=%E6%8E%A5%E5%8F%A3 "接口")

## 继承
`is-a`关系<br />C#中继承关系使用`:`
```csharp
public  class  Text: PresentationObject
{
    public  int  FontSize { get; set; }
    public  string  FontName { get; set; }
    public  void  AddHyperLink()
    {
        Console.WriteLine("添加超链接");
    }
}
```

## 复合
`has-a`关系
```csharp
public  class  Logger
{
    public  void  Log(string  message) 
    {
        Console.WriteLine($"日志 {DateTime.Now}: {message}");
	}
}
```
```csharp
public  class  DbMigrator
{
	private  readonly  Logger  _logger;
    public  DbMigrator(Logger  logger)
    {
        _logger = logger;
    }

	public  void  Migrate()
    {
		_logger.Log("数据迁移开始");
		// TODO: 处理数据迁移
    }
}
```
```csharp
public  class  Install
{
	private  readonly  Logger  logger;
    public  Install(Logger  logger)
    {
        this.logger = logger;
    }

	public  void  install()
    {
		_logger.Log("安装开始");
		// TODO: 安装过程省略
    }
}
```
最后在main函数里面调用
```csharp
var  logger = new  Logger();

var  dbMigrater = new  DbMigrator(logger);

var  installer = new  Installer(logger);

dbMigrater.Migrate();
```
## 访问修饰符（protected与internal）

### protected
只要是被protected声明的变量或者方法，只能被自己访问、或者被继承于自己的子类访问，其他一切外部操作都无法访问。那么接下来，我们用代码尝试一下吧。
```csharp
public class Car
{
    public void Accelerate()
    {
        Console.WriteLine("加油");
    }

    protected void Stop()
    {
        Console.WriteLine("制动");
    }
}

public class WulinHongguang : Car
{
    public void Drift()
    {
        this.Accelerate();
        this.Stop();
    }
}
```
如果Stop时private的话就无法访问了

### internal
使用了 internal 修饰符以后，相关的代码只能在同一个程序集或者说同一个项目中使用，任何一个外部项目都没办法从外部对它进行访问。

## 构造函数的继承
```csharp
public class Staff
{
    public Staff()
    {
        Console.WriteLine("员工类初始化");
    }
}

public class Manager : Staff
{
    public Manager()
    {
        Console.WriteLine("经理类初始化");
    }
}

```
命令行中，我们得到了两行输出，“xxx”。输出结果表明，当我们在创建manager对象的时候，作为派生类，他不仅了调用自己的构建方法，甚至还会在调用自己之前先调用基类、也就是staff类的构造方法。

### `base`关键词
```csharp
public class Staff
{
    public Staff()
    {
        Console.WriteLine("员工类初始化");
    }

    public int Number { get; set; }
    
    public Staff(int number)
    {
        Number = number;
    }
}

public class Manager : Staff
{
    public Manager()
    {
        Console.WriteLine("经理类初始化");
    }
    
    public Manager(int number)
    {
 		Console.WriteLine("{number}经理初始化");
    }
}

```
运行一下发现，manger对象的编号为0，一般来说，整数对象如果没有赋值的时候就是0。所以，这就说明构造方法根本没有对员工编号赋值。这就说明，虽然我们在初始化manager对象的时候使用了参数，但是在调用继承构造方法的时候，并不是按我们预想的那样调用staff类有参数的构造方法，而是调用了无参数的默认构造方法。实际上，如果一个class存在多个构造方法，那么他在处理类继承的时候就面临着多个构造方法选择的问题。**在默认情况下，无论基类有多少个构造方法，他的衍生类在创建对象的时候都会默认使用默认无参数构造方法执行继承工作。所以，这就是为什么manager对象的赋值会失败。**
```csharp
public class Manager : Staff
{
    public Manager()
    {
        Console.WriteLine("经理类初始化");
    }

    // 加个base(number)就能指定屌用继承构造函数
    public Manager(int number) : base(number)
    {
 		Console.WriteLine("{number}经理初始化");
    }
}
```

## 向上转型和向下转型
### 向上转型（upcasting）
```csharp
public class Shape
{
    // comment
}

public class Circle : Shape 
{
    // comment
}

Circle circle = new Circle();
// implicit transform (upcasting)
Shape shape = circle;
```
#### 7.12.2 向下转型（downcasting）
```csharp
public class Shape
{
    // comment
}

public class Circle : Shape 
{
    // comment
}

Circle circle = new Circle();
Shape shape = circle;
// explicit transform (downcasting)
Circle circle2 = (Circle)shape
```

### `as` keyword
为了防止这种低级错误，我们可以使用as关键词。<br />比如说，有随便一个对象，我们希望把他转化为Car。显式转化如代码所示，我们连obj原始类型都不清楚，所以这段代码大概率会报错。
```csharp
Car car = (Car) obj; // 可能会抛异常
Car car = obj as Car; // 不会抛出异常
if (car != null) {
	// continue
}
```

### `is` keyword
使用is关键词，我们可以检查对象的类型。通过先进行类型验证，再执行对象转换，这样可以极大的提高我们代码的安全程度。
```csharp
if (obj is Car) {
	Car car = (Car) obj; // 不会出错
}
```
如示例代码，如果obj is 是一辆 car，那么，我们就可以在if语句中安全的对obj进行汽车类型的转换了。否则，我们就不会obj做任何处理。