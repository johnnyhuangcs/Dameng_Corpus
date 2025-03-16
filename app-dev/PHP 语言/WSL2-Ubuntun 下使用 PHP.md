

# WSL2-Ubuntun 下使用 PHP

## 一、WSL 安装

### 1.1 微软商店下载 WSL

查看可通过在线商店获得的 Linux 发行版列表。

```
wsl --list --online
```

或者使用以下命令：

```
wsl -l -o
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2024012612041113RZPL9959SK718X3X)

在商店搜索 ubuntu，下载需要的版本。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240126120427TAMLCJGB0RHA7AEFUV)

> **注意**
>
> 此处下载 Ubuntu 和 Ubuntu 22.04.3 LTS 是一样的，因为目前 22.04.3 LTS 就是最新的。



### 1.2 列出已安装的 Linux

查询已安装的 Linux 发行版：

```
wsl --list --verbose
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240126120600Y1S3F7WDVM3TM2S5HH)

说明：查看安装在 Windows 计算机上的 Linux 发行版列表，其中包括状态（发行版是正在运行还是已停止）和运行发行版的 WSL 版本（ WSL1 或 WSL2 ）。 比较 WSL1 和 WSL2，此命令也可输入为：wsl -l -v 。 可与 list 命令一起使用的其他选项包括：--all（列出所有发行版）、--running（仅列出当前正在运行的发行版）或 --quiet（仅显示发行版名称）。

### 1.3 将 WSL 版本设置为 1 或 2

将 WSL Ubuntu-20.04 版本切换为版本 1：

```
wsl --set-version Ubuntu-20.04 1
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202401261208425S0RO00178ZBS5TQ3E)

说明：命令格式为 wsl --set-version  ，若要指定运行 Linux 发行版的 WSL 版本（1 或 2），请将  替换为发行版的名称，并将  替换为 1 或 2。 比较 WSL 1 和 WSL 2。 WSL 2 仅在 Windows 11 或 Windows 10 版本 1903 、内部版本 18362 或更高版本中可用。

查询已有的 wsl 版本：

```
wsl --list --verbose
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240126120914X3WLEIWKVRQ3FMIHOW)

说明：此 * 代表当前 cmd 使用版本。

### 1.4 将目录更改为主页

将当前目录切换到 wsl 主目录：

```
wsl ~
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240126121008HGSWNPOEG1K537QR28)

说明：~ 可与 wsl 一起使用，以在用户的主目录中启动。 若要在 WSL 命令提示符中从任何目录跳回到主目录，可使用命令 cd ~ 。

### 1.5 进入到指定 wsl 环境

应用商店下载的直接点击就能进入当前环境并启动。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202401261210418NYL6XU0RQ8H27Y5ZA)

## 二、 下载其他依赖

### 2.1 下载 Apache2 和 php

建议下载默认的 Apache 和 php 集成，不要进行 php 官网下载解压，目前高版本 php 解压包在 Ubuntu 环境有很多依赖和文件缺失的问题。

Ubuntu 20.04 和 22.04 中 Apache 软件包为 Apache2，运行下面的命令来更新软件包索引，并且安装 Apache。

```
sudo apt update
sudo apt install Apache2
sudo systemctl status Apache2
```

使用命令下载 php ：

```
sudo apt install -y php
```

说明：此命令会下载当前环境默认 php 版本，Ubuntu 20.04.6 LTS php 默认版本为 7.4 ，Ubuntu 22.04.3LTS php 默认版本为 8.2 ，重复下载不会覆盖已有目录，目前 Ubuntu 只能下载 NTS 版本。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240126121518I03AMWE2INVXQO9I98)

此处不要下载 php-fpm，直接下载 php，因为高版本 php 核心代码已经集成了 fpm，不用使用此命令启动，phpphp -s 0.0.0.0:50，因为目前 ubuntu 默认整合了 Apache2 和 php 的链接。

### 2.2 使用 Apache2 和 php

查看已有 php 版本：

```
sudo update-alternatives --config php
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240126121604HSTJVCKGD8WT83IM3J)

切换当前 php 环境:

```
sudo update-alternatives --set php /usr/bin/php8.2
```

查看当前 php 配置文件路径:

```
php --ini
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2024012614594104HME5ZHEE0RIT9Q2Q)

添加系统或当前用户环境变量：

```
cat /etc/profile
```

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml25252\wps4.jpg)

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240126141805I45SIHWM9MCSOUMDM0)

修改 php.ini 配置文件。

![111.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20241223124749HQ7PEYYVSUTSBD4J36)

查看模块是否导入成功：

```
php -m
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240126181007XQDKQK99X6S7X3ITMB)

## 三、使用 Xhell

如果需要外部 ip 访问 php 需要使用 Xhell，关闭防火墙。

查看是否已经安装 SSH 。

更新系统，安装 SSH 。

```
sudo apt update && sudo apt upgrade -y
```

卸载并安装 SSH ：

```
sudo apt autoremove --purge openssh-server -y && sudo apt install openssh-server -y
```

修改 SSH 配置 。

编辑配置文件：

```
sudo vim /etc/ssh/sshd_config
```

添加配置（开放端口 20）

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240126181406KC4Q3LBGIU532RXSLR)

重启并查看本机 IP 。

安装好 SSH 后，Ubuntu 需要重新启动，重启后还需要启动 SSH。

启动 SSH ：

```
service ssh start
```

查看本机 IP ：

```
ip address
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240126142803VR9YKIGP8XU3NT546S)

Xshell 中配置连接

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240126142951R7BCIF5UA7HKEGD9VY)

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202401261429575OUOBOK1I98CO7NFYD)

## 四、使用达梦连接 php

测试前需要注册实例，目前实例参数为默认参数。

### 4.1 Apache2 和 php 运行 php

修改监听:

```
vim /etc/apache2/ports.conf
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240126182000P1FH97KKOZWCNKXTAS)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml25252\wps6.jpg)

```
说明：以下是 Apache2 命令。

启动# sudo /etc/init.d/Apache2 start

停止# sudo /etc/init.d/Apache2 stop

重启# sudo /etc/init.d/Apache2 restart
```

将 PHP 文件放在 Apache 的 Web 根目录（通常是  /var/www/html ）中，并通过浏览器访问它们。

例如此处目录下有个 phpinfo.php 文件，内容如下：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240126181922KPACUZDL9ZHB3H9YHP)

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240126143701JSX4KNGJ50XKFXDDGV)

显示 php 为线程不安全。

### 4.2 直接使用 php 执行

创建 php_conn.php 。

```
<?php
   header("Content-type:text/html;charset=UTF-8"); //防止页面乱码
try
{
    $link = dm_connect("localhost:5236", "SYSDBA", "*****")
        or die("Could not connect : " . dm_error()."\n");  

    //使用 dm_error 会显示 dm 的 php 接口返回的错误，执行成功，则继续往下执行。
    print "php: Connected successfully"."\n"; 

    /*断开连接*/ 
    dm_close($link); 
}
catch(Exception $e)
{
    $e->getMessage() . "<br/>";
}
?>
```

使用 php  php_conn.php 运行。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240126144509IL9QOWATB49T95AZE5)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml25252\wps9.jpg)

创建 php_create.php 。

```
<?php
   header("Content-type:text/html;charset=UTF8"); //防止页面乱码
try
{
    $link = dm_connect("localhost:5236", "SYSDBA", "*****")
        or die("Could not connect : " . dm_error()."\n");  

    //使用 dm_error 会显示 dm 的 php 接口返回的错误，执行成功，则继续往下执行。
    print "php: Connected successfully"."\n"; 
    //初始化创建subject表
    $result = dm_exec($link, 'create table subject(name varchar2,score int)') or die("Query failed : " . dm_error()."\n");
	print "php: create table success"."\n";
    //插入数据
    $result = dm_exec($link, "insert into subject values('语文',119)") or die("Query failed : " . dm_error()."\n");  
    print "php: insert success"."\n"; 
	//插入数据
    $result = dm_exec($link, "insert into subject values('数学',133)") or die("Query failed : " . dm_error()."\n");  
    print "php: insert success"."\n"; 
	//插入数据
    $result = dm_exec($link, "insert into subject values('英语',140)") or die("Query failed : " . dm_error()."\n");  
    print "php: insert success"."\n"; 

    //删除数据
    $result = dm_exec($link, "delete from subject where name='数学'") or die("Query failed : " . dm_error()."\n");  
    print "php: delete success"."\n"; 

    //更新数据
    $result = dm_exec($link, 'update subject set name = \'英语-新课标\' where name=\'英语\'') or die("Query failed : " . dm_error()."\n");  
    print "php: update success"."\n"; 

    //查询数据
    $result = dm_exec($link, "select * from subject") or die("Query failed : " . dm_error()."\n");  
    print "<table border=\"1\" cellspacing=\"1\" cellpadding=\"1\">\n"; 
    while ($line = dm_fetch_array($result)) 
    { 
    print "\t<tr>\n"; 
    foreach ($line as $col_value) { 
            print "\t\t<td>$col_value</td>\n"; 
        } 
            print "\t</tr>\n"; 
    } 
    print "</table>\n";  

    /*释放资源*/ 
    dm_free_result($result); 
    print "php: select success"."\n"; 

    /*断开连接*/ 
    dm_close($link); 
}
catch(Exception $e)
{
    $e->getMessage() . "<br/>";
}
?>
```

使用 php  php_create.php 运行。

```
php  php_create.php
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240126144728TFJ7ZSRCU9STHKGVQN)

## 五、参考

1．示例代码下载：[PHP_TEST.zip](https://eco.dameng.com/eco-file-server/file/eco/download/20240126145539P42NMSQXI5P8BWV8KE) 文件。

2．以上文档内容参考过程中遇到任何问题，可到[达梦技术社区](https://eco.dameng.com/community/question/)提问交流。
