# Prism

​		Prism提供了一组设计模式的实现，有助于编写结构良好的可维护XAML应用程序，包括**MVVM、依赖注入、命令、事件聚合器**。

**学习资料：**

- [博客园]:https://www.cnblogs.com/seekdream/category/867518.html

- [官方文档]:https://prismlibrary.com/docs/index.html



## 前言

​		即预备知识

### 安装

- 打开VS

- 菜单栏：扩展 -> 管理扩展

- 搜索**Prism Template Pack**

- 重启VS

- 可以直接从创建新项目中选择项目模板

  ![image-20220827213701512](E:\Practice\记录文档\Prism.assets\image-20220827213701512.png)
  
  

### 问题

1. 编译失败：找不到资产文件……运行 NuGet 包还原以生成此文件

   菜单栏：工具 -> NuGet包管理器 -> 程序包管理器控制台 -> 输入命令：**dotnet build**

2. 运行时弹出：该项目不知道如何运行配置文件

   菜单栏：工具  -> 获取工具与功能 -> 

   ![image-20220828193942081](E:\Practice\记录文档\Prism.assets\image-20220828193942081.png) ![image-20220828194102509](E:\Practice\记录文档\Prism.assets\image-20220828194102509.png)或者**修复VS**



### 重要概念

#### Modules

​		Modules是能够独立开发、测试、部署的功能单元，Modules可以被设计成实现特定业务逻辑的模块（如Profile Management），也可以被设计成实现通用基础设施或服务的模块（如Logging、Exception Management）。

#### Module Catalog

​		在Prism中，Module Catalog指明了要Load哪些Module，和用什么样的顺序去Load这些Module。

#### Shell

​		Shell是宿主应用程序（host application），modules将会被load到Shell中。Shell定义了应用程序的整体布局和结构，而不关心寄宿其中的Module，Shell通常实现通用的application service和infrastructure，而应用的逻辑则实现在具体的Module中，同时，Shell也提供了应用程序的顶层窗口。

#### Views

​		Views是应用中展现特定功能的视图，它展现UI、定义交互行为，并且通过数据绑定的方式与ViewModel进行交互。

#### View Model & Presenters

​	View Model用来封装应用程序的UI逻辑及其状态。

#### Model

​		Model被用来封装数据和相应的验证，以及相关的业务规则来保证数据的一致性和正确性。

#### Commands

​		Command被用来封装应用程序功能，Prism提供了Delegate Command和CompositeCommand两个类。

#### Regions

​		Regions是应用程序UI的逻辑区域，它很像一个PlaceHolder，Views在Regions中展现，很多种控件可以被用作Region：ContentControl、ItemsControl、ListBox、TabControl。Views能在Regions[编程](http://www.2cto.com/kf)或者自动呈现，Prism也提供了Region导航的支持。

![695641-20160814220240375-1527515297](E:\Practice\记录文档\Prism.assets\695641-20160814220240375-1527515297.png) 

#### Navigation

Prism支持两种导航：state-based导航和view-switching导航。

#### EventAggregator

组件通常要和其它的组件或者服务进行通信。为此，Prism提供了EventAggregator组件，这个组件实现pub-sub事件机制，允许一些组件发布事件，另一些组件去订阅事件而无需两个组件之间的引用。

#### Dependency Injection container

Prism使用依赖注入模式来管理组件之间的依赖，Prism被设计使用Unity、Mef等依赖注入容器。

#### Services

Services是用来实现非UI相关功能的逻辑，例如logging、exception management、data access。Services可以被定义在应用程序中或者是Module中，Services通常被注册在依赖注入容器中，使得其它的组件可以很容易的定位这个服务。

#### Controllers

Controller被用来控制在Region中哪个View将会被呈现，同时控制这个View的创建和初始化。

#### Bootstrapper

Bootstrapper用来初始化应用程序级别的组件和服务，它也被用来配置和初始化module catalog和Shell 的View和View Model。

![695641-20160814232047031-793196258](E:\Practice\记录文档\Prism.assets\695641-20160814232047031-793196258.png) 





## 使用

​		首先，选择一个项目模板，创建新项目。其次会弹出一个窗口，选择IOC容器，分别有两个可选项**Unity和DryIOC**。

