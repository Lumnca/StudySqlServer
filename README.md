# ----------------------- SqlServer2012 -------------------------- #

<p id="title"></p>

## 目录 ##

:arrow_down:<a href="#a1">1.数据库管理</a>

<p id="a1"></p>

## :mortar_board: 数据库管理 ##

:arrow_double_up:<a href = "#title">返回目录</a>

### :memo:1.1数据库基本简介 ###

* 想要创建数据库，至少要拥有Create database.Create any database 或者 Alter any database权限，创建数据库的用户将成为该数据库的所有者，任何可以访问SQL
Server 链接的用户登录账户都可以成为数据库的所有者。在同一个实例中，最多可以创建32767个数据库，超过这个数量的数据库将会创建失败。

* 每个SQl Server 数据库至少包含两个文件： `一个数据文件和一个日志文件`，**数据文件里包含的是数据库的对象，比如表，视图，和索引等；日志文件里包含的是用于恢复数据库所需的信息** 

>数据文件扩展名

 * 主要文件为 .mdf

 * 次要文件为 .ndf

>数据库日志扩展名为 

 * 主次均为 .ldf

* 数据库状态

|状态|说明|
|:--:|:---:|
|ONLINE(在线)|可以对数据库进行访问|
|OFFLINE(离线)|数据库无法使用|
|RESTORING(还原)|正在还原文件，或者离线还原，此时数据库不可用|
|RECOVERRING(恢复)|正在恢复数据库，该状态是暂时性的状态，恢复成功后，数据库会自动回到在线状态|
|RECOVERY RENDING(恢复待定)|数据库并没损坏，但有可能缺少文件，此时数据库不可用，并等待用户执行操作来完成恢复工作|
|SUSPECT(可疑)|数据库有可疑或者损坏，启动过程中无法恢复问文件，此时数据库不可用|
|EMERGENCY(紧急)|一般用于排除故障|

### :memo:1.2创建数据库 ###

使用Create database 语句可以创建数据库：

`Create database 数据库名称`

Create database语句后面参数为：

  * database_name ：数据库名，这个名字要唯一

  * ON ： 用来定义数据库的数据文件，PRIMARY指出其后所定义的文件是主数据，如果省略，则第一个定义的文件是主数据文件

  * LOG ON : 用来定义数据库的日志文件，如果没有指定LOG ON，将会自动创建

  * COLLATE : 定义数据库的默认排序规则，没有则默认排序

  * WITH ： 用来控制外部与数据库之间的双向访问
 
  * FOR ATTACH ：用来设置附加数据库的选项，必须指定一个主文件的<filespec>项

  * ATTACH_REBUILD_LOG ：用来设置附加数据库的选项，该选项只限于读写数据库。

注意一下示例注释采用的//不能在命令语句中，本文只是方便解释，编写代码不能采用这种注释

示例1（自定义创建于目标文件夹） ：
```
create database t2
ON
(
 //库名
	NAME = "my1",
 //创建位置
	FILENAME= "F:\SQLServer\Sql\my1.mdf"
)
```

示例2 （对数据库的存储大小进行限制）：
```
create database t3
ON
(
	NAME = "my1",
	FILENAME= "F:\SQLServer\Sql\my1.mdf",
 //规定初始容量
	SIZE = 5MB,
 //最大容量
	MAXSIZE= 10MB,
 //文件增长的数量
	FILEGROWTH = 5%
)
```

示例4 （指定日志文件存入位置）：
```
create database t3
ON
(
	NAME = "my1",
	FILENAME= "F:\SQLServer\Sql\my1.mdf",
	SIZE = 5MB,
	MAXSIZE= 10MB,
	FILEGROWTH = 5%
)
//日志文件
LOG ON
(
//日志命名
	NAME = "myTest",
 //日志名存入文件位置
	FILENAME = "F:\SQLServer\Sql\myTest1.ldf"
)
```

示例5（写入多个数据文件以及数据组） ：
```
create database 数据库
ON
(
	NAME = "数据库文件1",
	FILENAME= "F:\SQLServer\Sql\my1.mdf",
	SIZE = 5MB,
	MAXSIZE= 10MB,
	FILEGROWTH = 5%
),
(
	NAME = "数据库文件2",
	FILENAME= "F:\SQLServer\Sql\my2.mdf",
	SIZE = 5MB,
	MAXSIZE= 10MB,
	FILEGROWTH = 5%
),
FILEGROUP 数据库文件组1
(
	NAME = "数据库文件3",
	FILENAME= "F:\SQLServer\Sql\my3.mdf",
	SIZE = 5MB,
	MAXSIZE= 10MB,
	FILEGROWTH = 5%
)
,
FILEGROUP 数据库文件组2
(
	NAME = "数据库文件4",
	FILENAME= "F:\SQLServer\Sql\my4.mdf",
	SIZE = 5MB,
	MAXSIZE= 10MB,
	FILEGROWTH = 5%
)
LOG ON
(
	NAME = "日志文件1",
	FILENAME = "F:\SQLServer\Sql\my1.ldf"
)

```

示例6 ： （排序规则）
```
create database 我的数据库
ON
(
	NAME = "数据库文件",
	FILENAME= "F:\SQLServer\Sql\my1.mdf",
	SIZE = 5MB,
	MAXSIZE= 10MB,
	FILEGROWTH = 5%
)
COLLATE Chinese_PRC_CI_AS
```
Chinese_PRC_CI_AS为排序规则，查看更多排序规则可以使用输入命令：`select * from :: fn_helpcollations()`查看规则表。

