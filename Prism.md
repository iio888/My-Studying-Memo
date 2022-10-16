# Prism

​		Prism提供了一组设计模式的实现，有助于编写结构良好的可维护XAML应用程序，包括**MVVM、依赖注入、命令、事件聚合器**。

**学习资料：**

- [博客园]:https://www.cnblogs.com/seekdream/category/867518.html

- [官方文档]:https://prismlibrary.com/docs/index.html





## 前言

​		即预备知识



### 创建一个Prism应用程序

- 添加NuGet包：**Prism.Unity** 或 Prism.DryIoc

  添加其中一个包后，会自动添加Prism.Wpf, Prism.Core

- 修改`App.xaml`

  - 添加命名空间
  - 将App替换为PrismApplication
  - 删除StartupUri属性

  ```xaml
  <prism:PrismApplication x:Class="PrismApplication.App"
               xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
               xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
               xmlns:local="clr-namespace:PrismApplication"
               xmlns:prism="http://prismlibrary.com/">
  
  </prism:PrismApplication>
  ```

- 修改 `App.xaml.cs`

  - 将继承的基类改为 PrismApplication，并引入对应命名空间
  - 因为PrismApplication是一个抽象类，所以需要实现它的接口

- 删除 `MainWindow.xaml`

  

### 添加第一个页面

- 新建一个页面

- 修改 `App.xaml.cs`

  ```c#
  protected override Window CreateShell()
  {
      return Container.Resolve<ShellWindow>();
  }
  
  protected override void RegisterTypes(IContainerRegistry containerRegistry)
  {
  
  }
  ```

​		其中，Container表示你所使用的依赖注入(DI)容器，`return Container.Resolve<ShellWindow>();` 表示此依赖注入容器将解析你的第一个页面<u>ShellWindow</u>的实例。



### 更简单地创建应用程序

- 获取 **Prism Template Pack**扩展，并安装

- 新建 Prism Blanket App 项目

  ![image-20221015163301929](E:\Practice\记录文档\Prism.assets\image-20221015163301929.png) 

- 创建项目完成后，VS会自动添加修改如上所作的操作

  ![image-20221015164145023](E:\Practice\记录文档\Prism.assets\image-20221015164145023.png) 



### 异常问题

1. 编译失败：找不到资产文件……运行 NuGet 包还原以生成此文件

   菜单栏：工具 -> NuGet包管理器 -> 程序包管理器控制台 -> 输入命令：**dotnet build**

2. 运行时弹出：该项目不知道如何运行配置文件

   菜单栏：工具  -> 获取工具与功能 -> 

   ![image-20220828193942081](E:\Practice\记录文档\Prism.assets\image-20220828193942081.png) ![image-20220828194102509](E:\Practice\记录文档\Prism.assets\image-20220828194102509.png)或者**修复VS**



### 快速浏览重要概念

#### Modules

​		Modules是能够独立开发、测试、部署的功能单元，Modules可以被设计成实现特定业务逻辑的模块（如Profile Management），也可以被设计成实现通用基础设施或服务的模块（如Logging、Exception Management）。

#### Module Catalog

​		在Prism中，Module Catalog指明了要Load哪些Module，和用什么样的顺序去Load这些Module。

#### Shell

​		Shell是宿主应用程序（host application），modules将会被load到Shell中。Shell定义了应用程序的整体布局和结构，而不关心寄宿其中的Module，Shell通常实现通用的application service和infrastructure，而应用的逻辑则实现在具体的Module中，同时，Shell也提供了应用程序的顶层窗口。

​		Shell is the Main application window，他是程序的主窗口，承载了构成应用程序的所有UI和控件。

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

​		Region可以充当注入Shell的动态内容的占位符。

![695641-20160814220240375-1527515297](E:\Practice\记录文档\Prism.assets\695641-20160814220240375-1527515297.png) 

#### Navigation

Prism支持两种导航：state-based导航和view-switching导航。

#### Event Aggregator

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





## 如何使用

​		

### Region

​		一块区域，用于注入View。

#### RegionManager

​		一个类，维护Prism应用程序中定义的所有Region的集合，应用程序中的每个Region都将被添加至RegionManager中。它还提供对这些Region的访问，<u>如视图组合（View Composition），区域导航（Region Navigation），创建区域（Define Region)。</u>

![image-20221015164520338](E:\Practice\记录文档\Prism.assets\image-20221015164520338.png)

#### 定义一个Region

​		设置prism:RegionManager.RegionName即定义了一个Region。

**注意：**Region的名称在应用程序中必须是唯一的，但是 scoped region 例外。

```xaml
<Grid>
	<ContentControl prism:RegionManager.RegionName="ContentRegion" />
</Grid>
```



#### Region Adapter

​		Region Adapter可以将View注入Region，Prism提供了以下Region Adapter：

- ContentControlRegionAdapter
- ItemsControlRegionAdapter
- SelectorRegionAdapter
  - 以下控件可以使用此Region Adapter
  - ComboBox
  - ListBox
  - Ribbon
  - TabControl

**自定义RegionAdapter：**

​		如果你想使用的控件没有RegionAdapter，并且仍然使用，那么程序将会出现异常。所以，你需要自定义一个RegionAdapter。

![image-20221015172241068](E:\Practice\记录文档\Prism.assets\image-20221015172241068.png)

1. 创建一个继承自 `RegionAdapterBase<T>` 的类，T为所使用的控件，如StackPanel

2. 实现接口

   ```c#
   public class StackPanelRegionAdapter : RegionAdapterBase<StackPanel>
       {
           public StackPanelRegionAdapter(IRegionBehaviorFactory regionBehaviorFactory)
               : base(regionBehaviorFactory) 
           { }
   
           protected override void Adapt(IRegion region, StackPanel regionTarget)
           {
               //implement code
               region.Views.CollectionChanged += (s, e) =>
               {
                   if (e.Action == System.Collections.Specialized.NotifyCollectionChangedAction.Add)
                   {
                       foreach (UIElement item in e.NewItems)
                       {
                           regionTarget.Children.Add(item);
                       }
                   }
                   else if (e.Action == System.Collections.Specialized.NotifyCollectionChangedAction.Remove)
                   {
                       foreach (UIElement item in e.OldItems)
                       {
                           regionTarget.Children.Remove(item);
                       }
                   }
               };
           }
   
           protected override IRegion CreateRegion() => new Region();
       }
   ```

3. 在 `App.xaml.cs` 中重新方法，并注册此RegionAdapter

   ```c#
   protected override void ConfigureRegionAdapterMappings(RegionAdapterMappings regionAdapterMappings)
   {
       base.ConfigureRegionAdapterMappings(regionAdapterMappings);
       regionAdapterMappings.RegisterMapping(typeof(StackPanel),
                                             Container.Resolve<StackPanelRegionAdapter>());
   }
   ```



### Module

​		单个Module表示单个模块。例如，一个邮件系统，Module可以是发信模块、收件模块、联系人模块等。在Visual Studio中，Module可以是一个类、一个库、一个实现了IModule接口的类。

![image-20221015203857666](E:\Practice\记录文档\Prism.assets\image-20221015203857666.png)



#### 创建一个Module

​		创建的Module类需要继承 **IModule**，并实现 `OnInitialized`方法和`RegisterTypes`方法。`RegisterTypes`方法在这个Module被Prism初始化的时候执行，`OnInitialized`方法随后执行。

​		containerRegistry参数便于你注册任何需要向Ioc注册的View和Service。containerProvider参数便于你解析或创建之前在Ioc中注册过的对象。

```c#
public class Module_IO : IModule
{

    public void OnInitialized(IContainerProvider containerProvider)
    {

    }

    public void RegisterTypes(IContainerRegistry containerRegistry)
    {

    }
}
```



#### Module Catalog

​		你需要在Module Catalog中注册你的Moduel。Module Catalog可以是不同的结构，如Code、App.config、Disk/Directory、XAML、Custom。

- code：一种强耦合的方式，它基于代码，需要应用程序强引用Moduel本身；
- App.config：使用类似于App.xaml的配置文件来配置，需要手动列出所有的Module；
- Disk/Directory：直接指出Moduel的所在目录，当程序启动时，会在指定目录加载所有Modules；
- Custom：使用定制的方式作为Module Catalog。

**Code:**

```c#
//App.xaml:
protected override void ConfigureModuleCatalog(IModuleCatalog moduleCatalog)
{
    //Register Module
    moduleCatalog.AddModule<ModuleIOModule>();
}
```

**Disk/Directory**

```c#
//App.xaml:
protected override IModuleCatalog CreateModuleCatalog()
{
    return new DirectoryModuleCatalog() { ModulePath = @".\Modules" };
}
```

- Module的项目属性 -> 生成事件：

```
xcopy "$(TargetDir)$(TargetName)"$(TargetExt)""$(SolutionDir)$(SolutionName)\bin\Debug\net6.0-windows\Modules\" /Y /S
```

**App.config**

```c#
//App.xaml
protected override IModuleCatalog CreateModuleCatalog()
{
    return new ConfigurationModuleCatalog();
}
```

> - 在项目中添加一个应用程序配置文件
>
> ![image-20221016162759071](E:\Practice\记录文档\Prism.assets\image-20221016162759071.png) 

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
	<configSections>
		<section name="modules" type="Prism.Modularity.ModulesConfigurationSection, Prism.Wpf"/>
	</configSections>
	<modules>
		<module assemblyFile="ModuleIO" 
                moduleType="ModuleIO.ModuleIOModule, ModuleIO, 
						    Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"
				moduleName="ModuleIOModule" startupLoaded="True"/>
	</modules>
</configuration>
```

- Module的项目属性 -> 生成事件：

```
xcopy "$(TargetDir)*.*" "$(SolutionDir)\$(SolutionName)\bin\Debug\net6.0-windows\" /Y
```





### View

​		在Prism中，一个View可以由多个View组成，View会被注入到Region中。所有，通常情况下，**View 是一个UserControl**。



#### View Composition

- 创建一个View的实例
- 并将这个实例加入到Region中

**实现方式：**

- View Discovery
- View Injection



#### View Discovery

​		使用此方式实现View Composition，使Region自己寻找需要的View，并且创建View实例，加入到该Region中。这意味着，无法控制何时创建View，以及何时将View加入Region。当Region初始化完成后，会自动执行这些操作。

**向RegionManager注册View：**

```c#
public class ModuleIOModule : IModule
{
    private readonly IRegionManager _regionManager;
    public ModuleIOModule(IRegionManager regionManager)
    {
        _regionManager = regionManager;
    }
    public void OnInitialized(IContainerProvider containerProvider)
    {
        _regionManager.RegisterViewWithRegion("ContentRegion", typeof(ViewA));
    }

    public void RegisterTypes(IContainerRegistry containerRegistry)
    {

    }
}
```



#### View Injection

​		需要手动创建View实例，并将其添加至Region，或在不需要时从Region中移除。开发人员可以控制何时以及如何将View添加至Region，同时也能控制Deactivate或激活View（前提是View已经存在在Region中）。

```c#
IRegion region = _regionManager.Regions["ContentRegion"];
var view1 = containerProvider.Resolve<ViewA>();
region.Add(view1);
```

​		一个Region中，同一时间只有一个View是Active的。所以，如下代码显示的Region中没有任何View。

```c#
IRegion region = _regionManager.Regions["ContentRegion"];
var view1 = containerProvider.Resolve<ViewA>();
var view2 = containerProvider.Resolve<ViewA>();
region.Add(view1);
region.Add(view2);

region.Activate(view1);
region.Activate(view2);
region.Deactivate(view2);
```





### ViewModel Loacator

​		用于连接ViewModel的实例和View的Datacontext。ViewModel Loacator会自动构造你的ViewModel，并设置对应View的DataContext。<u>与此同时，开发者需要遵循一些**命名规则**，如：</u>

- View需要在Views的命名空间下
- ViewModel需要在ViewModels命名空间下
- VM name = View Name + "ViewModel"
  - 若View以View结尾，**如MainView**
  - 则View Model命名为**MainViewModel**



#### 使用步骤

- 在ViewModels文件夹下，创建一个ViewModel
- ViewModel需要继承自BindableBase
- 在View中绑定ViewModel中的属性
- 设置属性 `prism:ViewModelLocator.AutoWireViewModel="True"`

```C#
 public class ViewAViewModel : BindableBase
{
    private string _title = "Hello from ViewModel";
    public string Title
    {
        get { return _title; }
        set { SetProperty(ref _title, value); }
    }
}
```



#### 修改命名规则

​		在App.xaml重写ConfigureViewModelLocator方法。

```c#
protected override void ConfigureViewModelLocator()
{
    base.ConfigureViewModelLocator();
    ViewModelLocationProvider.SetDefaultViewTypeToViewModelTypeResolver((viewType) =>
    {
        //Name Logic
        var viewName = viewType.FullName;
        var assemblyName = viewType.Assembly.FullName;
        //one Assembly
        var vmName = $"{viewName.Replace("Controls", "ViewModels")}ViewModel, {assemblyName}";
        return Type.GetType(vmName);
    });
}
```



#### 直接指定ViewModel

​		不用经过反射，效率更高，速度更快。

```c#
public void RegisterTypes(IContainerRegistry containerRegistry)
{
    //First Way
    ViewModelLocationProvider.Register<ViewA, ViewAViewModel>();
    
    //Second Way
    ViewModelLocationProvider.Register<ViewA>(() =>
    {
        return new ViewAViewModel() { Title = "Factory" };
    });
}
```





### Command

#### 定义

​		有三种定义方式，下列列出了两种，其中，第一种的第二项参数是可选项。`DelegateCommand<T>`表示DelegateCommand接受T类型的参数。CanExe参数可以控制该Command是否会被触发，以及按钮的IsEnable属性。

- 当然，Command必须是View Model的属性，以便View绑定

```c#
DelegateCommand SomeCommand = new DelegateCommand(DoSomething, CanExe);
DelegateCommand<string> stringCommand = new DelegateCommand<string>(DoStringThing);

private bool CanExe()
{
    return true;
}

private void DoStringThing(string str)
{

}
private void DoSomething()
{
    Title = "Do Something!";
}
```



### IEventAggregator

​		View之间，ViewModel之间，或组件之间的沟通方式。IEventAggregator基于事件，拥有订阅者和发布者。

![image-20221016213936157](E:\Practice\记录文档\Prism.assets\image-20221016213936157.png)



### 使用

> ​		假设一个场景，ModuleA的ViewA向ModuleB的ViewB发消息，ViewA有一个编辑框和一个发送按钮，ViewB有一个消息列表。ViewB从ViewA接受的消息会显示在消息列表中。此时，ViewModelA需要向ViewModelB发消息。A是发布者，B是订阅者。

- 新建一个项目，用于放置事件类，因为Module A和Module B同时需要引用此事件类型

  ![image-20221016233802991](E:\Practice\记录文档\Prism.assets\image-20221016233802991.png) 如下代码创建事件即可。

  ```c#
  public class MessageSentEvent: PubSubEvent<string> { }
  ```

- 在Module A和Module B项目中都引用PrismDemo.Core项目

- ViewModelA：

  ```c#
  public class ViewAViewModel : BindableBase
  {
      private string _message = "Hello";
      private readonly IEventAggregator _eventAggregator;
  
      public string Message
      {
          get { return _message; }
          set { SetProperty(ref _message, value); }
      }
  
      public DelegateCommand<string> StringCommand { get; private set; }
  
      public ViewAViewModel(IEventAggregator eventAggregator)
      {
          StringCommand = new DelegateCommand<string>(DoStringThing);
          _eventAggregator = eventAggregator;
      }
  
  
      private void DoStringThing(string str)
      {
          _eventAggregator.GetEvent<MessageSentEvent>().Publish(Message);
      }
  }
  ```

- ViewModelB：

  ```c#
  class ViewBViewModel: BindableBase
  {
      private ObservableCollection<string> _msgList = 
          new ObservableCollection<string>();
      public  ObservableCollection<string> MsgList
      {
          get { return _msgList; }
          set { SetProperty(ref _msgList, value); }
      }
  
      public DelegateCommand ClearCommand { get; private set; }
  
      public ViewBViewModel(IEventAggregator eventAggregator)
      {
          ClearCommand = new DelegateCommand(ClearMsg);
          eventAggregator.GetEvent<MessageSentEvent>().Subscribe(OnMsgReceived);
      }
  
      private void OnMsgReceived(string msg)
      {
          MsgList.Add(msg);
      }
  
      private void ClearMsg() => MsgList.Clear();
  }
  ```

  
