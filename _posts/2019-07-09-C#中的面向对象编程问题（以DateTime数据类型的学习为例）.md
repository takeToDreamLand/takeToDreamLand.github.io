---
layout: post
title: C#中的面向对象编程问题
subtitle: 以DateTime数据类型的学习为例
---

在学习C#中的面向对象编程时，我想到可以用C#中的`DateTime`数据类型为学习对象，来加深对**类（对象）** 这个概念的理解。
我们点开`DateTime`数据类型的定义：  
![](http://evernotepic-1253152250.cossh.myqcloud.com/20190417021611.png)
第一眼可能看的有点迷糊，但是我们根据各行最前面的定义修饰符可以将其分为四类：**静态属性**，**静态方法**，**实例属性**，**实例方法**。分别对应着`static type Name`、`static type Name()`、`type Name`、`type Name()`。而实例方法又包含**构造函数**。如下所示。   

|-----------------+----------------+--------------|
|                 | 属性 Attribute | 方法 Function |
|-----------------|:----------------:|:---------------:|
| **静态 Static** | 静态属性       | 实例属性      |
| **实例 Entity** | 静态方法       | 实例方法      |
|-----------------+---------------+---------------|

### 实例化
---

在介绍以上四类的区别前，我们先要对`实例化`这一概念有一个清楚的认识。  
首先，`对象（object，抽象意义上的对象）`是对一个现实目标**对象**的定义，是一种描述。举个例子，我们对**学生**这一群体进行了观察和分析后，定义了一个`Student`对象，该对象包含`ID`、`Name`、`Age`三个属性，此时`Student`对象便是对学生这一现实目标对象的定义和描述。那这跟实例化有什么关系呢？那我们接着举例，假如我们要用上面定义的`Student`对象来描述一名叫作**小明**的学生，那么就需要将`Student`对象**实例化**为一个具体的实例，并为该实例赋予具体的值，如下：
```cs
Student student_XiaoMing = new Student()
{
    ID = 1900001;
    Name = "小明"；
    Age = 18;
}
```
这样的话，`student_XiaoMing`就能够描述小明这个人了。这就是**实例化**，是将抽象概念转换为具体对象的操作。
### 静态属性
#### 概念：
所谓静态属性，即只属于类本身的属性，而与实例对象无关的属性，不需要任何实例化即可使用的属性。举个例子，我们定义了**人**这么个类，这个类有个属性叫做**物种**，则我们可以将其定义为静态属性，因为这个属性的值一直为"**人类**"（从生物概念上的人类）。这样，无论这个类是否实例化，都可以调取这个属性的值。
#### 定义方式：
我们使用`static Name`来定义。需要注意的是，由于是一种**属性**，它的定义不需要像**函数**（**方法**）那样带括号，这也是我们写作`static Name`而不是`static Name()`的原因。代码如下：
```cs
        // 摘要:
        //     表示 System.DateTime 的最小可能值。 此字段为只读。
        public static readonly DateTime MinValue;
        
        // 摘要:
        //     表示 System.DateTime 的最大可能值。 此字段为只读。
        public static readonly DateTime MaxValue;
	    
        // 摘要:
        //     获取一个 System.DateTime 对象，该对象设置为此计算机上的当前日期和时间，表示为本地时间。
        //
        // 返回结果:
        //     其值为当前日期和时间的对象。
        public static DateTime Now { get; }
        
        // 摘要:
        //     获取当前日期。
        //
        // 返回结果:
        //     一个对象，设置为当天日期，其时间组成部分设置为 00:00:00。
        public static DateTime Today { get; }
```
#### 使用：
静态代表该属性不需要实例化即可使用，也就是其不是属于对象的属性。我们不需要先定义（或者实例化）一个`DateTime`数据类型的变量就可以获取该属性。比如下面的代码使用了`DateTime`数据类型的几个静态属性：
``` cs
    class Program
    {
        static void Main(string[] args)
        {
            // public static readonly
            Console.WriteLine("DateTime数据类型的最大值为{0}", DateTime.MaxValue);
            Console.WriteLine("DateTime数据类型的最小值为{0}", DateTime.MinValue);
            // public static 
            Console.WriteLine("现在的时间为{0}", DateTime.Now);
            Console.WriteLine("现在的日期为{0}", DateTime.Today);
            Console.Read();
    }
```
代码中`readonly`修饰符代表该**静态属性**是只读不可写的，就像数学中的`π`一样。按F5运行的结果如下：   
![](http://evernotepic-1253152250.cossh.myqcloud.com/20190417024213.png)
> 考虑一下，如果修改一个未实例化的对象的静态属性会怎样？

----
### 静态方法
#### 概念：
**静态方法**与上面刚讲的**静态类型**相比有微妙的不同。静态方法也是属于对象本身的，它相当于为对象定制的一系列操作方法（譬如加减乘除、掐头去尾等等）。举个例子，我们定义了 **动物（Animal）** 这么个对象，我们为这个对象定义了一个**静态方法**——**交配**，方法的定义为`public static Animal Mating(animal1, animal2)`，假设这个方法能够将返回两种动物交配的后代，那么我们就可以使用`Animal.Mating(马, 驴)`这个语句调用`Mating()`方法，并得到返回的结果为`骡子`！
为了方便理解**静态方法**代表什么，我们将`DateTime`数据类型的代码中典型的静态方法截图出来Review一下会更好理解。如下面两张图：
![](http://evernotepic-1253152250.cossh.myqcloud.com/20190417024830.png)
![](http://evernotepic-1253152250.cossh.myqcloud.com/20190417024557.png)
上面的图中，一方面定义了一些属于对象的操作方法：  `static int Compare(DateTime t1, DateTime t2)`用于比较`t1`和`t2`的大小、`static int DaysInMonth(int year, int month)`定义了一个获得某年某月的整月天数（有28、29、30、31四种情况）的方法；  
也定义了适用于**运算符**的一些规则：使用减号计算时间差的`static TimeSpan operator -(DateTime d1, DateTime d2)`、使用**逻辑运算法**判断两个时间的关系的`static bool operator ==(DateTime d1, DateTime d2)`。诸如此类，都是属于对象的方法，也就是**静态方法**。
#### 定义方式：
**静态方法**的定义与**静态属性**最大的区别在于定义时需要指定**形参**（当然也可以缺省），即`static Name(val1, val2)`的形式。示例如下：
```cs
        // 摘要:
        //     对两个 System.DateTime 的实例进行比较，并返回一个指示第一个实例是早于、等于还是晚于第二个实例的整数。
        //
        // 参数:
        //   t1:
        //     要比较的第一个对象。
        //
        //   t2:
        //     要比较的第二个对象。
        //
        // 返回结果:
        //     有符号数字，指示 t1 和 t2 的相对值。 值类型 条件 小于零 t1 早于 t2。 零 t1 与 t2 相同。 大于零 t1 晚于 t2。
        public static int Compare(DateTime t1, DateTime t2);
        
        // 摘要:
        //     返回指定年和月中的天数。
        //
        // 参数:
        //   year:
        //     年。
        //
        //   month:
        //     月（介于 1 到 12 之间的一个数字）。
        //
        // 返回结果:
        //     指定 month 中 year 中的天数。 例如，如果 month 等于 2（表示二月），则返回值为 28 或 29，具体取决于 year 是否为闰年。
        //
        // 异常:
        //   T:System.ArgumentOutOfRangeException:
        //     month 小于 1 或大于 12。 - 或 - year 小于 1 或大于 9999。
        public static int DaysInMonth(int year, int month);
```
#### 使用：
下面展示了`DateTime`数据类型的两个静态方法的使用：判断两个日期是否相同的`DateTime.Equal()`方法和对于`DateTime`数据类型的加号（+）运算。
```cs
        static void Main(string[] args)
        {
            DateTime now = DateTime.Now;
            DateTime lastMonth = now.AddMonths(-1); // 月份减1
            Console.WriteLine("现在的时间为{0}", now);
            Console.WriteLine("上个月为{0}", lastMonth);

            // static function
            Console.WriteLine(DateTime.Equals(now,lastMonth)?"两个日期相同":"两个日期不相同");

            Console.WriteLine("现在的日期为{0}，时间间隔为{1}，加起来为{2}", DateTime.Today, new TimeSpan(10000000), DateTime.Today + new TimeSpan(10000000));
            Console.Read();
        }
```
按F5运行结果如下：
![](http://evernotepic-1253152250.cossh.myqcloud.com/20190417083905.png)

----
### 实例属性
#### 概念：
实例属性通常是在学习**面向对象编程**时碰到的第一个知识点，所以其实是相当基础、并且可以在很多地方看到相似用法的内容。这里我们就选择了**C语言**中的**结构体**来比较，加深理解。
所谓对象的实例属性，可以直接理解为结构体中的属性。这些属性在类被定义时不会开辟相应的内存空间，而是当被实例化后，才能被访问到。我们比较一下C语言中的**结构体**和C#中的**对象**，这两者的定义和使用：
C语言中的结构体：
```C
#include <stdio.h>
struct Human {
	char Name[15];
	int Age;
};
int main()
{
	struct Human human1 { "小明",18 };
	printf("创造的人类名为%s，年龄为%d", human1.Name, human1.Age); // 引用结构体属性
}
```
![](http://evernotepic-1253152250.cossh.myqcloud.com/20190420001421.png)
和C#中的对象：
```cs
namespace Project_Human
{
    class Program
    {
        static void Main(string[] args)
        {
            Human human1 = new Human()
            {
                Name = "小明",
                Age = 18
            };
            // 引用类的实例属性
            Console.WriteLine("创造的人类名为{0}，年龄为{1}", human1.Name, human1.Age); 
        }
    }
    class Human
    {
        public string Name { get; set; } // get和set分别指定了该属性的读和写动作触发的函数//
        public int Age { get; set; } // 在规范的要求中，我们需要写上代表其支持读写
    }
}
```
![](http://evernotepic-1253152250.cossh.myqcloud.com/20190420002013.png)  
以上两种语言编写相同功能时，仅有细微的差别。主要是集中在属性定义和实例化上。这里主要是由于C#从创造之初就是面向企业开发的，代码上其更多的遵循了“**规范化**”的思想，对我们而言的直观感受就是其非常的臃肿、学习起来非常费功夫。但是这其实也是其强大功能所带来的一种副作用，抛开对它的成见，学的会轻松一点。
#### 定义方式：
在上面的代码以及`DateTime`数据类型的定义中，我们也可以看到，实例属性的定义与静态属性相比，其实只是少了`static`这么一个修饰词，其它并没有什么区别。
`DateTime`类型中的实例属性定义:
```cs
public long Ticks { get; }
public DateTime Date { get; }
public int Month { get; }
public int Minute { get; }
public int Millisecond { get; }
public DateTimeKind Kind { get; }
public int DayOfYear { get; }
public DayOfWeek DayOfWeek { get; }
public int Day { get; }
public int Second { get; }
public TimeSpan TimeOfDay { get; }
public int Year { get; }
```
我们可以看到，一个`DateTime`的数据类型本质是一个**对象（Object）**，其有许许多多与时间相关的**属性**，包括年、月、日、周、时、分、秒等。而这些属性又是由各种各样的数据类型定义的，包括`int`、`long`、`TimeSpan(用于表示时间段)`等。
#### 使用：
所有的实例属性在类实例化以后才可以访问，但是如果实例化以后该属性没有值，大多数情况下其值默认为`null`。  
 > 当然也有其它情况，比如`int`型默认值是多少？
```cs
    class Program
    {
        static void Main(string[] args)
        {
            DateTime now = DateTime.Now;
            Console.WriteLine("现在是{0}。", now);
            Console.WriteLine("现在的日期为{0}，星期为{1}，{2}时{3}分{4}.{5}秒", now.Date, now.DayOfWeek, now.Hour, now.Minute, now.Second, now.Millisecond);
            Console.Read();
        }
    }
```
![](http://evernotepic-1253152250.cossh.myqcloud.com/20190420103957.png)  
在上述调用中，我们可以发现，当直接输出`DateTime`对象时，其表现与`string`无异；而当一个个访问它的属性时，它又表现为一个完完全全的`Object`。并且值得一提的是，`DateTime.Date`这个属性虽然是**获取日期部分的数据**，但事实上，由于返回的数据仍然是`DateTime`类型的，因此返回的日期还会带着**时间**部分的属性，只是全部为**空**了。

----
### 实例方法
#### 概念：
实例方法即对对象的操作方法，比如当我们在游戏中使用`Human`类定义了一个名为**NPC**的实例，它拥有一个名为`move(from, to)`的、用于移动的实例方法。现在我们规定这个NPC的行为方式为——不断的在两点间巡逻，那么我们就可以将其写作：
```cs
Human NPC = new Human();
NPC.move(A, B); // 从起点走到终点
NPC.move(B, A); // 从终点走到起点
```
#### 定义方式：
由于看不到`DateTime`数据类型中实例方法的定义，因此我们回到`Human`类的定义中，我们为创建的人类定义一个改名的实例方法——`AlterName()`。我们使用两种方式来定义，根据定义方式的不同引用的方式也不同。
```cs
    class Program
    {
        static void Main(string[] args)
        {
            var human = Human.Create();
            Console.WriteLine("创造的人类名字叫做{0}，年龄为{1}。", human.Name, human.Age);
            human.AlterName(); // 第一种方法：直接修改对象本身
            Console.WriteLine("创造的人类使用第一种方法修改后的名字叫做{0}，年龄为{1}。", human.Name, human.Age);
            human = new Human().AlterName(human); // 第二种方法：创建新的对象并覆盖原对象
            Console.WriteLine("创造的人类使用第二种方法修改后的名字叫做{0}，年龄为{1}。", human.Name, human.Age);
            Console.Read();
        }
    }
    class Human
    {
        // 实例属性：姓名和年龄
        public string Name { get; set; }
        public int Age { get; set; }
        
        // 实例方法：重新生成对象的名字，使用了两种不同的写法，调用的方法也会随着变化
        // 方法一：
        public void AlterName()
        {
            this.Name = NameSet.GenName(); // 重新生成随机名字
        }
        // 方法二：
        public Human AlterName(Human human)
        {
            human.Name = NameSet.GenName();
            return human;
        }
    }
```
运行结果如下：
![]()
第一种方法会直接修改实例的值（由于使用了`this`关键字，代表了调用的实例本身）；第二种方法会返回一个新的实例，这里等于是返回了一个除了名字以外都与原来的`Human`对象完全一致的新`Human`。
