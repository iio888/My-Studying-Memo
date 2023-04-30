

# Linux Shell





## 常用命令



### 1 基本操作

#### 1.1 查看当前路径

`pwd`

#### 1.2 查看目录下的所有文件

- 查看当前目录

`ls`		`ls -l`

- 查看指定目录下的文件

`ls Code2`

#### 1.3 终端移动至目录下

支持绝对路径、相对路径

`cd /home/csdn/web`		`cd web`

返回上一级目录

`cd ..`

#### 1.4 新建一个文件

若 `file.c` 存在，则打开；若不存在，则新建。

`touch file.c`

#### 1.5 删除文件或目录

- 删除文件

`rm file.c`

- 删除目录

`rmdir web`		`rm -d web`

#### 1.6 复制文件或目录

- 复制文件

`cp file.c file2.c`

- 复制目录

`rm -r Code Code2`

#### 1.7 重命名

`mv Code Code2`		`mv 1.txt 2.txt`	

#### 1.8 查看文件内容

- `cat passwd`			`cat -n passwd`

`-n` 查看内容会标序号

- `haed passwd`			`head -n 8 passwd`

`head` 默认查看前十条内容，`tail` 用法于 `head` 一致

- `more +100 /etc/locale.gen`

  从第100行开始逐行显示，`more` 是逐行显示

- `less /etc/locale.gen`

  `less` 支持上下滚动查看内容

#### 1.9 查找文件

```git
#find 搜索范围 选项 目标文件
find /etc/ -name passwd

#find 目录 -user 指定用户名
find /etc  -user root

#find 目录 -size 文件大小
find /etc -size 1M
```

#### 1.10 创建链接

```git
# ln -s 原文件或目录 软链接名
ln -s /usr /home/csdn/myusr
```

`-s` 表示软链接

#### 1.11 压缩文件

- 压缩为 gzip/gz 格式，原地打包

`gzip edu.txt`

- 压缩为 tar 格式，新建压缩包

`tar -cvzf 1.tar 1.txt 2.txt`

#### 1.12 解压文件

- 解压 gz 格式

`gunzip edu.txt.gz`

#### 1.13 移动文件

`mv index.txt Code2/`

#### 1.14 下载工具

`apt`

#### 1.15 搜索

##### find

搜索文件属性

##### grep

搜索文件内部的内容

`grep "搜索的内容" 搜索路径/文件 参数`

- `-r`：基于目录搜索，递归搜索，必须添加
- `-i`：忽略大小写差别
- `-n`：标出行号

#### 1.16 防火墙

查看防火墙状态：`sudo ufw status  verbose`

关闭防火墙：`sudo ufw disable`

打开防火墙：`sudo ufw enable`



### 2 快捷操作

- 将输入流重定向 `<`
- 将输出流重定向 `>`
- 将错误流重定向 `2>`
- 追加 输出流重定向 `>>`

#### 特殊字符 `$`

- 上条命令的第一个参数 `$_`
- 上条命令的错误代码（命令返回值） `$?`
- 程序的第一项参数 `$1`
- 程序的文件名 `$0`
- 给定参数的个数 `$#`
- 当前命令的进程ID `$$`
- 展开所有参数 `$@`



- 上一级目录 `..`
- 当前目录 `.`
- `" "` 与 `' '`
  - 双引号内替换变量，而单引号不替换



### 3 脚本

​		脚本的第一行是 `shebang`，表明需要用哪个程序运行该脚本，脚本必须包含 `shebang`。`shebang` 的开头必须是 `#!`，之后跟上运行程序的路径，例如 `#!/bin/bash` 和 `#!/usr/local/bin/python`。若不知道具体的路径，可以使用 `env` 命令，它会利用 **环境变量** 中的程序来解析该脚本，例如 `#!/usr/bin/env python`。





## 初始操作



### 共享文件夹

**步骤：**虚拟机 -> 设置 -> 选项 -> 共享文件夹

**位于：**`/mnt/hgfs/自定义文件夹`



### 远程连接

Xshell 用于远程连接服务器，Xftp用于可视化传输文件

1. 下载 xshell,

   官网*（外网）*下载 **教育版**，下载链接会发送至邮箱

2. 在虚拟机终端输入 `ifconfig` 获得IP地址

   若找不到该命令，`sudo apt install net-tools`

   即，**第二行 `inet` 后的IP地址**

3. `ps -e | grep ssh`

   若没有看到 `sshd`，则 `sudo service sshd start` 或者 `sudo /etc.init.d/ssh start`

   若没有安装，则 `sudo apt install openshh-server`

4. 启动 Xshell，设置 *会话* 的属性

   将主机号改为 *第二步* 获得的IP地址

   ![image-20220529201506444](E:\Practice\记录文档\Linux.assets\image-20220529201506444.png)

5. 点击对话框右下角 *连接*，并输入用户名和密码

6. 完成连接





## Tools



### 1 tldr

解释命令，相比于 `man` 和 `--help`，提供了具体例子，更容易理解。

- `sudo apt-get install tldr`



### 2 查看目录

- `tree`

- `broot`

- `nnn`

  交互式



### 3 查找文件

#### 查找文件名称

- `find`

  - `find -name`

    搜索文件名

  - `find -type`

    搜索对应类型文件

  - `find -path '**/test/*.py'`

    搜索路径名

  - `find . -name "*.tmp" -exec rm {} \;`

    搜索指定文件，并执行命令
    
  - `find . -type f`

    按普通格式类型查找
    
  - `find -print0`

    显示文件路径

- `locate`

  定位文件路径

  `updatedb`

  更新数据库

- `fd ".*py"`

  查找文件名，支持[[正则表达式]]

#### 查找文件内容

- `grep`

  搜索文件内容

  - `grep -R`

    递归搜索文件内容
    
  - `-E`  

  - `-v "REGEX"`  排除指定条件

- `rg "import" -t py ~/scratch`

  查找指定文件类型中，包含指定文本的文件。

  会详细显示路径，及指定文本的所在行的所有内容。

  引号内支持 **正则表达式**

  - `rg -C 5`

    显示附近5行内容

  - `rg -u`

    不忽略隐藏文件

  - `rg --files-without-match`

    找到不匹配的文件

- `sed`  流编辑器

  利用简短的命令来修改文件，最常用的是 `s`，即替换命令，支持正则表达式

  `sed 's/REGEX/SUBSTITUTION/'`

  - `-E`  完全支持正则
  - `\1`、`\2` 表示捕获组1、2



### 4 定位 bash/sh 脚本错误

`shellchech`



### 5 查找历史命令

- `history`

- `CTRL + R`

  对历史记录进行逆序搜索

- `fzf`

  模糊查找

- 在命令的开头加上一个空格，该条命令不会被加进shell记录中

  - 在`.bashrc` 中添加 `HISTCONTROL=ignorespace`
  - 向`.zshrc` 添加 `setopt HIST_IGNORE_SPACE`



### 6 安装包管理器

#### Homebrew

安装程序不用使用 `sudo`

**安装：**

`/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"



### 其他小工具

- `less`

  文件分页器，可以通过翻页的方式，浏览较长的文本

- `sort`

  对输入的数据进行排序

  - `-n`  按照字典序排序

  - `-k1,1`  仅基于以空格分割的第一列进行排序

    `1,1`  表示从第一列到第一列

  - `-r`  倒序排序

- `uniq -c`

  将连续出现的行折叠，并将 **出现次数** 作为前缀

- `paste`

  - `-s`  合并命令行
  - `-d,`  以指定分隔符 *（这里是 `,`）*进行分割

- `awk`

  `awk '$1 == 1 && $2 ~ /^c[^ ]*e$/ { print $2 }' a.txt`

  接受一个模式串（可选）以及一个代码块，指定当模式匹配时，应该做何种操作

  - `$0` 表示所有列，`$1` 到 `$n` 表示第n列
  - 列的分割默认基于空格，可以通过 `-F` 来修改
  - **替换：**`awk '{ sub(/AAAA/,"BBBB"); print $0 }' a.txt`

- `bc`

  计算输入流

- `wc -l`

  输出结果的行数









# gcc 编译器

一个程序从源代码到可执行程序，需要经过：**预处理** -> **编译** -> **汇编** -> **链接**



## 基本操作

`gcc hello.c` 默认生成 `a.out` 可执行文件，**编译+链接**



### 执行可执行程序

**命令：**`./a.out`



### 预处理

将 `#include` 的内容展开，进行宏替换，去掉注释行。

**命令：**`gcc -E hello.c > include.txt`



### 编译

生成目标文件 `.o`

**命令：**`gcc -c hello.c`

​			`gcc -c hello.c -o hello.o`



### 链接

把多个目标文件，包括 **动态库、静态库** 链接到一起，生成最终的可执行文件。  

**命令：**`gcc -o executeFile hello.o world.o `





# makefile

```makefile
executeFile.exe: hello.o world.o
	gcc -o executeFile hello.o world.o
hello.o: hello.c
	gcc -c hello.c -o hello.o
world.o: world.c
	gcc -c world.c -o world.o
	
clean:
	rm -f *.o executuFile.exe
```

精简版：

```makefile
executeFile.exe: hello.o world.o
	gcc -o *.o $@	//$@ = executeFile.exe
%.o: %.c
	gcc -c $< -o $@
clean:
	rm -f *.o executeFile.exe
```







# Vim 编辑器

​		Vim 是 Vi 的升级版，大致分为三个模式：Normal、Insert、Viusal、命令行。Vim支持自定义，并且自定义的功能很强，自定义文件保存在 `~/.vimrc`。Vim 可以不用鼠标做一切操作，熟练起来后，效率高于其他编辑器。

​		[Vim Wiki](https://vim.fandom.com/wiki/Vim_Tips_Wiki)

​		[VimGolf](http://www.vimgolf.com/)

​		[Best Vim Tips](https://vim.fandom.com/wiki/Best_Vim_Tips#Basic_use)



## 基本操作

​		进入Vim时，用户会处于 Normal 模式，且用户的大部分处理文字的时间都会在 Normal 模式。用户可以在Normal模式中，进去其他模式。

- **Normal 模式：**处理文字，在任何模式下按 `ESC` 进入

- **Insert 模式：**插入文字，在Normal模式下按 `i` 进入

- **Visual 模式：**选中文字，在Normal模式下按 `v` 进入

- **命令行模式：**键入命令，在Normal模式下按 `:` 进入

​		Vim 的接口是可编程的。即，在Normal模型下的命令是可以组合使用的，像程序语言一样，会产生不同的效果。大致语法如下：`[operator] [number] [motion]`，如：`d3w`，删除3个单词。



### 移动光标

- `h` `j` `k` `l`

​		这四个按键分别为，左下上右，由于上下左右按键距离较远，所以尽量使用这四个键进行移动光标。可以用数字组合，如 `4j`，光标向下移动4行。

- `w`  `e`  `b`

  分别为：移动至下一个单词的词头，下一个词尾，上一个词头

- `0`  `$`  `^`

  分别为：移动至行首、第一个非空字符、行尾

- `H`  `M`  `L`

  分别为：移动至屏幕首行、中央、底部

- `Ctrl-u`  `Ctrl-d`

  屏幕上翻、屏幕下翻

- `%`

  找到与当前括号的匹配括号

- `gg`  `G`

  移动至文件头、文件尾

- `{行数}G`

  移动至 {行数}

### 保存与关闭与打开

- `:wq`

​		对文件的操作需要在命令行模式下进行。其中，`w` 表示保存文件，`q` 表示退出。

- `:q!`

  不保存，直接退出程序。

- `:e {文件名}`

  打开要编辑的文件

### 查找与替换

- `/{正则表达式}`

  `n` 跳至下一个匹配项，`N`跳至上一个

- `:s`  [帮助文档](http://vim.wikia.com/wiki/Search_and_replace)

  - `:s/foo/bar/g`  将当前行中的 `foo` 替换为 `bar`，`g` 表示 `global`
  - `%s/foo/bar/g`  在整个文件中将 foo 全局替换成 bar
  - `:%s/foo/bar/gc` 全局替换，但是替换前会询问，`c` 表示 `confirm`

### 编辑文本

- `O` / `o` 在之上/之下插入行

- `d{移动命令}`  

  删除 {移动命令}。

  例如， `dw` 删除词, `d$` 删除到行尾, `d0` 删除到行头。

- `c{移动命令}`

  改变 {移动命令}，**改变后进入插入模式**

  例如， `cw` 改变词、`d{移动命令}` 再 `i`

- `x` 删除字符（等同于 `dl`）

- `s` 替换字符（等同于 `xi`）

- `u` 撤销, `<C-r>` 重做

- `y` 复制 / “yank” （其他一些命令比如 `d` 也会复制）

- `p` 粘贴

- `~` 改变字符的大小写

### 修饰语

 `i`，表示“内部”或者“在内“； `a`， 表示”周围“。

- `ci(` 改变当前括号内的内容
- `ci[` 改变当前方括号内的内容
- `da'` 删除一个单引号字符串， 包括周围的单引号





## 快捷操作



### 宏录制及使用

- 在normal模式下，按q加一个字母开始录制
- type 需要录制的操作
- 按下q完成录制

### 反转选中文字

`vnoremap ;rv c<C-O>:set ri<CR><C-R>"<Esc>:set nori<CR>`

### 快速缩进多行

- 选中要缩进的行
- 用 `<` 或 `>` 缩进或者缩出

### 快速添加注释

#### 1 宏录制

#### 2 块选择模式

- 进入命令行模式
- `Ctrl + v` 进入 Visual Block 模式
- 选择需要注释的行
- `Shift + i` 进入插入模式
- 输入需要注释的符号
- 多次按 `Esc` 即可完成注释



## 自定义Vim

​		Vim 由一个位于 `~/.vimrc` 的文本配置文件（包含 Vim 脚本命令）配置Vim。



### 插件

​		使用插件只需要创建一个 `~/.vim/pack/vendor/tart/` 的文件夹，然后把插件放到这里（比如通过 `[[git]] clone`）。浏览 [Vim Awesome](https://vimawesome.com/) 来了解一些很棒的插件。

#### VsVim

​		VsVim可以将Visual Studio的文本编辑器变为Vim编辑器。配置文件一般存放在`C:\Users\Administrator`





# 终端定制

​		以下方案为 **zsh + oh my zsh**。



1. 安装zsh

   `sudo yum -y install zsh`

2. 切换终端为 zsh

   `chsh -s /bin/zsh

3. 查看当前终端

   `echo $SHELL`

4. 安装 oh-my-zsh





