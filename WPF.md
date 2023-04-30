# WPF

> <u>什么是WPF？</u>
>
> WPF是 **Windows Presentation Foundation** 的简称，是专门用来编写程序 <u>表示层</u> 的技术和工具。

​		大部分的程序都是多层架构的程序，一般至少包含三层：**数据层、业务逻辑层、表示层**。其中，表示层负责把数据的流程展示给用户看，表示层程序也常被称为**客户端程序**。WPF的功能就是用来编写应用程序的表示层，而数据层和业务逻辑层的开发有其相应的专门的技术。

![应用程序的多层架构](E:\Practice\记录文档\WPF.assets\应用程序的多层架构.png)



## WPF项目构成

​		从VS中新建一个WPF应用程序项目，VS会自动生成一系列必要的源代码，这为开发者节省了大量的时间和精力，如下图：

![image-20220731154144800](E:\Practice\记录文档\WPF.assets\WPF项目构成.png)

- Properties：包含了程序要用到的一些资源（如图标图片、静态的字符串）和配置信息
- References：标记了当前项目需要引用哪些**其他的项目**，目前包含的都是.NET Framework中的类库
- App.xaml：程序的主体。它声明了程序的主窗体。其中，`App.xaml.cs`为App.xaml的后台代码
- MainWindow.xaml：程序的主窗体。同样地，`MainWindow.xaml.cs`为MainWindow.xaml的后台代码





# XAML

> <u>什么是XAML？</u>
>
> XAML，读作 *zaml*，全称是*Extensive Application Markup Language*，即可扩展应用程序标记语言。它在桌面开发及富媒体网络程序的开发中扮演了HTML+CSS+JavaScript的角色，成为设计师与程序员之间沟通的枢纽。**所以，XAML是WPF技术中专门用于设计UI的语言**。

​		XAML是一种声明性语言，当你见到一个标签，就意味着声明了一个对象。XAML实现了UI与逻辑的剥离，它只能用来声明一些UI元素，无法在其中加入程序逻辑。这样，与UI相关的元素统统集中在程序的UI层，与逻辑相关的代码统统集中在程序的逻辑层，形成了一种 **高内聚 - 低耦合** 的结构。

​		XAML是一种由XML派生而来的语言，很多XML中的概念在XAML中是通用的。



## 简单的XAML代码

```xaml
<Window x:Class="MyFirstWpfApplication.Window1"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        Title="Window1" Height="300" Width="300">
    	<Grid
</Window>
```



### 标签

​		类似与XML，XAML中也需要用 **标签**`<Tag>Content</Tag>`来声明一个元素，每个元素都有起始标签和终止标签，夹在标签中的代码是属于这个标签的内容，当然也可以存在 **空标签**`<Tag></Tag>`。在标签中，可以为元素的 **特征Attribute** 赋值。

#### Attribute和Property

​		在进行面向对象思想编程时，常常需要对客观事物抽象，再把抽象出来的结果封装成类，类中用来表示事物状态的成员就是**Property**。而Attribute是编程语言语法层面的东西。当有A和B两个元素，为了区分A和B的用法，通常会在A和B上加一些Attribute，与抽象出来的对象没有关系。所以，可以将**Attribute**翻译为**特征**。Attribute只是用来影响类在程序中的用法，Property则对应着抽象对象的性状，两者不是一个层面的东西。





# MVVM





## C#



### 委托

​		C#中的委托关键字是**delegate**，类似与C++中的函数指针。**委托是一个类**，它可以绑定一个或多个函数，然后通过委托去执行绑定的函数，所以被称为委托。C#中有自带的委托类，Action和Func。



#### Action和Func

**Action：** 无返回值，可以带有任意数量参数

```C#
Action BookAction = new Action(Book);
BookAction();
public void Book()
{
    Console.WriteLine("我是提供书籍的");
}
```

```C#
Action<string> BookAction = new Action<string>(Book);
            BookAction("百年孤独");
public void Book(string BookName)
{
	Console.WriteLine("我是买书的是:{0}",BookName);
}
```

**Func：** 具有返回值和参数

```c#
Func<string> RetBook = new Func<string>(FuncBook);
Console.WriteLine(RetBook);
public string FuncBook()
{
	return "送书来了";
}
```



