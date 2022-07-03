# 基本概念



## 静态变量 `static`

- 存放在全局区，在程序结束后释放



## 命名空间

### 定义

```c++
namespace Han{
    int a = 0;
}
```

### 访问

- `Han::a = 1;`

- ```c++
  using Han::a;
  a = 1;
  ```

- `using namespace Han;`

- `::` 成为域解析操作符号，用来指明要使用的命名空间。

### 命名空间 `std`

 代码中的 `string`、`cin`、`cout` 都位于命名空间 `std `。

###  注意

1. 不同命名空间内的名字可以重复，从而避免命名冲突问题
2. 推荐在函数内部声明 std



## 基本输入输出

​		总结常用的几种输入输出形式。

### `cin` 的使用

​		从缓冲流中读入数据

#### 1 `cin >>`

​		以空格、制表符、回车结束

#### 2 `cin.get()`

​		读取每个字符，可以以EOF结束





### `cout` 的使用

​		`cout` 是 C++ 中 `ostream` 类型的**对象**，该类被封装在 `<iostream>` 库中。`ostream` 对象只能有一个。

- `clear()` 清空缓冲区，`cin` 同样适用



#### 连续输出过程

​		 **i** 和 **++i** 会在栈里面保存 **i** 的引用，而 **i++** 会在栈里面保存数字。

`cout << ++i << i++ << i << i++ << ++i << endl;`

- 将 **endl** 压入栈中， **i** 值不变；

- 将 **i** 的引用压入栈中， **i** 的值加 **1** 变成 **2**（ **++i** ）；

- 将 **2** 压入栈中， **i** 的值加 **1** 变成 **3**（ **i++** ）；

- 将 **i** 的引用压入栈中， **i** 的值不变（ **i** ）；

- 将 **3** 压入栈中， **i** 的值加 **1** 变成 **4**（ **i++** ）；

- 将 **i** 的引用压入栈中， **i** 的值加 **1** 变成 **5**（ **++i** ）；

- 将栈里的数据依次弹出，即可得到 **53525** 。

  （ 因为i的值是 **5** ，所以所有 **i** 的引用都是 **5** ）

  

#### 以不同进制输出

- 默认以十进制输出。`setbase` 和 `setiosflags` 包含在 `<iomanip>` 中

```c++
cout << i << endl;
cout << dec << i << endl;				//十进制
cout << oct << i << endl;				//八进制
cout << hex << i << endl;				//十六进制
cout << setiosflags(ios::uppercase);	//字母大写输出
cout << setbase(8) << i << endl;		//以n进制输出，n只能取 8,10,16
```



#### 控制输出位数

- 头文件：`<iomanip>` 

- 均采用**“四舍五入”**控制精度

- 默认浮点数输出有效位数是 **6** 位（若前面整数位数大于 **6** 位，使用科学计数法输出）

```c++
cout << setprecision(3) << i << endl;	//输出n位有效数
cout << fixed << setprecision(3) << i << endl;	//输出小数后n位
cout << setiosflags(ios::fixed);		//控制小数点后精度
```



#### 强制显示小数点和正负号

- 头文件：`<iomanip>` 

```c++
cout << setiosflags(ios::showpoint);	//输出小数点
cout << setiosflags(ios::showpos);		//输出正负号
```



#### 设置填充字符

- 头文件：`<iomanip>` 

```c++
cout << setfill('*') << setw(8) << 1.22 << endl;
//output: ****1.22
```



#### iomanip 用法总结

 地址：<https://blog.csdn.net/qq_35481167/article/details/82792103>





## 强制类型转换

使用：`cast-name<target_type>(expression);`

​		相较于 C 的类型转换，C++ 可以进行错误检查。如果 `target_type` 是引用类型，则转换结果是左值。

### static_cast

​		任何具有明确定义的类型转换，只要不包含底层const，都可以使用。当需要把一个较大的算术类型赋值给较小的类型时，`static_cast` 非常有用。还可以将 `void*` 转换为初始的指针类型，此时应确保指针的值不变。

### const_cast

​		改变运算对象的底层 `const`，一般用于改变指针或引用的 `const` 属性，从而间接修改指向的值。而变量本身的 `const` 属性是不能去除的，会产生未定义的结果。

### dynamic_cast

​		用于动态类型转换。只能用于含有虚函数的类，用于类层次间的向上和向下转化。只能转指针或引用。向下转化时，如果时非法的对于指针返回null，对于引用抛出异常。
​		**向上转换：**指的是子类向基类的转换。
​		**向下转换：**指的是基类向子类的转换。

### reinterpret_cast

​		几乎什么都可以转，可能会出问题，尽量少用。

### 旧式的强制类型转换

- `type (expr);`
- `(type) expr;`

​		如果旧式强制类型换成 `const_cast` 和 `static_cast` 也同样 **合法**，则其转换执行如同 `const_cast` 和 `static_cast`；若不合法，则其转换执行如同 `reinterpret_cast`。





##  `const` 和指针和引用

​		常量必须初始化。所谓指向常量的指针和引用，不过是它们自己觉得自己指向了常量，而可以通过直接修改指向的值来修改，初始化时没有要求一定要指向一个常量。

​		从右向左看，与标识符最近的符号，对变量的性质影响最大。**顶层const** 表示指针本身是否为常量，**底层const** 表示指针指向的值是否为常量。赋值时，需要考虑左右值是否都具有底层const，或者右值是否可以转换成const。

### 指针常量

`const int *p;`		`int const *p;`

- 本质是指针，指向了一个常量
- 可以更改指向，可以修改 `p`
- 不可以更改指向地址存放的值，不可以修改 `*p`
- 可以存 **非常量** 的地址

### 常量指针

`int *const p;`

- 本质是常量，所以必须 **初始化**
- 不可以更改指向，不可以修改 `p`
- 可以更改指向地址存放的值，可以修改 `*p`

### 指向常量的常指针

`const int* const p;`

- 不可以更改指向
- 不可以更改指向地址存放的值

### 常量引用

`const int& ref = 10`

- 用来修饰形参，防止误操作

- 编译器将代码修改为：

  ```c++
  int temp = 10;
  const int& ref = temp;
  ```

- ref 只读，不可修改

- 初始化时，对引用的对象是否为常量不作要求，所以，此时可以更改引用对象的值。

### `constexpr`

​		声明为 `constexpr` 的变量一定是一个常量，而且必须用常量表达式初始化。

#### 修饰指针

​		`constexpr` 只对指针本身有效，而与指针所指的对象无关。以下代码中，p是一个指针常量，而q是一个常量指针。

```c++
const int *p = nullptr;
constexpr int *q = nullptr;
```





## 内存四区

### 代码区

1. 存放CPU执行的**机器指令**
2. 共享，只读

### 全局区

1. 存放**全局变量**和**静态变量**
2. 还包含了**常量区**
3. 数据在程序结束后由OS释放

### 栈区

1. 存放**局部变量、函数的参数值**等
2. 由编译器自动分配释放

### 堆区

1. 利用 `new` 在堆区开辟内存
2. 利用 `delete` 手动释放内存
3. 由程序员分配释放，若程序员不释放，在程序结束时由操作系统回收



## 引用 `&`

1. 引用是数据的别名

2. 引用在定义时必须初始化

3. 一旦初始化后，不能再引用其它数据

4. 引用作函数的返回值

   - 不要做局部变量的引用

   ```c++
   int& test(){
       int a = 10;
       return a;
   }
   int main(){
       int &ref = test();
       cout << ref << endl;	//true,10; 编译器保留
       cout << ref << endl;	//false; 内存释放
       return 0;
   }
   ```
   
   - 函数的调用作为左值，必须返回引用
   
     ```c++
     int& test (){
         static int a = 20;
         return a;
     }
     int main () {
         int& ref = test ();
         cout << &ref << endl;	//003DA000
         cout << ref << endl;	//20
         test () = 1000;			
         cout << &test () << endl;//003DA000
         cout << ref << endl;	//1000
         return 0;
     }
     ```
   
5. 本质是指针常量

   ```c++
   //编译器发现引用，自动转换为 int* const ref = &a;
   void func (int& ref){
       ref = 100;
   }
   int main (){
       int a = 10;
       //编译器发现引用，自动转换为 int* const ref = &a;
       int& ref = a;
       //ref是引用，自动转换为 *ref = 20;
       ref = 20;
       cout << a << endl;		//20
       cout << ref << endl;	//20
       func(a);
       cout << a << endl;		//100
       cout << ref << endl;	//100
       return 0;
   }
   ```

6. 常量引用

   `const int& ref = 10`

   - 用来修饰形参，防止误操作

   - 编译器将代码修改为：

     ```c++
     int temp = 10;
     const int& ref = temp;
     ```

   - ref 只读，不可修改
   
   - 初始化时，对引用的对象是否为常量不作要求，所以，此时可以更改引用对象的值。



## 函数提高

### 默认参数

1. 可以不传递有默认值的参数

```c++
int func (int a, int b = 20, int c = 30){
    return a + b + c;
}
int main (){
    cout << func(10) << endl;	//60
}
```

2. 传递有默认值的参数时，覆盖默认值

   `func(10,30) == 70`

3. 有默认值的形参，必须在形参表中靠右

4. 函数声明和函数实现，只能一个有默认参数

   

### 占位参数

仅提供数据类型，可以有默认参数

```c++
void func (int a, int = 10){
    cout << “this is func" << endl;
}
int main (){
    func (10,10);
    return 0;
}
```



### 函数重载

满足条件：

1. 同一个作用域下
2. 函数名称相同
3. 返回类型相同
4. 函数参数类型不同，或个数不同，或顺序不同

```c++
void func (){
    cout << "func()" << endl;
}
void func (int a){
    cout << "func(int a)" << endl;
}
void func (double a){
    cout << "func(double a)" << endl;
}
int main (){
    func();
    func(10);
    func(3.14);
    return 0;
}
```



### 遇到引用和 `const`

```c++
void func (int& a){
    cout << "int& a" << endl;
}
void func (const int& a){
    cout << "const int& a" << endl;
}
int main (){
    int a = 10;
    func(a);	//调用 func1
    func(10);	//调用 func2
    return 0;
}
```

- 因为 a 是变量，10是常量
- 因为 `int& a = 10` 不合法，而 `const int& a = 10` 合法

- 由于函数在进行值传递时，需要重新分配内存并拷贝值，而当参数是复杂的结构体时，值传递就大大的浪费了空间。所以，可以使用传递 `const` 和引用，既保证了值不被修改，又保证了不浪费空间。



### 遇到默认参数

```c++
void func (int a, int b = 10){
    cout << "func(int a, int b = 10)" << endl;
}
void func (int a){
    cout << "func(int a)" << endl;
}
int main(){
    func(10);	//出错，二义性，func1,func2均可调用
    return 0;
}
```



## 类和对象

### 类的定义

- 举例

```c++
class Circle{
public:				//访问权限
    int r;
    double calCircumstance(){
        return 2 * r * 3.14;
    }
};
int main (){
    Circle c1;		//类的实例化
    c1.r = 10;
    cout << "周长为 " << c1.calCircumstance() <<endl;
    return 0;
}
```

- 注意：

  1. 类中若不声明访问权限，默认为 `private`
  2. 类中的属性和行为，统一成为成员
  3. 访问权限冒号以下，皆为权限范围内成员

  

### `sturct` 和 `class` 的区别

- `class` 默认权限是私有 `private`
- `struct` 默认权限是公共 `public`



### 封装

#### 访问权限

class 的成员默认权限是 private

| 访问权限  | 类外可否访问 | 子类可否访问父类 |
| :-------: | :----------: | :--------------: |
|  public   |     可以     |       可以       |
|  private  |    不可以    |      不可以      |
| protected |    不可以    |       可以       |

#### 成员属性设为私有

优点：

1. 可以自己控制读写权限

2. 对于写，可以检测数据的有效性

   - 通过 `setAttribution` 方法写入，控制数据范围

   - 而不是通过 `class.attribution` 直接写入

     ```c++
     class people{
     private:
         int age;
     public:
         void setAge (int a){
             if ( a>=0&&a<=150 )		//控制数据范围
                 age = a;
             else
                 cout << "写入失败" << endl;
         }
     }
     int main (){
         people stu;
         stu.age = 150;		//无法访问
         stu.setAge(150);	//写入失败
     	return 0;
     }
     ```

#### 友元 friend

全局函数、类，成员函数可以作为友元，可以访问类中的私有变量，写在类中

```c++
class building{
    //全局函数
    friend void func(building* b);
    //类
    friend class goodFriend;
    //成员函数
    friend void goodfd::vst();
public:
    building(){
        stRoom = "客厅";
        bdRoom = "卧室";
    }
private:
    string stRoom;
    string beRoom;
};
void func(buildind* b){
    cout << b->bdRoom << endl;
}
class goodFriend{
public:
    goodFriend(){
        b = new building;
    }
    void visit();
};
//类外定义成员函数，构造函数也可以
void goodFriend::visit(){
    cout << b->stRoom << endl;
}
class goodfd{
public:
    goodfd(){};
    void vst(){
        b = new building;
        cout << b->stRoom << endl;
    }
};
```





### 构造函数和析构函数

#### 构造函数

`类名 ( ) {}`

1. 函数名称与类名相同
2. 可以有参数，因此可以重载
3. 在对象实例化时，自动调用构造函数，且只会调用一次
4. 无参构造函数也叫默认构造函数

#### 析构函数

`~类名 ( ) {}`

1. 没有返回值，不写`void`
2. 函数名称与类名相同，在名称前加上 `~`
3. 不可以有参数，因此不可以重载
4. 在对象销毁前会自动调用析构函数，且只会调用一次

#### 注意

- 类中必须有构造函数和析构函数
- 若没有，编译器会自动补上构造和析构的空语句函数

#### 拷贝构造函数

````c++
Person (const Person& p) {
    cout << "copy" << endl;
    age = p.age;
}
````

- 以值传递方式作为函数形参时，会调用拷贝构造函数
- 当前类作为函数返回值时，会调用拷贝构造函数

#### 调用方式

1. 括号法

   ```c++
   Person p1;			//无参
   Person p2(10);		//有参
   Person p3(p2);		//拷贝
   ```

   - 调用默认构造函数时，不要使用 ( )

     如：`Person p1 ();`

     因为编译器会认为这是函数声明

2. 显示法

   ```c++
   Person p1;
   Person p2 = Person(10);		//等号右侧称为匿名对象
   Person p3 = Person(p2);
   ```

   - 当前行执行结束后，系统立即回收匿名对象

   - 不要利用拷贝构造函数，初始化匿名对象

     如：`Person (p3)`

     因为编译器会认为 `Person (p3) == Person p3`

3. 隐式转换法

   ```c++
   Person p4 = 10;		//相当于Person p4 = Person(10)
   Person p5 = p4;
   ```


#### 构造函数调用规则

默认情况下，c++编译器至少给一个类添加三个函数：

- 默认构造函数（无参）
- 默认析构函数（无参）
- 默认拷贝函数（拷贝属性值）

调用规则：

1. 若定义了有参构造，则C++不再提供默认无参构造，但会提供默认拷贝构造
2. 若定义了拷贝构造，则C++不再提供其他构造函数



### 深拷贝与浅拷贝

浅拷贝：简单的赋值拷贝操作

深拷贝：在堆区重新开辟空间，再进行赋值操作

- 编译器的拷贝构造函数，都是浅拷贝
- So，如果类中属性有在堆区开辟的，一定要以自提供拷贝构造函数，防止浅拷贝带来的问题



### 初始化列表

用于初始化类中的属性

```c++
class person {
public:
	int m_A;
	int m_B;
	person (int a, int b): m_A (a), m_B (b) {}
};
int main (){
    person p(10,20);
}
```

- 初始化列表中，属性值也可为常量

```c++
class person {
public:
	int m_A;
	int m_B;
	person(): m_A (10), m_B (20) {}
};
int main (){
    person p;
}
```



### 类对象作为类成员

- 先运行类成员的构造函数，再运行自己的构造函数

- 多个类成员，按代码行顺序运行

- 因为类的对象为本地变量，所以存放再栈区，先进后出运行析构函数



### 静态成员

####  特性

- 所有对象共享一份数据

- 在编译阶段分配内存

- 类内声明，类外必须初始化

  `int Person::m_A = 10`

- 静态成员函数只能访问静态成员变量

- 同样有访问权限

#### 访问方式

1. 通过类名访问

   `Person::m_A = 100;`

   `Person::func();`

2. 通过对象访问

   ```c++
   Person p;
   p.func();
   cout << p.m_A << endl;
   ```



### 类的存储

- 空对象占用1个字节，是为了区分每一个空对象在内存中的位置

- 类的大小为 **类中非静态成员变量大小的总和**



### `this` 指针

- 指向被调用的成员函数所属的对象

- 不需要定义，直接使用

- 本质是指针常量，指向不可以修改

- 当形参和成员变量同名时，可用 `this` 指针来区分

  ```c++
  class Person{
  public:
      int age;	//命名规范：int m_Age;
      Person (int age){
          this->age = age;
      }
  }
  
  ```

- 返回对象本身，用 `return *this`

  ```c++
  //返回对象本身，要引用
  //若以值返回，则会创建新对象
  Person& addAge (Person& p){
      this.age += p.age;
      return *this;
  }
  int main (){
      Person p1(10);
      Person p2(10);
      //链式编程思想
      p2.addAge(p1).addAge(p1).addAge(10);
      return 0;
  }
  ```

  

### 对象的空指针

- 空指针也可以访问成员函数

- 该成员函数中不可以有成员变量

  ```c++
  class Person{
  public:
      int age;
      void showMsg() {
          cout << "Message" << endl;
      }
      void showAge() {
          if ( this==NULL )	//保证代码健壮性
              return;
          cout << this->age << endl;
      }
  }
  int main() {
      Person* p = NULL;
      p->showMsg();	//ok
      p->showAge();	//wrong
      return 0;
  }
  ```

  

### `const` 修饰成员函数

#### 常函数

- 也称 **常量成员函数**
- 定义：**成员函数后**加 `const` 的函数
- 常函数内不可以修改成员属性
- 成员属性声明时加关键字 `mutable` 后，则可以在常函数中修改
- 隐式地将 `this` 指针转变为 **指向常量的指针**，即 **指针常量**

```c++
class person{
public:
    int m_A;
    mutable int m_B;
    void showPerson() const{
        this->m_B = 100;
        //this->m_A = 100;
    }
}
```

#### 常对象

- 定义：**声明对象前**加 `const` 的对象

  `A const a(3, 4);`

- 常对象只能调用常函数

- 在生存期内不能被更新，但必须被初始化



### 运算符重载

通过 `operator+(-/=/<</++/==/!=/())` 重载运算符

```c++
class person{
    friend ostream& operator<< (ostream &cout, person &p);
private:
    int m_A;
    int m_B;
public:
    person () {}
    person (int a, int b){
        this->m_A = a;
        this->m_B = b;
    }
    person operator+ (person &p){
        person tmp;
        tmp.m_A = this->m_A + p.m_A;
        tmp.m_B = this->m_B + p.m_B;
        return tmp;
    }
};
//重载左移要作为全局函数
ostream& operator<< (ostream &cout, person &p){
        cout << p.m_A << " " << p.m_B;
}

int main(){
    person p1(1,2);
    person p2(1,2);
    person p3 = p1 + p2;
    cout << p3.m_A << " " << p3.m_B << endl;
    cout << p << endl;
    return 0;
}
```

**注意：**

1. 不能重载内置类型的运算符，如 `int` 等
2. 不要滥用运算符重载
3. 单目运算符最好重载为成员函数，双目最好为友元函数。
4. =、[] 只能重载为成员函数，<< 和 >> 只能重载为友元函数。





## 继承

子类（派生类）继承父类（基类）



### 基本语法

```c++
class father{
public:
    int a;
};
class son: public father{
public:
    int b;
};
```



### 继承方式

| 继承方式 | 子类                                     |
| -------- | ---------------------------------------- |
| 公共继承 | 私有不可访问，其他权限不变               |
| 保护继承 | 公共和保护成员变为保护权限，私有不可访问 |
| 私有继承 | 公共和保护成员变为私有，私有成员不可访问 |



### 内存占用

子类会继承父类的所有非静态成员属性，不论权限。所以，子类的内存占用大小为 **父类的所有非静态成员属性 + 子类本身的非静态成员属性**。而父类中的私有成员属性，被编译器隐藏了，所以无法访问。



### 查看对象模型

利用开发人员命令提示工具查看对象模型

1. 开始菜单 -> VS 2019 -> 开发人员命令提示符

2. 跳转文件所在盘符

3. 跳转文件所在路径

4. 查看对象模型

   `cl /d1 reportSingleClassLayout类名 "文件名"`



### 父类和子类的构造顺序

​	顺序类似于堆栈，父类先进栈，最后出栈。

**构建子类对象时，**先调用父类的构造函数，再调用子类的构造函数。

**销毁子类对象时，**先调用子类的析构函数，再调用父类的析构函数。



### 继承同名成员

- 访问子类：直接访问    `son.m_A`
- 访问父类：加作用域    `son.father::m_A`

只要子类中出现父类的同名成员函数，即使参数不同，发生重载，编译器也会将父类中的所有同名函数隐藏，不可访问。要想访问，需要加作用域。

**静态同名成员**与非静态同名成员，访问方式相同。除了通过以上**对象的方式**访问，还可以通过**类名**访问。

- 访问子类：直接访问    `son::m_A`

- 访问父类：加作用域    `father::m_A`

- 通过子类访问父类：`son::father::m_A`

  表示：son 类中 father 作用域下的m_A变量。



###  多继承

**语法：**`class son: public father1, protected father2 {}`

- 实际开发中，不建议多继承。

多继承可能会引发父类中有同名成员出错，需要加作用域区分。



### 菱形继承

也称为**钻石继承**。继承关系如图所示。

<img src="E:\Practice\记录文档\c++笔记.assets\菱形继承.png" alt="image-20220329153659042" style="zoom:80%;" />

```c++
class Animal{
public:
    int age;
};
class Sheep: public Animal {};
class Tuo: public Animail {};
class SheepTuo: public Sheep, public Tuo {};
```

#### 产生的问题

- 羊继承了动物的数据，驼同样继承了动物的数据，当草泥马使用数据时，就会产生二义性。
- 草泥马继承自动物的数据继承了两份，其实我们应该清楚，这份数据我们只需要一份就可以。

#### 问题解决：虚继承

```c++
class Animal{
public:
    int age;
};
class Sheep: virtual public Animal {};
class Tuo: virtual public Animail {};
class SheepTuo: public Sheep, public Tuo {};
```

在继承的父类前加 **`virtual` 关键字**，使之成为虚继承，此时，被继承的父类称为**虚基类**。

虚继承仅继承下一份同名的基类成员和**虚基类指针（vbptr）**。虚基类指针指向虚基类表（vbtable)。虚基类表中记录了虚继承同名变量的偏移量，使得子类可以正确找到继承的唯一一份数据。

![image-20220329162446316](E:\Practice\记录文档\c++笔记.assets\虚继承.png)





## 多态

多态分为静态多态和动态多态。**静态多态**即重载，函数重载和运算符重载。**动态多态**是派生类和虚函数实现时多态。

**静态多态和动态多态的区别：**

| 多态     | 绑定函数地址 | 确定函数地址 |
| -------- | ------------ | ------------ |
| 静态多态 | 早绑定       | 编译阶段确定 |
| 动态多态 | 晚绑定       | 运行阶段确定 |



### 虚函数

函数重载是**早绑定**，在编译阶段就确定了函数地址。此时，doSpeak 传入的是 Animal。

```c++
class Animal{
public:
    void speak(){
        cout << "An Animail is speaking." << endl;
    }
};
class Cat: public Animal{
public:
    void speak(){
        cout << "A cat is speaking." << endl;
    }
};
void doSpeak (Animal &animal){
    animal.speak();
}
int main() {
    Cat cat;
    doSpeak(cat);		//An Animail is speaking.
    return 0;
}
```

**父类**中使用**虚函数**，**子类**可以实现动态多态，函数地址**晚绑定**，使函数地址在运行阶段确定。此时，doSpeak 传入的是 Cat。

```c++
class Animal{
public:
    virtual void speak(){
        cout << "An Animail is speaking." << endl;
    }
};
int main() {
    Cat cat;
    doSpeak(cat);		//A cat is speaking.
    return 0;
}
```



### 动态多态

函数地址**晚绑定**，使函数地址在运行阶段确定。父类的指针或引用，指向子类对象。



#### 满足条件

1. 有继承关系
2. 子类 **重写** 父类的虚函数，*virtual 可写可不写*

**重写：**函数名、返回值类型、参数列表 完全相同



#### 实现原理

​		父类内部存储 **vfptr(虚函数指针)** ，指向 **vftable(虚函数表)。**虚函数表内记录父类虚函数的地址。**子类**内部存储继承自父类的虚函数指针，指向父类的虚函数表，表内记录父类的虚函数地址。

​		当子类重写父类虚函数时，子类中的虚函数表会替换成子类重写父类虚函数的地址。**所以，**当父类指针或引用指向子类时，发生动态多态。

![image-20220331141721381](E:\Practice\记录文档\c++笔记.assets\动态多态原理.png)



### 虚析构函数

实现动态多态时，当子类中有属性在**堆区**中开辟了内存，而释放父类指针时，无法正常调用子类的析构函数，导致**内存泄漏**。

而虚析构可以解决这个问题。在父类析构函数前添加 `virtual` 关键字。

```c++
class base{
public:
    virtual callName () = 0;
}
class son: public base{
public:
    string *name;
    son (string n){
        name = new string(n);
    }
    callName (){
        cout << *name << endl;
    }
    ~son(){						//未调用
        if (name!=NULL){
            delete name;
            name = NULL;
        }
    }
}

int main(){
    base* b = new son("Tom");
    delete b;
	return 0;
}
```

**语法：**`virtual ~类名() {}`



### 抽象类

类中包含**纯虚函数** 或 **纯虚析构** 的类，称为抽象类。



#### 纯虚函数

语法：`virtual void speak (int a) = 0;`

#### 特点

- 无法实例化对象
- 子类必须重写抽象类的的纯虚函数，否则也属于抽象类



#### 纯虚析构函数

语法：`virtual ~类名() = 0;`    `类名::~类名() {}`

#### 特点

- 需要具体函数实现
- 虚析构或纯虚析构就是用来解决通过父类指针释放子类对象





## 模板

C++中的泛型编程思想，使代码复用率大大提升。



### 函数模板

```c++
template<typename T>	//声明模板，T为类型名
void mySwap (T &a, T &b){
    T tmp = a;
    a = b;
    b = tmp;
}
int main() {
    int a = 10, b = 20;
    mySwap(a,b);		//自动类型推导
    mySwap<int>(a,b);	//显示指定类型
}
```

**注意：**声明后必须紧跟 **一个** 函数模板



### 普通函数和函数模板

#### 隐式类型转换

1. 普通函数可以发生 **隐式类型转换**
2. 函数模板在 **自动类型推导** 时，不可以发生 **隐式类型转换**
3. 函数模板在 **显示指定类型** 时，可以发生 **隐式类型转换**



#### 调用

1. 当普通函数与模板函数相同时，优先调用普通函数

2. 若想强制调用函数模板，可以使用空模板参数

   `mySwap<>(a,b);`

3. 函数模板也可以发生 **重载**

4. 当函数模板更匹配参数时，优先调用函数模板



### 具体化模板

当传入的特殊参数类型会使模板函数错误时，使用具体化模板解决。

可以不用紧接着模板函数。

```c++
template<> void isEqual (person &a, person &b){
    //具体实现
}
```





### 类模板

类的模板，函数模板是函数的模板。

```c++
template<class NameType, class AgeType>
class person{
public:
    NameType m_Name;
    AgeType m_Age;
    person(NameType name, AgeType age){
        this->m_Name = name;
        this->m_Age = age;
    }
}
int main() {
    person<string,int> p1("aa",19);
    return 0;
}
```



**注意：**

1. 类模板中的成员函数，在**运行时**创建
1. 生成类模板对象时，必须显示指定数据类型
1. 类外实现成员函数时，作用域的类名后面必须加上模板参数 `person<NameType, AgeType>::person(){}`



### 类模板和函数模板的区别

- 类模板没有自动类型推导，必须指定类型

  `person p1("aa",11);    //wrong`

- 类模板的参数列表中，可以使用默认参数

  ```c++
  template<class NameType, class AgeType = int>
  class person{
      ...
  }
  int main() {
      //可以不指定具有默认参数的数据类型
      person<string> p("aa",11);
      return 0;
  }
  ```



### 类模板作为函数参数

示例中的类如上文所示。共三种方式，第一种最为常用。

#### 1 显示指定参数

```c++
void printPerson (person<string,int> &p){
    ...
}
```

#### 2 类模板直接传入

定义函数前，需要声明模板参数，让编译器知道T1，T2

```c++
template<class T1,class T2>
void printPerson (person<T1,T2> &p){
    ...
}
```

#### 3 将类模板作为模板传入

直接将类模板的类型作为 **模板**，*定义方法类似于函数模板*

```c++
template<class T>
void printPerson (T &p){
    ...
}
```



### 类模板与继承

当继承一个类模板时，可以使用两种方式。

- 显示指定父类模板的数据类型
- 继承时声明模板

```c++
template<class T>
class base {
    T m;
};
class son1: public base<int> {
    
};
template<class T1, class T2>
class son2: public base<T1> {
    T2 a;
}
int main() {
    son1 s1;
    son2<int,char> s2;	//此时，T1=T为int, T2为char
}
```











## 文件操作

头文件：`#inlcude <fstream>`

文件分为：文本文件和二进制文件



### 文本文件

#### 写文件

1. 包含头文件    `#inlcude <fstream>`
2. 创建流对象    `ofstream ofs;`
3. 打开文件       `ofs.open("文件路径", 打开方式);`
4. 写数据           `ofs <<  "写入的数据";`
5. 关闭文件       `ofs.close();`

#### 读文件

步骤大致如写文件。

打开文件要判断 **是否打开成功：**`ifs.is_open();`

文件流对象：`ifstream ifs;`

```c++
//first, devided by space
char buf[1024] = {0};
while ( ifs>>buf )
	cout << buf << " ";
//second, devided by line, includes space
char buf[1024] = {0};
while ( ifs.getline(buf,sizeof(buf)) )
	cout << buf << endl;
//third, devided by line, includes space
string buf;
while ( getline(ifs,buf) )
	cout << buf << endl;
```



### 二进制文件

#### **写文件：**

```c++
ofstream ofs;
ofs("person.txt",ios::out|ios::binary);
Person p = {"aa",18};
ofs.write((const char*)&p,sizeof(Person));
ofs.close();
```



#### **读文件：**

```c++
ifstream ifs;
ifs.open("person.txt",ios::in|ios::binary);
if ( !ifs.is_open() ){
    cout << "error" << endl;
}
Person p;
ifs.read((char*)&p,sizeof(Person));
cout << p.name << " " << p.age << endl;
ifs.close();
```



### 打开方式

| 打开方式    | 解释                         |
| ----------- | ---------------------------- |
| ios::in     | 读文件                       |
| ios::out    | 写文件                       |
| ios::ate    | 初始位置：文件末尾           |
| ios::app    | 追加方式写文件               |
| ios::trunc  | 如果文件存在，先删除，再创建 |
| ios::binary | 二进制方式                   |

注意：打开方式可以同时使用

用法：`ios::binary | ios::out`

**绝对路径的斜杠：`/`**







## 内联函数

​		当函数代码比较简单，又需要频繁调用时，系统开销会很大。此时可以使用内联函数，告诉编译器，将函数体插入代码中，从而减少开销。



### 使用规则

在函数返回值前加关键字 `inline`

```c++
inline int getMax( int a, int b ){
    return a>b ? a : b;
}
int main() {
    int a = 2, b = 3;
    cout << getMax(a,b) << endl;
    return 0;
}
```



### 为什么不使用宏定义

​		因为宏定义是在预编译阶段，单纯将代码段插入进主体，而不是像函数一样，进行传参操作。若使用不当，容易出现二义性。并且宏定义不会进行参数检查。

​		如：`#define getMax(a,b) ((a)>(b)?(a):(b))`











# STL

STL（Standard Template Library），**标准模板库**



## 基本概念

1. 从广义上分为，**容器（container），算法（algorithm），迭代器（iterator）**
2. 容器和算法之间，通过**迭代器**进行无缝连接
3. 六大组件：**容器、算法、迭代器、仿函数、适配器（配接器）、空间配置器**
   - **容器：**各种数据结构，如 *vector、list、deque、set、map*等
   - **算法：**常用算法，如 *sort、find、copy、for_each* 等
   - **迭代器**：连接容器和算法
   - **仿函数：**
   - **适配器：**
   - **空间配置器：**





## 容器 container

​		即常用数据结构：数组、链表、树、栈、队列、集合、映射表等。

容器分为**序列式容器**和**关联式容器**：

​	**序列式容器：**每个元素都有固定的位置，强调值加入的次序

​	**关联式容器：**二叉树结构，强调元素值的关系排序，元素没有固定的位置



### vector

- 容器：`vector`

- 算法：`for_each`

- 迭代器：随机访问迭代器

- 头文件：`#include <vector>`

类似于数组，底层是一段连续的内存空间。当容量满了之后，会进行 **动态扩展**，寻找更大的内存空间，然后将原数据拷贝新空间，释放原空间。

![image-20220405211601908](E:\Practice\记录文档\c++笔记.assets\vector.png)

#### 基本操作

- 创建：`vector<int> v;`    `vector<int> v(n);  //大小为n `

- 尾部插入元素：`v.push_back(10)`    `v.emplace_back(10);`

- 删除尾部元素：`v.pop_back()`

- 遍历容器：

  ```c++
  //first
  //起始迭代器，指向第一个元素
  vector<int>::iterator itBegin = v.begin();
  //结束迭代器，指向最后一个元素的下一个位置
  vector<int>::iterator itEnd = v.end();
  while ( itBegin!=itEnd ){
      cout << *itBegin << endl;
      itBegin++;
  }
  
  //second
  for ( vector<int>::iterator it=v.begin(); it!=v.end(); it++ )
      cout << *it <<endl;
  
  //third
  //#include <algorithm>
  void myPrint (int val){
      cout << val << endl;
  }
  for_each( v.begin(), v.end(), myPrint );
  ```

- 查看首个元素：`int first = v.front()`

- 查看末尾元素：`int last = v.back()`

- 访问 idx 所指数据：`v.at(idx)`    `v[idx]`

- 互换容器：`v.swap(v1);`

- 赋值： 

  `v1 = v2;`   

  `v1.assign(v2.begin(), v2.end());`  

   `v1.assign(n,elem);`

- 判断是否为空： `v.empty()`

- 容器的容量：`v.capacity()`

- 容器中元素的个数：`v.size()`

- 重新指定容器中的元素个数，而容量不变：`v.resize(100)`

  若容器变长，以默认值填充新位置；若容器变短，删除末尾元素

  若想以 elem 填充，则 `v.resize(100, elem)`

#### 和数组的区别

1. 数组是静态空间，而vector可以**动态扩展**
2. **动态扩展**是，寻找更大的内存空间，然后将原数据拷贝到新空间，释放原空间

#### 构造函数

- 默认构造函数：`vector<T> v;`
- 拷贝区间内的元素：`vector<int> v(v.begin(), v.end());`
- 拷贝 n 个 elem：`vector<int> v(n, elem);`
- 拷贝构造函数：`vector<int> v2(v1);`

#### 另外

1. 容量初始值为0，随着元素个数增多，**动态扩展**

2. `vector<int>(v).swap(v);` 收缩内存

   `vector<int>(v)` 是一个**匿名对象**， 通过 swap 交换容器，使 v 指向匿名对象，完成内存收缩，并回收多余空间

3. `reserve(int len);` 预留空间，减少动态扩展次数

   容器预留 `len` 个元素长度，预留位置不初始化，元素不可访问
   
4. `v.end()` 表示末尾元素的**后一位**，`v.rend()` 表示逆序末尾元素*（即首元素）*的**后一位*（即首元素的前一位）***。



### string

- 容器：`string`

- 头文件：`#include <string>`

string 本质是一个类，类内部封装了 `char*`

#### 构造函数

- 创建一个空串：`string();`
- 字符串初始化：`string (const char* s);`
- 拷贝构造函数：`string (const string& str);`
- 使用 n 个字符 c 初始化：`string (int n, char c);`

#### 基本操作

- 赋值：

  `string s = "hello";`

  `string s;	s.assign("hello");`

- 末尾拼接：

  `string s = "wo";	s += "c++";`

  `string s = "wo";	s.append("c++");`

  `string s = "wo";	s.push_back('c');`

  ```c++
  string s1 = "12";	
  string s2 = "3456";	
  s1.append( s2, 0, 3 );	将s2从位置0开始的3个字符拼接到s1后面
  ```

- 插入：`s1.insert (1, "00");    //在s1的第1位前插入"00"`  

- 删除：`s1.erase(1, 3);    //删除s1第1位起的3个字符`  

- 查找：

  ```c++
  string s = "asdfg";
  //int find(const string& str, int pos=0) const;
  //从s的第pos位开始查找str第一次出现的位置，默认第0位
  //找不到，返回0
  int pos = find(s);
  ```

  `rfind` 查找最后一次出现的位置

- 替换：

  ```c++
  string s = "abcdefg";
  //从s的第1位字符起的3个字符，替换成"1111"
  s.replace(1,3,"1111");
  //"a1111efg"
  ```

- 比较：`res = s1.compare(s2);`

- 读取单个字符：`str[i]`    `str.at(i)`

- 字符串大小：`str.size()`

  string 中结尾没有 `'\0'`，大小为字符个数

- 获取子串：`s2 = s1.substr( 1, 3 );  //s1从第1位开始3个字符大小的子串`



### list

- 迭代器：双向迭代器，只支持前移和后移

双向循环链表。插入操作和删除操作都不会造成原有list迭代器的失效，**这在vector是不成立的**。

![image-20220508152403526](E:\Practice\记录文档\c++笔记.assets\list.png)

#### 构造函数

- 默认构造：`list<T> lst;`
- 拷贝构造：`list<int> l1(l2);`
- 拷贝元素构造：`list<int> l1(l2.begin(), l2.end());`
- 初始化元素构造：`list<int> L(n,elem);`

#### 基本操作

- 赋值：`L1 = L2;`    `L1.assign(n, elem);`       `L1.assign(L2.begin(), L2.end());`

- 交换容器： `L1.swap(L2);`

- 元素个数：`L.size()`

- 容器是否未空：`L.empty()`

- 重新指定容器长度：`L.resize(num);`    `L.resize(num, elem);`

  默认以默认值填充，若容器变短，则删除超出长度的元素

- 插入元素：`push_back(elem)`    `push_front(elem)`    `insert(pos.elem)` 等

- 删除元素：`pop_back()`    `pop_front()`    `erase(pos) //返回下一个数据的位置`

- 删除所有与 elem 匹配的元素：`remove(elem)`

- 查看元素：`L.front()`    `L.back()`

- 反转：`L.reverse();`

- 排序：`L.sort();`





### stack

堆栈，**没有迭代器**

#### 构造函数

- 默认构造：`stack<T> stk;`   
- 拷贝构造：`stack(const stack &stk);`

#### 基本操作

- 添加元素：`push(elem);` 
- 弹出元素：`pop();` 
- 返回栈顶元素：`top(); `
- 是否为空：`empty();` 
- 返回栈的大小：`size(); `



### queue

队列，**没有迭代器**

![img](E:\Practice\记录文档\c++笔记.assets\queue.jpg)

#### 构造函数

- 默认构造：`queue<int> q;`
- 拷贝构造：`queue<int> q1(q2);`
- 使用第二个参数构造：

#### 基本操作

- 访问队首：`q.front()`
- 访问队尾：`q.back()`
- 入队：`q.emplace(1)`    `q.push(1)`
- 出队：`q.pop();`
- 返回元素个数：`q.size()`
- 队列是否为空：`q.empty()`
- 交换队列：`q.swap(q1);`



### deque

- 头文件：`#include <deque>`

- 迭代器：随机访问迭代器，只读

![clip_image002-1547547642923](E:\Practice\记录文档\c++笔记.assets\deque.jpg)

双端数组，头部插入和删除的效率比 vector 快，但是访问元素的速度比 vector 慢。deque 内部是一个 **中控器**，中控器中维护着缓冲区地址，缓冲区中存放着真实数据，使得使用 deque时像一段连续的内存空间。

![clip_image002-1547547896341](E:\Practice\记录文档\c++笔记.assets\中控器.jpg)

#### 构造函数

- 默认构造：`deque<T> d;`
- 拷贝构造：`deque<int> d1(d2);`
- 将 `[beg,end)` 区间内的元素拷贝：`deque<int> d1(d2.begin(), d2.end());`
- 拷贝 n个elem元素：`deque<int> d(10, 100);`

#### 基本操作

- 赋值： `d1 = d2;`    `d2.assign(d1.begin(), dd1.end());`    `d2.assign(10,100);`
- 是否为空：`deque.empty()`
- 元素个数：`d.size()`
- 重新指定容器长度（默认以0填充）：`d.resize(num);`    `d.resize(num, elem);`
- 两端插入：`push_back(elem)`  `push_front(elem)`  `pop_back()`  `pop_front()`
- 指定位置插入：`insert(pos, elem)`  
- 清空数据：`d.clear();`
- 清除数据：`erase(beg, end)`    `erase(pos)`
- 取值：`d.at(index)`  `d[index]`  `d.front()`  `d.back()`



### set

- 头文件：`#inlcude <set>`

set 中所有元素在插入时都会自动排序，属于**关联式容器**，底层结构使用 **红黑树** 实现。set 容器中不允许有重复元素。

#### 构造函数

- 默认构造：`set<T> st;`
- 拷贝构造：`set( const set &st);`

#### 基本操作

- 插入：`st.insert(10);`

  `pair<iterator,bool> insert (const value_type& val);`

  返回插入结果，表示插入是否成功

- 删除：

​		删除 `pos` 迭代器所指元素，返回下一个元素的迭代器

​		`it = erase(pos);`

​		删除区间元素，返回下一个元素的迭代器

​		`it = erase( beg, end );`

​		删除容器中值为 `elem` 的元素：`erase(elem);`

- 清空所有元素：`st.clear();`

- 返回元素数目：`st.size()`

- 容器是否为空：`st.empty()`

- 交换容器：`s1.swap(s2);`

- 查找 `key`：`st.find(key)`

  若存在，返回该 `key` 的迭代器；若不存在，返回 `set.end()`

- 统计 `key` 的个数：`st.count(key)`

#### 排序

​		set 容器默认从小到大排序

利用仿函数，自定义规则：

```c++
#include <set>
class myCmp{
public:
    bool operator()(int v1, int v2)
        return v1>v2;
};
int main(){
    set<int,myCmp> st;
    return 0;
}
```

**注意：**存放自定义类型时，必须指定排序规则，才可以插入数据



### map

头文件：`#include <map>`

形式：`map<key,value>`

迭代器：双向迭代器

底层由 **红黑树** 实现。存储的对组 **pair**。map 存储 key 和 value，为 **关联式容器**，按照 key 从小到大排序，key值是**唯一**的，不允许出现重复元素。



#### 基本操作

- 插入：`m.insert(pair<int,int>(1,10));`
- 拷贝构造：`map<int,int> m2(m);`
- 赋值：`m1 = m2;`
- 交换容器：`m1.swap(m2);`

##### 获取容器中的值

1. map 重载了 `[]` ，不可用数组下标访问元素，而是以 key 访问 `map[key]`。若无该 key，则返回 `0` 或 `""` （value的数据类型）

2. `[]` 可以添加新元素。若 `"abc"` 不存在，则添加对组 `{"abc", 10}`

   `myMap["abc"] = 10;`

3. `myMap.at("abc")`也可以取值。**不同于 `[]` 的是，若查找失败，不会添加新元素，而是会抛出 ·out_of_range` 异常



#### 注意

- map 中 key 值会被 `const` 修饰，不得被修改

- 可以自定义排序规则，C++提供了模板

  `map<int,string,greater<int>>`：从大到小排序

  `map<int,string,less<int>>`：从小到大排序
  
- 自定义排序规则如 set



#### unordered_map

`unordered<int,int> hashtable;`

即**哈希表**，内部元素无序

**操作：**

- `hashtable.find(5) `		查找指定元素；若没找到，返回`hashtable.end()`
- `hashtable[5]`                  访问元素





### 时间复杂度

**map, set, multimap, and multiset**

插入、查看、删除：O(logn)

**hash_map, hash_set, hash_multimap, and hash_multiset**

插入、查看、删除：O(1)，最坏情况 O(n)









## 算法 algorithm

​		用有限的步骤，解决问题，称为算法。

算法分为**质变算法**和**非质变算法**：

​		**质变算法：**改变元素内容的算法。如 拷贝、替换、删除等

​		**非质变算法：**不改变元素内容的算法。如 查找、计数、遍历、寻找极值等



### 函数对象

​		类中重载了 **函数调用`()`符号**，实例化后的对象，称为函数对象，**也称为仿函数**。函数对象可以和普通函数一样调用，可以作为参数传递，但是**本质上是一个类**，它可以拥有自己的状态。

#### 示例

```c++
class MyAdd {
public:
    int count;
    MyAdd() {
        //设置函数对象的状态
        count = 0;
    }
    int operator() (int v1, int v2) {
        count++;
        return v1 + v2;
    }
};
void printAdd (MyAdd &mp, int v1, int v2) {
    cout << mp(v1,v2) << endl;
}
int main() {
    MyAdd myAdd;
    //调用仿函数
    cout << myAdd(1,2) << endl;		
    cout << myAdd(2,3) << endl;
    cout << myAdd(3,4) << endl;
    //输出函数对象的状态
    cout << myAdd.count << endl;
    //函数对象作为参数传递
    printAdd(myAdd,4,5);
    return 0;
}
```



#### 谓词

当函数对象重载 `()` 的返回值为 `bool` 类型时，函数对象称为谓词。若仿函数接受一个参数，则称为一元谓词；若接受两个参数，则称为二元谓词。

谓词以匿名对象的形式，作为函数的形参

```c++
class MyCompare{
	bool operator() (int v1, int v2) {
        return v1 > v2;
    }  
};
int main() {
    vector<int> v;
    v.push_back(30);
    v.push_back(10);
    v.push_back(20);
    //自定义排序规则
    sort(v.begin(), v.end(), MyCompare());
    return 0;
}
```



### 内建函数对象

头文件：`#include <functional>`

内建函数对象是STL中的一些已经存在，可以使用的函数对象。



#### 算术仿函数

功能是实现四则运算。二元仿函数的数据类型相同，所以只需传递一个模板参数。

```c++
#include <functional>
int main() {
    plus<int> p;
    minus<int> m;
    multiplies<int> multi;
    divides<int> d;
    modulus<int> mod;
    negate<int> n;
    cout << p(1,2) << endl;
    cout << m(2,1) << endl;
    cout << multi(2,3) << endl;
    cout << d(6,2) << endl;
    cout << mod(9,2) << endl;  //取余
    cout << n(2) << endl;	//取相反数
}
```



#### 关系仿函数

功能是实现关系对比。返回值均为 bool。

- 等于：`equa_to<T>`
- 不等于：`not_equal<T>`
- 大于：`greater<T>`
- 大于等于：`greater_equal<T>`
- 小于：`less<T>`
- 小于等于：`less_equal<T>`



#### 逻辑仿函数

功能是实现逻辑运算，与或非。返回值均为 bool。

- 逻辑与：`logical_and<T>`
- 逻辑或 ：`logical_or<T>`
- 逻辑非：`logical_not<T>`



### 随机数种子

当使用 `rand()` 函数时，每次运行的随机数都是相同的。这时，需要添加随机数种子。

```c++
#include <ctime>
srand((unsigned int)time(NULL));
```

​		如此，每次运行产生的随机数都会根据时间变化。



### 常用遍历算法

头文件：`#include <algorithm>`

#### for_each

- 功能：遍历容器
- beg：起始迭代器
- end：末尾迭代器
- _func：函数或者函数对象，对每一个元素的操作
- 语法：`for_each(iterator beg, iterator end, _func);`
- **注意：遍历自定义类型数据时，_func必须为 *谓词* 。**

```c++
void func1(int elem);
class func2 {
public:
	void operator()(int elem) {
		cout << elem << " ";
	}
};
void func1(int elem) {
	cout << elem << " ";
}
int main() {
	vector<int> v;
	for (int i = 0; i < 10; i++) {
		v.emplace_back(i + 1);
	}
    //使用函数
	for_each(v.begin(), v.end(), func1);
    cout << endl;
    //使用函数对象
	for_each(v.begin(), v.end(), func2());
    cout << endl;
	return 0;
}
```

#### transform

- 功能：将源容器的元素搬运至目标容器

- beg1：源容器起始迭代器
- end1：源容器结束迭代器
- beg2：目标容器起始迭代器
- _func：函数或函数对象，使源容器中的元素经过 func 再搬运至目标容器
- 语法：`transform(iterator beg1, iterator end1, iterator beg2, _func);`
- **注意：搬运之前必须在目标容器开辟空间**

```c++
int func1(int elem) {
	return elem*2;
}
class func2 {
public:
	void operator()(int elem) {
		cout << elem << endl;
	}
};

int main() {
	vector<int> v;
	for (int i = 0; i < 10; i++) {
		v.emplace_back(i + 1);
	}
	vector<int> tv;
	//目标容器需要提前开辟空间
	tv.resize(v.size());
	transform(v.begin(), v.end(), tv.begin(), func1);
	for_each(tv.begin(), tv.end(), func2());
	return 0;
}
```



### 常用查找算法

头文件：`#include <algorithm>`

#### find

- 功能：在容器中查找指定元素。若找到，则返回元素的迭代器；若没找到，则返回结束迭代器。
- 语法：`find(iterator beg, iterator end, value);  `
- **注意：在查找 *自定义数据类型*  时，需要在类中重载 `==` 操作符，参数要加 `const`。**

```c++
#include <algorithm>
class Person {
public:
	Person(string name, int age) 
	{
		this->m_Name = name;
		this->m_Age = age;
	}
	//重载==，因为编译器无法直接比较自定义数据类型
	bool operator==(const Person& p) 
	{
		if (this->m_Name == p.m_Name && this->m_Age == p.m_Age) 
		{
			return true;
		}
		return false;
	}
    
	string m_Name;
	int m_Age;
};
int main() {
	vector<Person> v;
	for (int i=0; i<5; i++)
		v.emplace_back(Person("aa", 10+i));
    
	auto it = find(v.begin(), v.end(), Person("aa", 20));
	if (it != v.end()) {
		cout << (*it).m_Name << " " << (*it).m_Age;
	}else{
		cout << "Not Find!";
	}
	cout << endl;
	return 0;
}
```

#### find_if

- 功能：在容器中查找某个符合指定条件的元素。找到，则返回元素迭代器；没找到，则返回结束迭代器。
- 语法：`find_if(iterator beg, iterator end, _Pred);  `
- _Pred：函数或者谓词，表示查找条件。

#### adjacent_find

- 功能：查找相邻的重复元素。若找到，则返回相邻元素的第一个迭代器；若没找到，则返回结束迭代器。
- 语法：`adjacent_find(iterator beg, iterator end);  `
- 示例：1，2，3，3，2，5。在这个序列中查找，结果为3的第一个迭代器。

#### binary_search

- 功能：在 **有序** 容器中查找指定元素。若找到，返回 true；否则，返回 false。
- 语法：`bool binary_search(iterator beg, iterator end, value);  `
- value：指定元素，需要与容器类型相同。

#### count

- 功能：统计容器中存在指定元素的个数。
- 语法：`count(iterator beg, iterator end, value);  `
- **注意：在统计 *自定义数据类型*  时，需要在类中重载 `==` 操作符，参数要加 `const`。**

#### count_if

- 功能：有条件地统计元素个数。
- 语法：`count_if(iterator beg, iterator end, _Pred);  `
- _Pred：谓词或函数，表示统计的条件。



### 常用排序算法

头文件：`#include<algorithm>`

#### sort

- 功能：排序容器内元素
- 语法：`sort(iterator beg, iterator end, _Pred);  `
- _Pred：谓词或函数，表示排序规则。

#### random_shuffle

- 功能：将容器内的元素随机调整次序
- 语法：`random_shuffle(iterator beg, iterator end);  `

#### merge

- 功能：将两个 **有序** 容器合并，**有序** 存放到目标容器中。
- 语法：`merge(iterator beg1, iterator end1, iterator beg2, iterator end2, iterator dest);  `
- **注意：目标容器必须有足够空间**

#### reverse

- 功能：将容器中元素反转。
- 语法：`reverse(iterator beg, iterator end);  `



### 拷贝和替换算法

头文件：`#include <algotithm>`

#### copy

- 功能：将源容器区间内的元素拷贝至目标容器中
- 语法：`copy(iterator beg, iterator end, iterator dest);  `
- **注意：目标容器必须有足够空间**

#### replace

- 功能：将容器区间内的元素替换为新元素
- 语法：`replace(iterator beg, iterator end, oldvalue, newvalue);  `
- **注意：若元素类型为自定义数据类型，则必须在类中重载 `==` ；若新元素为自定义数据类型中的基本数据类型，则需要重载 `=` 操作符**

#### replace_if

- 功能：替换容器区间内满足条件的元素
- 语法：`replace_if(iterator beg, iterator end, _pred, newvalue);  `
- _Pred：谓词或函数，表示条件规则。
- **注意：若新元素为自定义数据类型中的基本数据类型，则需要重载 `=` 操作符**

#### swap

- 功能：互换两个容器的元素
- 语法：`swap(container c1, container c2);  `
- **注意：两个容器的类型必须完全相同。**



### 算术生成算法

头文件：`#include <numer>`

#### accumulate

- 功能：累加容器内元素，即求和
- 语法：`accumulate(iterator beg, iterator end, value);  `
- beg：起始迭代器
- end：结束迭代器
- value：累加的初始值

**另外，使用自定义类型时：**

需要定义一个函数对象或者普通函数，作为第四个参数传入。定义的函数需要两个参数，具体如下。

```c++
#include <numeric>
class person {
public:
	int age;
	static int cnt;
	person() {
		cout << "person: " << this->cnt++ << endl;
		this->age = 10 + cnt;
	}
};
int person::cnt = 0;
//定义函数对象或函数
class func2 {
public:
	int operator()(int init, person p) {
		return init + p.age;
	}
};
int func1(int init, person p) {
	return init + p.age;
}

int main() {
	vector<person> p(10);
	cout << accumulate(p.begin(), p.end(), 0, func1) << endl;
    cout << accumulate(p.begin(), p.end(), 0, func2()) << endl;
	return 0;
}
```

#### fill

- 功能：填充元素
- 语法：`fill(iterator beg, iterator end, value);  `
- beg：起始迭代器
- end：结束迭代器
- value：填充的元素，类型必须与容器相同或者可以进行强制类型转换的类型



### 常用集合算法

头文件：`#include <algorithm>`

#### set_intersection

- 功能：求两个 **有序** 容器的交集，**使用前需要开辟足够空间**
- 语法：`set_intersection(iterator beg1, iterator end1, iterator beg2, iterator end2, iterator dest);  `
- beg1,end1：容器1的起始和结束迭代器
- beg2,end2：容器2的起始和结束迭代器
- dest：目标容器的起始迭代器
- 返回值：指向交集 **末尾元素** 的后一个元素的迭代器



#### set_union

- 功能：求两个 **有序** 容器的并集，**使用前需要开辟足够空间**
- 语法：`set_union(iterator beg1, iterator end1, iterator beg2, iterator end2, iterator dest);  `
- 用法均与 `set_intersection` 类似



#### set_difference

- 功能：求两个 **有序** 容器的差集，**使用前需要开辟足够空间**
- 语法：`set_difference(iterator beg1, iterator end1, iterator beg2, iterator end2, iterator dest);  `
- 用法均与 `set_intersection` 类似





## 迭代器 iterator

​		提供一种方法，使之可以依序访问容器中的各个元素，而又无需暴露该容器的内部表达方式。**算法通过迭代器访问容器中的元素。**迭代器的使用类似于指针。

​		每个容器都有自己专属的迭代器。

​		常用的迭代器为：**双向迭代器**和**随机访问迭代器**

| 种类         | 功能                       | 支持运算                      |
| ------------ | -------------------------- | ----------------------------- |
| 输入迭代器   | 对数据只读                 | ++、==                        |
| 输出         | 对数据只写                 | ++                            |
| 前向         | 读写，并能向前推进         | ++、==、！=                   |
| **双向**     | 读写，并能向前向后操作     | ++、--                        |
| **随机访问** | 读写，可以跳跃访问任意数据 | ++、--、[n]、-n、<、<=、>、>= |







# C++ 11 新特性

https://www.nowcoder.com/discuss/840768?toCommentIpt=1



## array 容器

​		序列式容器，定义时必须指定大小，且大小固定，大小是容器类型的一部分。

定义：`array<int, 10> arr;`





## 智能指针

头文件：`#include <memory>`

​		为防止没有及时有效地释放已分配的内存，造成内存泄露，引入了智能指针。智能指针指向动态分配的内存，能够确保变量在离开作用域时，自动销毁指针。智能指针的**核心实现技术**是引用计数。



#### unique_ptr

独占的智能指针。`unique_ptr` 类型的指针指向的内存，只能由该指针管理。所以 **引用计数** 只能为1。



#### shared_ptr

共享的智能指针。多个智能指针可以同时管理一块内存，引用计数为零时，销毁指针。



#### weak_ptr

弱引用的智能指针。它不共享指针，不能操作内存，是用来监视 `shared_ptr` 的。





## emplace_back

容器的插入函数，相较于 `push_back()` 的效率更高



### 相比于push_back

`push_back()`：先调用 **构造函数** 创建一个新元素的临时对象，再调用 **移动构造函数** 移动到容器中

`emplace_back()`：在原地调用 **构造函数** 创建新元素，省去了调用移动构造函数的过程





## const 和 constexpr





## auto 和 decltype

​		二者都是类型说明符，自动推导变量的类型。

### auto

`auto b = 3.14;`

`auto a = 2;`

​		auto 一般会忽略掉顶层 const，会自动将推导的数组类型转换为指针类型



### decltype

`decltype(f()) sum  = x;		sum的类型的 f()的返回类型`

​		`decltype` 不会忽视变量的顶层 const和引用。`decltype` 的表达式，如果外层加了括号，则会得到引用类型。如果表达式是解引用操作，那么也会得到引用类型。
