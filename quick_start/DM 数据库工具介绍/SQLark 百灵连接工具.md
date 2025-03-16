

# SQLark 百灵连接工具

## 一、概述

SQLark 百灵连接是一款面向信创应用开发者的数据库开发和管理工具，由达梦数据历时三年自主研发，致力于帮助信创应用开发人员更加高效、安全地完成数据库的开发和管理任务。

目前 SQLark 支持在 Windows，Linux，macOS 等多种操作系统上进行部署。

### 1.1 获取方式

通过 PC 端访问 SQLark 官网 [www.sqlark.com](https://www.sqlark.com/?s=eco-com-doc)，下载安装 SQLark 最新客户端。

### 1.2 功能概览

#### 1.2.1 支持达梦数据库/Oracle/MySQL

每种数据库均支持丰富的数据库对象：模式、表、视图、物化视图、函数、存储过程、序列、触发器、包、DBLink、自定义类型。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20241227180535QINQWQXHUTSQ5F0LSB)

#### 1.2.2 SQL 智能编辑器

基于语法语义解析实现代码补全能力，为用户提供精准的 SQL 编码提示，包括但不限于：

（1）代码提示和补全：基于 SQL 语法实现对关键字、对象名、别名、 代码块的智能提示和代码补全；通过外键关系自动补全整个 JOIN 联表查询语句；代码内快速查看对象 DDL、支持对系统函数的语法和示例查询等。

（2）辅助开发：SQL 格式化 、快速注释 、大小写转换。

（3）PL/SQL 对象支持：支持函数、存储过程、包等 PL/SQL 对象的实时语法结构解析，快速定位代码。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20241227175903YJFQTD4G4B12CQVSY6)

#### 1.2.3 丰富的应用市场

SQLark 不断更新的应用市场，助力将信创应用开发工作化繁为简。

（1）[数据生成](https://www.sqlark.com/docs/zh/v1/data-generation/data-generation.html)：快速生成千万级/亿级仿真表数据，助力大型项目 POC 测试

（2）[数据导入](https://www.sqlark.com/docs/zh/v1/data-import-and-export/data-import.html#%E6%95%B0%E6%8D%AE%E5%AF%BC%E5%85%A5)：从外部文件快速导入数据到目标表

（3）[执行计划分析器](https://www.sqlark.com/docs/zh/v1/inquire/execution-plan.html)：辅助分析复杂的执行计划，快速定位慢 SQL 问题

（4）[ER 图](https://www.sqlark.com/docs/zh/v1/er/view-er.html)：根据数据库/模式/表生成逆向 [ER 图](https://www.sqlark.com/docs/zh/v1/er/view-er.html)，帮助开发者快速厘清数据库的表结构设计

![3.jpg](https://eco.dameng.com/eco-file-server/file/eco/preview/20241227175606T709CXZ9KGHUW0W613)

#### 1.2.4 数据迁移

提供全生命周期的数据迁移解决方案，一次迁移成功率 90% 以上

（1）迁移评估：通过分析源库对象、兼容性、大表大字段表等迁移重难点情况，生成源库画像和迁移策略，评估本次迁移需要投入的工作量。

（2）迁移实施：基于迁移策略对数据库对象和表数据开展自动化迁移和语法转换，为迁移异常提供错误分析和修改建议，以任务管理的方式保障迁移工作完成。

（3）迁移校验：通过比对源库和目标库的对象数量、表数据量是否一致，检验迁移工作是否正确、完整。

![4.jpg](https://eco.dameng.com/eco-file-server/file/eco/preview/202412271756175FMGGJ58106D4ZOMFM)

### 1.3 产品反馈与交流

产品安装和使用过程中有任何疑问、BUG 或需求，欢迎通过社区论坛 [ask.sqlark.com](http://ask.sqlark.com/) 进行反馈。

  * SQLark 官网 ：[www.sqlark.com](https://www.sqlark.com/?s=eco-com-doc)
  * SQLark 社区 ：[ask.sqlark.com](http://ask.sqlark.com/)



## 二、安装 SQLark

### 2.1 Windows 环境

#### 2.1.1 系统需求

Microsoft Windows 7、Windows 8、Windows 8.1、Windows 10、Windows 11

#### 2.1.2 操作步骤

  1. 下载完成后，双击打开 .exe 文件。
  2. 点击 SQLark 许可协议与服务条款，阅读协议，勾选【阅读并同意】，点击【自定义安装】。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2024122014224223SQWQQCQCDXKCBVC4)

  3. 点击【浏览】，选择安装位置（以 D:\Program Files (x86) 为例）。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20241220142251HP2I1ONUQFH1TE26WE)

  4. 等待安装界面显示安装完成，点击【立即体验】



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20241220142306FDTUK3G7C5GTBFATCY)

### 2.2 macOS 环境

#### 2.2.1 系统需求

支持的芯片平台：Intel、Apple Silicon（M1/M2）

操作系统需求：macOS Monterey 12、macOS Ventura 13、macOS Sonoma 14

#### 2.2.2 操作步骤

  1. 下载完成后，在访达中打开【下载】文件夹。
  2. 双击打开 SQLark_X_X_X.dmg 文件，在安装对话框中，拖动【SQLark 百灵连接】图标到【Applications】文件夹。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20241220142322GQYIPIW97UXF7RTJQI)

  3. 在启动台或应用程序文件夹中，单击 SQLark 图标即可启动应用。



### 2.3 Linux 环境

#### 2.3.1 系统需求

支持的操作系统版本 ：kylin V10（x86_64）

#### 2.3.2 操作步骤

SQLark 提供 tar.gz 和 rpm 两种安装包，以下为安装 rpm 包的操作步骤，了解更多请点击：[安装 SQLark](https://www.sqlark.com/docs/zh/v1/install/install.html)

  1. 下载 rpm 安装包后，可通过【我的电脑 | 下载文件夹】 查看；右键单击目录空白处，点击【在终端中打开】。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202412201423408NI4PXR1MPMWUZUT44)

  2. 打开终端后，输入以下安装命令，并按下回车键。



```
sudo rpm -ivh [SQLark_version]
```

![](http://dev.sqlark.com/docs/manual/install/install/linux/rpm01.jpg)

![](http://dev.sqlark.com/docs/manual/install/install/linux/rpm01.jpg)

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20241220142357GK0FRDNRZ6ISJSXY62)

  3. 等待安装进程加载至 100% 后，显示如下界面，则安装完成。
  4. 点击【开始菜单 | 所有程序 | 系统工具】，即可查看 SQLark 应用程序已安装。



## 三、快速上手

本文以达梦数据库为例，引导用户快速基于 SQLark 开始数据库连接和管理工作。

### 3.1 连接达梦数据库

SQLark 使用 JDBC 驱动程序连接到达梦数据库，安装包里内置驱动程序。
点击工具栏“连接” — 选择“达梦” — 弹出“新建连接”对话框。
填写以下信息：

  * 连接名
  * 主机
  * 端口
  * 用户名
  * 密码



确保以上信息正确，点击“连接”按钮，即可连接上达梦数据库。

![5.jpg](https://eco.dameng.com/eco-file-server/file/eco/preview/20241227175644WOYI0G4BP2DY5UN64U)

### 3.2 浏览数据库对象

成功连接达梦数据库后，您将在左侧对象导航栏中看到达梦数据库的对象列表。SQLark 支持的达梦数据库对象包括：

  * 模式
  * 表
  * 视图
  * 物化视图
  * 函数
  * 存储过程
  * 序列
  * 触发器
  * 包
  * 同义词
  * 外部链接
  * 自定义类型



除此之外，左侧对象导航栏还可以进行角色、用户、表空间的管理。选择需要管理的对象名称，点击右键，可通过弹出菜单选择执行各种管理操作。

![6.jpg](https://eco.dameng.com/eco-file-server/file/eco/preview/20241227175652JIKB90CAVSRUZBUWVC)

### 3.3 编写 SQL 代码

SQLark 提供代码智能提示和自动补全，包括：

  * 支持对关键词和模式名、表名、表别名、字段名等对象名的补全；
  * 支持对联表语句等代码段的智能提示；
  * 支持对函数常用的形参格式的提示；
  * 支持对 DMSQL 的智能提示；
  * 支持一键格式化、快速注释和转换为大小写等辅助功能。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20241227180611XL8DBRX72JR16G40LG)

### 3.4 执行 SQL 代码

在 SQL 编辑窗口中选中需要执行的语句，点击“执行”按钮或 Ctrl+Enter 快捷键，即可完成代码执行，您可通过执行信息和结果集窗口来进行代码开发工作。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20241227180706B2JWMIO1HPS4EYFD26)
