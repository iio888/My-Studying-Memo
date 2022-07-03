# 1 理解网络编程和套接字

​		网络编程就是编写程序使两台计算机交换数据。套接字（socket）是网络传输用的 **软件设备**，由 **操作系统** 提供。两台计算机通过套接字进行通信。

​		套接字分为两种，一种是TCP套接字，另一种是

​		以下程序由 **C语言** 编写。





## TCP套接字

​		TCP套接字类似于电话机，而通信就类似于打电话。接电话的是服务端，打电话的是客户端。接打电话与计算机收发消息，有类似的步骤。

​		**socket相关函数包含于头文件 `#include <sys/socket.h>`**



### 服务端

​		服务端可以类比为接电话的一端。大致步骤如下：

#### 1 调用 socket 函数

== 准备电话机，创建一个套接字

```c
int socket(int domain, int type, int protocol);
```

成功时，返回文件描述符；失败时，返回 -1

#### 2 调用 bind 函数

==分配电话号码，给创建好的套接字分配IP地址和端口号

```c
int bind(int sockfd, struct sockaddr *myaddr, socklen_t addrlen);
```

成功时，返回 0；失败时，返回 -1

#### 3 调用 listen 函数

==连接电话线，使服务端处于监听状态，可接受连接请求

```c
int listen(int sockfd, int backlog);
```

成功时，返回 0；失败时，返回 -1

#### 4 调用 accept 函数

==拿起话筒，接受了对方的连接请求

```c
int accept(itn sockfd, struct sockaddr *addr, socklen_t *addrlen);
```

成功时，返回文件描述符；失败时，返回 -1



### 客户端

​		客服端可以类比为打电话的一端，第一步和服务端相同，都需要创建套接字。客户端的程序相比服务端较为简单，只有两步。第二步需要向服务端发送连接请求。

```c
int connect(int sockfd, struct sockaddr *serv_addr, socklen_t addrlen);
```

成功时，返回 0；失败时，返回 -1



### 文件操作

​		在Linux中，socket被认为是文件的一种，所以使用socket的时候，可以使用文件I/O的相关操作；而在Windows中，socket与文件需要加以区分，所以在Windows下需要调用特殊的数据传输相关的函数。



#### 文件描述符

​		文件描述符其实就是，操作系统给文件和套接字起的别名，它是一个整数，为了方便称呼而存在。而在Windows中，文件描述符被称为 **文件句柄**。



#### 打开文件

- path：文件名的字符串地址
- flag：文件的打开模式

```c
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>

int open(const char *path, int flag);
```

| 打开模式 | 含义                     |
| -------- | ------------------------ |
| O_CREAT  | 必要时创建文件           |
| O_TRUNC  | 删除全部现有数据         |
| O_APPEND | 维持现有数据，保存到末尾 |
| O_RDONLY | 只读打开                 |
| O_WRONLY | 只写打开                 |
| O_RDWR   | 读写打开                 |



#### 关闭文件

- fd：文件描述符（文件或套接字）

```c
#include <unistd.h>
int close(int fd);
```

成功时，返回 0；失败时，返回 -1



#### 将数据写入文件

- fd：文件描述符
- buf：传输数据的缓冲地址值
- nbytes：传输数据的字节数
- `ssize_t` 是 `typedef` 声明的 `signed int` 类型
- `size_t` 是 `typedef` 声明的 `unsigned int` 类型

```c
#include <unistd.h>
ssize_t write(int fd, const void *buf, size_t nbytes);
```

成功时，返回写入的字节数；失败时，返回 -1。`ssize_t` 和 `size_t` 来源于头文件 `#include <sys/types.h>` 的声明。



#### 读取文件中的数据

- fd：文件描述符
- buf：接受数据的缓冲值地址
- nbytes：接受数据的最大字节数

```c
#include <unistd.h>
ssize_t read(int fd, void* buf, size_t nbytes);
```

成功时，返回接受的字节数，或者遇到文件末尾，返回 0；失败时，返回 -1



## Windows下套接字

​		Windows套接字一下简称 Winsock



### 准备工作

#### 1 链接 `ws2_32.lib` 库

- 项目的属性页 -> 输入 -> 附加依赖项
- 输入 `ws2_32.lib`



#### 2 初始化 Winsock

- wVersionRequested： Winsock 版本信息
- lpWSAData： WSADATA 结构体变量的地址值

```c
#include <winsock2.h>
int WSAStartup(WORD wVersionRequested, LPWSADATA lpWSAData);
int main() {
    WSADATA wsaData;
    ...
    if (WSAStartup(MAKEWORD(2,2), &wsaData) != 0)
        ErrorHandling("WSAStartup() error!");
    ...
    return 0;
}
```

成功时，返回 0；失败时，返回非零的错误代码值。调用完函数，相应参数中将填充已初始化的库信息。

##### WORD 类型的参数

- WORD类型是通过 `typedef` 声明的 `unsigned short` 类型

- 该参数表示版本信息
- 若版本为 1.2，则传递的值为 `0x0201`；若版本为 2.2，则传递的值为 `0x0202`
- 借助 `MAKEWORD` 宏函数构建 WORD：`MAKEWORD(1, 2);` 为版本 1.2

##### LPWSADATA 类型的参数

- LPWSADATA类型是WSADATA的指针类型
- 无特殊意义，但必须传递



#### 3 注销该库

```c
#include <winsock2.h>
int WSACleanup(void);
```

成功时，返回 0；失败时，返回 SOCKET_ERROR



### 套接字相关函数

```c
#include <winsock2.h>
SOCKET socket(int af, int type, int protocol);		// ==socket() of Linux
//成功时，返回套接字句柄；失败时，返回INVALID_SOCKET
int bind(SOCKET s, const struct sockaddr* name, int namelen);	
//成功时，返回0；失败时，返回SOCKET_ERROR
int listen(SOCKET s, int backlog);		
//成功时，返回0；失败时，返回SOCKET_ERROR
SOCKET accept(SOCKET s, struct sockaddr* addr, int* addrlen);
//成功时，返回套接字句柄；失败时，返回INVALID_SOCKET
int connect(SOCKET s, const struct sockaddr* name, int namelen);
//成功时，返回0；失败时，返回SOCKET_ERROR
int closesocket(SOCKET s);		// ==close() of Linux
//成功时，返回0；失败时，返回SOCKET_ERROR
```



### Winsock 数据传输函数

Windows下的套接字 I/O 函数和文件 I/O 函数严格区分。而在Linux中，套接字可以用文件I/O函数进行数据传输。

#### 发送数据

- SOCKET s：数据传输对象的 **套接字句柄**
- const char* buf：待传输数据的缓冲地址
- len：传输的字节数
- flags：多种选项信息

```c
#include <winsock2.h>
int send(SOCKET s, const char* buf, int len, int flags);
```

#### 接收数据

- 参数信息与 `send()` 类似

```c
#include <winsock2.h>
int recv(SOCKET s, const char* buf, int len, int flags);
```







# 2 套接字类型与协议设置

```c
#include <sys/socket.h>
int socket(int domain, int type, int protocol);
```

- domain：协议族信息
- type：套接字类型
- protocol：协议信息



## 协议族

​		套接字中的协议分类信息，成为 **协议族**，可以分为如下几类

| 名称      | 协议族               |
| --------- | -------------------- |
| PF_INET   | IPv4 互联网协议族    |
| PF_INET6  | IPv6 互联网协议族    |
| PF_LOCAL  | 本地通信的UNIX协议族 |
| PF_PACKET | 底层套接字的协议族   |
| PF_IPX    | IPX Novell 协议族    |

​		最常用的是 **IPv4协议族**，即 PF_INET协议族。这一项协议族参数，决定了第三个参数 *protocol* 的范围。最终套接字使用的协议取决于第三个参数 *protocol*。



## 套接字类型

​		套接字类型即数据的传输方式。由于协议族中还包含了多种数据传输方式，所以需要第二项参数指出，具体使用哪种数据传输方式。以下是两种具有代表性的数据传输方式。



### 1 面向连接的套接字

​		即 `int type = SOCK_STREAM`。收发数据的套接字内都有缓冲（字节数组），数据像是在传送带上传输一样，按序传递，按序到达。到达的数据将保存在缓冲中，只要数据没有超过缓冲的容量，就不一定会调用 `read()` 函数读取缓冲数组。当缓冲数据满了，套接字就停止传输数据，这样就不会导致数据丢失。若传输数据过程中出错，套接字还提供了重传服务。面向连接的套接字只能与另外一个同是面向连接的套接字连接。**因此，面向连接的套接字是**

- 可靠的
- 按序传递的
- 基于字节的
- 必须一一对应的**套接字**



### 2 面向消息的套接字

​		即 `int type = SOCK_DGRAM`。面向消息的数据传输就类似于骑摩托车送快递，强调速度而非顺序，这种方式发送数据具有损坏或丢失的风险。送出去的数据包裹有大小限制，大型数据包裹需要分批次发送。如果用两辆车分别发送数据，则同样要分两次接收数据。**因此，面向消息的套接字是**

- 不可靠的
- 不按序传递的
- 以高速传输为目的的**套接字**



## 最终协议选择

​		在 *同一协议族中存在多个数据传输方式相同的协议* 时，需要通过第三个参数，指定具体协议信息。多数情况下，协议是唯一的，可以传递 0。**IPv4协议族** 中，面向连接的协议只有 TCP，面向消息的协议只有 UDP。

- IPv4协议族中面向连接的套接字

  `int tcp_socket = socket(PF_INET, SOCK_STREAM, IPPROTO_TCP);`

- IPv4协议族中面向消息的套接字

  `int udp_socket = socket(PF_INET, SOCK_DGRAM IPPROTO_UDP);`



## Win平台下socket函数

​		该函数的参数种类和含义与Linux的socket函数完全相同，只有 **返回值类型** 稍有不同。

```c
#include <winsock2.h>
SOCKET socket(int af, int type, int protocol);
```

- 成功时，返回套接字句柄；失败时，返回 `INVALID_SOCKET`

​		返回类型 `SOCKET` 是保存套接字句柄的一个整型类型，实际上可以用 `int` 型接收，但是考虑到之后的扩展性，建议使用 `SOCKET` 来作为返回值的类型。同样地，失败返回值 `INVALID_SOCKET` 实际上值为 -1。







# 3 地址族与数据序列

​		本章将着重讲解给套接字分配IP地址和端口号的方法。



## IP地址和端口号

​		IP地址指向了数据该分配给哪台计算机，端口号指向了具体接收或发送的应用程序。



### IP地址

​		IP地址分为 IPv4 和 IPv6，目前广泛使用的是 IPv4。IPv4 包含4字节，IPv6 包含16字节。IPv6 是为解决 IPv4 地址耗尽问题而诞生的。下面所说的IP地址，均指 IPv4。

​		IP地址分为A, B, C, D四类。一个IP地址中，包含了网络号和主机号。发送数据时，首先通过网络号确定要发送的网络，再通过主机号确定要发送的具体的计算机。在网络中实现传递数据的具体物理设备，是路由器或交换机。

- A类地址的首字节范围：0 - 127
- B类地址的首字节范围：128 - 191
- C类地址的首字节范围：192 - 223



### 端口号

​		计算机中一般配有 NIC（Network Interface Card，网络接口卡），用于向计算机内部传递数据。数据通过NIC从外界到达计算机内部，计算机的操作系统根据NIC传递过来的数据，找到端口号，发送给相应的应用程序。

​		端口号就是在同一操作系统内为区分不同套接字而设置的，因此一个端口号一次只能分配给一个应用程序。端口号由16位构成，范围是 0-65535，但是，0-1023 是知名端口号，一般分配给特定的应用程序，个人使用时，应当分配此范围外的端口号。**另外，**TCP套接字和UDP套接字不会共用端口号，因此可以重复使用同一个端口号。



## 地址信息的表示

​		应用程序内以 **结构体** 的形式，定义地址信息，如下所示。

```c
struct sockaddr_in {
    sa_family_t 	sin_family;		//地址族
    struct in_addr  sin_addr;		//32位IP地址
    uint16_t 		sin_port;		//端口号
    char 			sin_zero[8];	//不使用
};
struct in_addr {
    in_addr_t		s_addr;			//32位IPv4地址
};
```

​		如上的一些以 `_t` 结尾的数据类型可以参考POSIX标准。POSIX标准是为UNIX系列操作系统设立的标准，它定义了一些其他数据类型，具体如下表所示。

| 数据类型名称 | 数据类型说明             | 声明的头文件 |
| ------------ | ------------------------ | ------------ |
| int8_t       | signed 8-bit int         | sys/types.h  |
| uint8_t      | unsigned 8-bit int       | sys/types.h  |
| int16_t      | signed 16-bit int        | sys/types.h  |
| uint16_t     | unsigned 16-bit int      | sys/types.h  |
| int32_t      | signed 32-bit int        | sys/types.h  |
| uint32_t     | unsigned 32-bit int      | sys/types.h  |
| sa_family_t  | 地址族（address family） | sys/socket.h |
| socklen_t    | 长度（length of struct） | sys/socket.h |
| in_addr_t    | IP地址，声明为 uint32_t  | netinet/in.h |
| in_port_t    | 端口号，声明为 uint16_t  | netinet/in.h |



### sin_family

| 地址族   | 含义                           |
| -------- | ------------------------------ |
| AF_INET  | IPv4网络协议中使用的地址族     |
| AF_INET6 | IPv6网络协议中使用的地址族     |
| AF_LOCAL | 本地通信中采用的UNIX协议地址族 |

### sin_addr

​		32位IP地址，以 **网络字节序** 保存。类型为32位无符号整型。

### sin_port

**16位端口号**，以网络字节序保存。

### sin_zero

​		无特殊含义，存在的意义为了使结构体 `sockaddr_in` 与结构体 `sockaddr` 的大小保持一致，并且 **必须填充为0**。结构体 `sockaddr`并非只为IPv4所设计，因此要在 `sin_family` 中指出地址族信息。而结构体 `sockaddr_in` 为了与其保持一致，也需要在`sin_family` 中指出地址族信息。

​		因为在 `bind()`中传递的第二个参数的类型是 `sockaddr`，所以需要大小保持一致。

```c
struct sockaddr {
	sa_family_t		sin_familt;		//地址族
    char			sa_data[14];	//地址信息
};
```

- `sa_data` 保存IP地址和端口号，剩余部分填充为0。

