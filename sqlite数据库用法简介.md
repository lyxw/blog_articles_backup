# sqlite数据库用法简介

### 0x01 基本命令

#### 1、进入命令行环境

打开一个控制台窗口，输入`sqlite3`回车，这时你就进入了SQLite命令行环境

它显示了版本号，并告诉你每一条SQL语句必须用分号`;`结尾 

![](https://lyxw.github.io/images/sqlite/sqlite1.png)

#### 2、命令行帮助

在命令行环境下输入`.help`回车，显示所有可使用的命令以及这些命令的帮助。

命令如下:

![](https://lyxw.github.io/images/sqlite/sqlite_help.png)

> 注意：所有的命令开头都是一个点

#### 3、退出命令行环境

`.quit`或者`.exit`都可以退出命令行环境

### 0x02 数据库和表的相关命令

#### 1、创建一个新的数据库

命令为：`sqlite3 文件名`

先建立一个`Db`目录，并在`Db`目录中创建一个`test.db`数据库文件，打开控制台窗口，命令如下：

```
mkdir Db
cd Db
sqlite3 test.db
```

![](https://lyxw.github.io/images/sqlite/sqlite2.png)

#### 2、打开一个已经存在的数据库

命令为：`sqlite3 已经存在的文件名`

创建一个新数据库和打开一个已经存在的数据库命令是一模一样的，如果文件在当前目录下不存在，则新建；如果存在，则打开。

#### 3、导入数据

命令为：`.read 数据文件`

将SQL语句复制到记事本中，保存为`test.sql`到上面说到的`Db`目录下，在命令行环境中输入

```
.read   test.sql
```

即将所有的数据导入到`test.db`数据库中。

![](https://lyxw.github.io/images/sqlite/sqlite3.png)

#### 4、列出所有的数据表

命令为：`.tables`

完成上面所有的工作以后，我们就可以列出所有的数据表了

![](https://lyxw.github.io/images/sqlite/sqlite4.png)

#### 5、显示数据库结构

命令为：`.schema`

其实就是一些 SQL 语句，他们描述了数据库的结构，

#### 6、显示表的结构

命令为：`.schema 表名`

![](https://lyxw.github.io/images/sqlite/sqlite4.png)

#### 7、导出某个表的数据

命令为：`.dump 表名`

这时我们可以看到，整个表以SQL语句的形式为导出来了，但是只是显示在终端上

![](https://lyxw.github.io/images/sqlite/sqlite5.png)

#### 8、设置导出目标

命令为：`.output 文件名`或者`.output stdout`

先运行`.output cars.sql`，然后再运行`.dump`命令试试看。如果要回复成导出到终端（标准输出），则运行`.output stdout`

![](https://lyxw.github.io/images/sqlite/sqlite6.png)

### 0x03 数据显示相关命令

#### 1、设置分隔符

命令为：`.separator 分隔符`

我们可以首先运行`SELECT * FROM test;`，可以看到默认的分隔符是`| `

运行`.separator :`以后，再`SELECT * FROM test;`，可以看到分隔符已经变成`:`了

![](https://lyxw.github.io/images/sqlite/sqlite7.png)

#### 2、设置显示模式

命令为：`.mode 模式`

有好几种显示模式，默认的是* list *显示模式，一般我们使用* column *显示模式，还有其他几种显示模式可以`.help`看mode相关内容。看看下面的图，和上面是不是显示的不一样了？

![](https://lyxw.github.io/images/sqlite/sqlite8.png)

#### 3、显示标题栏

命令为：`.headers on`

看看，是不是又不太一样了？

![](https://lyxw.github.io/images/sqlite/sqlite9.png)

#### 4、设置每一列的显示宽度

命令为：`.width w1,w2,w3.........`

一些内容，在默认的宽度显示不下时，这个命令就有用了

#### 5、设置`NULL`值显示成什么样子

命令为：`.nullvalue 你想要的NULL值格式`

默认情况下NULL值什么也不显示，你可以设置成你自己想要的样子

![](https://lyxw.github.io/images/sqlite/sqlite10.png)

#### 6、列出当前显示格式设置情况

命令为：`.show`

![](https://lyxw.github.io/images/sqlite/sqlite11.png)

#### 7、配置文件

命令为：`.sqliterc`

如果我们每次进入命令行都要重新设置显示格式，很麻烦，其实`.show`命令列出的所有设置项都可以保存到一个`.sqliterc`文件中，这样每次进入命令行就自动设置好了。`.sqlterc`文件在Linux下保存在用户的* Home *目录下，在Windows下可以保存到任何目录下，但是需要设置环境变量让数据库引擎能找到它，感兴趣的可以看看帮助。
