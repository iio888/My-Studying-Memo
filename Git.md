# Git

​		免费、开源的**分布式版本控制系统**。

​		客户端并不只提取最新版本的文件快照， **而是把代码仓库完整地镜像下来，包括完整的历史记录**。 这么一来，任何一处协同工作用的服务器发生故障，事后都可以用任何一个镜像出来的本地仓库恢复。 因为每一次的克隆操作，实际上都是一次对代码仓库的完整备份。

[Pro Git]:https://git-scm.com/book/en/v2
[Learn Git Branching]:https://learngitbranching.js.org/



## 基本概念



### 中心代码仓库

​		每一个developer本地的代码仓库**（repository）**都是一个功能完整，历史完整的仓库，它们是完全等价的。在Git的开发流程中，我们会把其中一个代码仓库人为地定义为**blessed repository**（受庇佑的代码仓库）来作为**中心代码仓库**。

### Staging Area

![staging_area](E:\Practice\记录文档\Git.assets\staging_area.png)

​		所有代码的编辑都发生在 **working directory**， 而所有的 committing 都发生在 **staging area**。所以用户必须先把要提交的代码添加到暂存区。

### Origin

​		因为 Git 是分布式的，所以除本地仓库以外，会存在很多个等价的远程代码仓库。因此，在本地的配置文件中，会以 remote 字段记录其他远程代码仓库的别称。**origin** 是其中最常见的别称，通常用来标记 **blessed repository** 的地址。



## 常用命令

http://www.cheat-sheets.org/saved-copy/git-cheat-sheet.pdf



### 1. 初始配置

#### 1. 设置用户名和邮件地址

​		如果使用了 `--global` 选项，那么该命令只需要运行一次，因为之后无论你在该系统上做任何事情， Git 都会使用那些信息。

​		当你想针对特定项目使用不同的用户名称与邮件地址时，可以在那个项目目录下运行没有 `--global` 选项的命令来配置。

```git
$ git config --global user.name "io888"
$ git config --global user.email ioWork@foxmail.com
```



#### 2. 查看配置信息

##### 查看所有信息

`$ git config --list`

##### 查看某一项配置

 `$ git config user.name`

`$ git config user.email`



#### 3. 让 Git 显示颜色

`$ git config --global color.ui true`



#### 4. 配置别名

​		使用`git status`命令时，我们可以通过配置别名的方式将其配置为`git st`，在使用时就比输入 `git status`简单方便很多。

**常用别名配置：**

```git
$ git config --global alias.st status
$ git config --global alias.co checkout
$ git config --global alias.ci commit
$ git config --global alias.br branch
$ git config --global alias.unstage 'reset HEAD'
$ git config --global alias.last 'log -1'

$ git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"
```





### 2. 撤销暂存区的修改

`$ git reset HEAD test.py`

​		把暂存区的修改撤销掉（unstage），重新放回工作区。



### 3. 显示最后一次提交信息

`$ git log -1`



### 4. 建立仓库 

- `cd` 至想要建立仓库的目录

  `$ cd /e/Practice/learning-git`

- `$ git init`

​		建立后，目录下会多一个 `.git` 的文件夹，这是Git 来跟踪管理版本库的。如果没有看到 .git 目录，那是因为这个目录默认是隐藏的，用`ls -ah`命令就可以看到了。



### 5. 克隆现有仓库

`$ git clone <url> `

`$ git clone https://codechina.csdn.net/codechina/help-docs`

​		这会在当前目录下创建名为 `help-docs` 的目录，并在这个目录下初始化一个 `.git` 文件夹， 从远程仓库拉取下所有数据放入 `.git` 文件夹，然后从中读取最新版本的文件的拷贝。 所有的项目文件都已经在 `help-docs` 中，等待后续开发和使用。

**自定义本地仓库名：**

`$ git clone https://codechina.csdn.net/codechina/help-docs  mydocs`



### 6. 将文件提交至仓库

- `$ git add readme.txt `

  暂存文件

- `$ git commit -m "wrote a readme file" `

​		`-m` 后面输入的是本次提交的说明，可以输入任意内容。

​		`commit` 可以一次提交很多文件，所以你可以多次 `add` 不同的文件。

**执行成功后：**

```
$ git commit -m "wrote a readme file"
[master (root-commit) 50ed06b] wrote a readme file
 1 file changed, 2 insertions(+)
 create mode 100644 readme.txt
```

-  file changed：1个文件被改动（新添加的readme.txt文件）

-  insertions：插入了两行内容（readme.txt有两行内容）



### 7. 查看仓库当前状态

`$ git status `

​		查看 **工作目录中有哪些文件还没有放进暂存区** 以及 **暂存区中有哪些内容还没有提交到仓库**。



### 8. 查看修改内容

`$ git diff `



### 9. 查看全部提交记录

`$ git log `

`$ git log --pretty=oneline `

使用 Q 退出



### 10. 移步至某一版本

HEAD	  	   表示当前版本

HEAD^		   表示上一个版本

HEAD^^  	   表示上上一个版本

HEAD~100	表示上100个版本

`$ git reset --hard HEAD^ `

`$ git reset --hard e55063a`



### 11. 查看文件内容

`$ cat readme.txt `



### 12. 查看命令记录

`$ git reflog `

可以查看历史版本号 `commit id`



### 13. 重新提交

`$ git commit --amend`

​		这个命令会将暂存区中的文件提交。 如果自上次提交以来你还未做任何修改（例如，在上次提交后马上执行了此命令）， 那么快照会保持不变，而你所修改的只是提交信息。



### 14. 撤销文件的修改

`$ git checkout -- readme.txt`

`$ git restore readme.txt`

​		这是一个危险的命令，你对那个文件在本地的任何修改都会消失——Git 会用最近提交的版本覆盖掉它。

​		`git checkout / restore` 其实是用版本库里的版本替换工作区的版本，无论工作区是修改还是删除，都可以“一键还原”。

**注意：**从来没有被添加到版本库就被删除的文件，是无法恢复的！



### 15. 删除文件

本地删除：

`$ rm test.txt`

仓库中删除：

`$ git rm test.txt`



### 16. 分支

#### 创建分支

- `$ git checkout -b dev`

创建了 dev 分支

相当于：

```git
$ git branch dev
$ git checkout dev
```

- `$ git switch -c dev`

  创建并切换到新的`dev`分支

#### 查看当前分支

`$ git branch`

#### 切换分支

`$ git checkout master`

`$ git switch master`

#### 合并分支

`$ git merge dev  `

合并指定分支到当前分支

#### 删除分支

`$ git branch -d dev`



### 17. 标签

​		**轻量级标签（lightweight)**，创建的标签都只存储在**本地**，不会自动推送到远程。所以，打错的标签可以在本地安全删除。

​		**含附注的标签(annotated)**，附注标签实际上是存储在仓库中的一个独立对象，它有自身的校验和信息，包含着标签的名字，电子邮件地址和日期，以及标签说明。

#### 打标签

- 默认标签是打在最新提交的 `commit`

`$ git tag <name>`		`$ git tag v1.0`

- 打历史版本的标签，加上版本号

`$ git tag v0.9 fb8b190`

- 创建带有说明的标签，即**含附注的标签(annotated)**

`$ git tag -a v0.1 -m "version 0.1 released" 187f963`

`-a` 指定标签名，`-m` 指定说明文字

- 创建一个带 `gpg` 签名的标签

`$ git tag -s v1.2 -m 'my signed 1.2 tag'`

#### 查看所有标签

`$ git tag`

#### 查看标签信息

`$ git show v0.9`

#### 删除标签

- 本地

`$ git tag -d 'v0.9'`

- 删除已推送tag

```git
$ git tag -d v1.0
$ git push origin :refs/tags/v1.0
```



#### 推送 tag

- 推送某个tag

`$ git push origin v1.0`

- 一次性推送本地tag

`$ git push origin --tags`





## 忽略特殊文件

`.gitignore`

​		有些时候，你必须把某些文件放到 Git 工作目录中，但又不能提交它们，比如保存了数据库密码的配置文件等等，每次`git status`都会显示`Untracked files ...`，这种情况下，就可以实用忽略特殊文件 `.gitignore` 来很方便的解决这个问题。

​		在 Git 工作区的根目录下创建一个特殊的 `.gitignore`文件，然后把要忽略的文件名填进去，Git 在每次进行提交的时候就会自动忽略这些文件。

### 强制添加被忽略文件

使用 `-f` 强制添加

`$ git add -f App.class`

### 检查规则

`$ git check-ignore`

### 添加例外规则

`! + 文件名`

`！App.class`



