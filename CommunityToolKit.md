# Community ToolKit

​		The `CommunityToolkit.Mvvm` package (aka MVVM Toolkit, formerly named `Microsoft.Toolkit.Mvvm`) is a modern, fast, and modular MVVM library. 

​		This package targets **.NET Standard** so it can be used on <u>any app platform: UWP, WinForms, WPF, Xamarin, Uno, and more;</u> and on any runtime: .NET Native, .NET Core, .NET Framework, or Mono. It runs on all of them. The API surface is identical in all cases, making it perfect for building shared libraries.



## Getting Started

​		To install the package from within Visual Studio:

1. In Solution Explorer, right-click on the project and select **Manage NuGet Packages**. Search for **CommunityToolkit.Mvvm** and install it.

   ![NuGet Packages](https://learn.microsoft.com/en-gb/dotnet/communitytoolkit/images/get-started/manage-nuget-packages.png) 

2. Choose this one.

   ![image-20220924162817040](E:\Practice\记录文档\CommunityToolKit.assets\image-20220924162817040.png)





## Source Generator

​		Starting with version 8.0, the MVVM Toolkit includes brand new Roslyn **source generators** that <u>will help greatly reduce boilerplate</u> when writing code using the MVVM architecture. **They can simplify scenarios where you need to setup observable properties, commands and more.**

​		This is a simplified view of how they work:

![Roslyn source generators](https://learn.microsoft.com/en-gb/dotnet/communitytoolkit/images/source-generator-visualization.png)

​		**即：**Source Generator用于减少代码量，在你编写代码的同时，为你生成你经常写的重复性代码，并在编译时加入编译。



**例如：**在ViewModel中编写这一段：

```c#
[ObservableProperty]
private string? name;
```

**实际上会被编译为：**

```c#
private string? name;
public string? Name
{
    get => name;
    set => SetProperty(ref name, value);
}
```



### ObservableProperty 

​		The [`ObservableProperty`](https://learn.microsoft.com/en-us/dotnet/api/communitytoolkit.mvvm.componentmodel.ObservablePropertyAttribute) type is an attribute that allows generating **observable properties** from <u>annotated fields</u>. Its purpose is to greatly reduce the amount of boilerplate that is needed to define observable properties.

​		**即，**可绑定属性，通过打标签，帮你减少代码量，自动生成<u>OnPropertyChanged()</u>等，用于与界面UI绑定。类需要继承**ObservableObject**。

#### **How it works**

```C#
[ObservableProperty]
private string? name;
```

```c#
public partial class ViewModel : ObservableObject
{
    [ObservableProperty]
    private string? name = "AA";

}
```

​		此时，ViewModel类会自动生成一个名为**Name(大驼峰）**的<u>属性</u>，可以在View中访问和绑定。**注意，**ViewModel类必须带有关键字 **partial**，因为它的部分类会被Source Generator生成。~~(下图有三个部分类是因为在ViewModel中有两个属性，其中一个未在文中给定。若如上编写ViewModel，仅有两个部分类)~~

![image-20220924164740306](E:\Practice\记录文档\CommunityToolKit.assets\image-20220924164740306.png)

![image-20220924164844349](E:\Practice\记录文档\CommunityToolKit.assets\image-20220924164844349.png)

​		The generated code is actually a bit more complex than this, and the reason for that is that it also <u>exposes some **methods** you can implement to hook into the notification logic</u>, <u>and run additional logic when the property **is about to be updated and right after it is updated**</u>, if needed. That is, the generated code is actually *similar* to this:

```C#
public string? Name
{
    get => name;
    set
    {
        if (!EqualityComparer<string?>.Default.Equals(name, value))
        {
            OnNameChanging(value);
            OnPropertyChanging();
            name = value;
            OnNameChanged(value);
            OnPropertyChanged();
        }
    }
}

partial void OnNameChanging(string? value);
partial void OnNameChanged(string? value);
```

​		自动生成的代码还带有两个方法：**OnYourPropertyNameChanging和OnYourPropertyNameChanged**，用于在你的属性 **即将更新之前**和**更新后立刻**执行的操作逻辑，当然，如果你没有实现这两个方法，编译器会删除这两个方法的调用。



####  Notifying Dependent Property or Command

​		Imagine you had a `FullName` **property** you wanted to raise a notification for whenever `Name` changes. You can do that by using the `NotifyPRopertyChangedFor` attribute, like so:

​		**即，**当Name变化时，通知FullName。

```c#
[ObservableProperty]
[NotifyPropertyChangedFor(nameof(FullName))]
private string? name;
```

​		Imagine you had a **command** whose execution state was dependent on the value of this property. That is, whenever the property changed, the execution state of the command should be invalidated and computed again. In other words, [`ICommand.CanExecuteChanged`](https://learn.microsoft.com/en-us/dotnet/api/system.windows.input.icommand.canexecutechanged) should be raised again. You can achieve this by using the `NotifyCanExecuteChangedFor` attribute:

​		**即，**当Name变化时，通知MyCommand。

```C#
[ObservableProperty]
[NotifyCanExecuteChangedFor(nameof(MyCommand))]
private string? name;
```



### RelayCommand 

> The [`RelayCommand`](https://learn.microsoft.com/en-us/dotnet/api/communitytoolkit.mvvm.input.RelayCommandAttribute) type is an attribute that allows generating relay command properties for annotated methods. Its purpose is to completely eliminate the boilerplate that is needed to define commands wrapping private methods in a viewmodel.

#### How it works

```C#
[RelayCommand]
private void GreetUser()
{
    Console.WriteLine("Hello!");
}
```

> The name of the generated command will be created based on the method name. The generator will use the method name and append "Command" at the end, and it will strip the "On" prefix, if present.Additionally, for asynchronous methods, the "Async" suffix is also stripped before "Command" is appeneded.

​		编译器会根据方法名，自动生成Command类型的属性名称，一般会在方法名的后面加上“Command"，若方法名前缀包含"On"，则生成时会自动去除。此外，对于异步方法，“Async”后缀也会在“Command”附加之前被剥离。**例如：根据GreetUser方法，生成GreetUserCommand属性。**

​		当然，方法也可以带有参数。In that case, it will automatically change the generated command to be an [`IRelayCommand<T>`](https://learn.microsoft.com/en-us/dotnet/api/communitytoolkit.mvvm.input.IRelayCommand-1) instead, accepting a parameter of the same type:

```C#
[RelayCommand]
private void GreetUser(User user)
{
    Console.WriteLine($"Hello {user.Name}!");
}
```

#### Enabling and disabling 

> It is often useful to be able to disable commands, and to then later on invalidate their state and have them check again whether they can be executed or not. 

​		通过方法 **CanGreetUser** 控制GreetUserCommand的**CanExecute**。以下例子说明，每当selectedUser发生改变时，会触发`GreetUserCommand.NotifyCanExecuteChanged()` 方法，每当`CanExecuteChanged`方法被触发，`CanExecute`也会执行。

```c#
[RelayCommand(CanExecute = nameof(CanGreetUser))]
private void GreetUser(User? user)
{
    Console.WriteLine($"Hello {user!.Name}!");
}

private bool CanGreetUser(User? user)
{
    return user is not null;
}

[ObservableProperty]
[NotifyCanExecuteChangedFor(nameof(GreetUserCommand))]
private User? selectedUser;
```

```xaml
<Button
    Content="Greet user"
    Command="{Binding GreetUserCommand}"
    CommandParameter="{Binding SelectedUser}"/>
```





## Messenger

> The [`IMessenger`](https://learn.microsoft.com/en-us/dotnet/api/microsoft.toolkit.mvvm.Messaging.IMessenger) interface is a contract for types that can be used to **exchange messages** between different objects. This can be useful to <u>decouple different modules of an application</u> without having to keep strong references to types being referenced.The MVVM Toolkit provides two implementations out of the box: [`WeakReferenceMessenger`](https://learn.microsoft.com/en-us/dotnet/api/microsoft.toolkit.mvvm.Messaging.WeakReferenceMessenger) and [`StrongReferenceMessenger`](https://learn.microsoft.com/en-us/dotnet/api/microsoft.toolkit.mvvm.Messaging.StrongReferenceMessenger)：<u>**the former** uses weak references internally, offering automatic memory management for recipients, **while the latter** uses strong references and requires developers to manually unsubscribe their recipients when they're no longer needed.</u>

​		IMessenger用于消息传递，MVVM Toolkit包含了两种实现： [`WeakReferenceMessenger`](https://learn.microsoft.com/en-us/dotnet/api/microsoft.toolkit.mvvm.Messaging.WeakReferenceMessenger) and [`StrongReferenceMessenger`](https://learn.microsoft.com/en-us/dotnet/api/microsoft.toolkit.mvvm.Messaging.StrongReferenceMessenger)。

- WeakReferenceMessenger：使用弱引用，自动管理内存，更易于使用。它是 MVVM Toolkit中类型使用[`ObservableRecipient`](https://learn.microsoft.com/zh-cn/dotnet/communitytoolkit/mvvm/observablerecipient)的默认类型。
- StrongReferenceMessenger：使用强引用，需要手动取消订阅，性能更佳



### How it works

​		Types implementing `IMessenger` are responsible for maintaining links between recipients (receivers of messages) and their registered message types, with relative message handlers. 

​		实现`IMessenger`类型的**作用**是连接<u>消息的接收者和消息</u>。

​		There are **two ways** to perform message registration: either through **the [`IRecipient<TMessage>`](https://learn.microsoft.com/en-us/dotnet/api/microsoft.toolkit.mvvm.Messaging.irecipient-1) interface**, or **using a [`MessageHandler<TRecipient, TMessage>`](https://learn.microsoft.com/en-us/dotnet/api/microsoft.toolkit.mvvm.Messaging.messagehandler-2) delegate** acting as message handler.**The first** lets you register all the handlers with a single call to the `RegisterAll` extension, which automatically registers the recipients of all the declared message handlers, **while the latter** is useful when you need more flexibility or when you want to use a simple lambda expression as a message handler.

​		注册消息的两种方式：实现`IRecipient<TMessage>`接口和使用`MessageHandler<TRecipient, TMessage>`委托。

- IRecipient\<TMessage>：使用`RegisterAll`，一次性自动注册所有**事件处理器和其<u>所有</u>的订阅者**

- MessageHandler<TRecipient, TMessage>：拥有更强的灵活性，你可以使用简短的Lamda表达式作为消息处理器

  

### Sending and receiving messages

*Here are the Codes:*

```c#
// Create a message
public class LoggedInUserChangedMessage : ValueChangedMessage<User>
{
    public LoggedInUserChangedMessage(User user) : base(user)
    {        
    }
}

// Register a message in some module
WeakReferenceMessenger.Default.Register<LoggedInUserChangedMessage>(this, (r, m) =>
{
    // Handle the message here, with r being the recipient and m being the
    // input message. Using the recipient passed as input makes it so that
    // the lambda expression doesn't capture "this", improving performance.
});

// Send a message from some other module
WeakReferenceMessenger.Default.Send(new LoggedInUserChangedMessage(user));
```





## Ioc

​		Ioc, Inversion of Control, **控制反转**。



### 准备工作

​		The MVVM Toolkit doesn't provide built-in APIs to facilitate the usage of this pattern, as there already exist dedicated libraries specifically for this such as the `Microsoft.Extensions.DependencyInjection` package, which provides a fully featured and powerful DI set of APIs, and acts as an easy to setup and use `IServiceProvider`.

​		此工具包不提供实现控制反转的API，因为 `Microsoft.Extensions.DependencyInjection` 已经提供了足够强大的以来反转API。所以，在使用之前，<u>需要在项目的NuGet包中添加 `Microsoft.Extensions.DependencyInjection`</u>。



### 配置和解析服务

​		第一步就是声明一个IServiceProvider实例，并<u>初始化所有必要的服务</u>，通常是在程序启动时做这些事。

```c#
public sealed partial class App : Application
{
    public App()
    {
        Services = ConfigureServices();

        this.InitializeComponent();
    }

    /// <summary>
    /// Gets the current <see cref="App"/> instance in use
    /// </summary>
    public new static App Current => (App)Application.Current;

    /// <summary>
    /// Gets the <see cref="IServiceProvider"/> instance to resolve application services.
    /// </summary>
    public IServiceProvider Services { get; }

    /// <summary>
    /// Configures the services for the application.
    /// </summary>
    private static IServiceProvider ConfigureServices()
    {
        var services = new ServiceCollection();

        services.AddSingleton<IFilesService, FilesService>();
        services.AddSingleton<ISettingsService, SettingsService>();
        services.AddSingleton<IClipboardService, ClipboardService>();
        services.AddSingleton<IShareService, ShareService>();
        services.AddSingleton<IEmailService, EmailService>();

        return services.BuildServiceProvider();
    }
}
```



