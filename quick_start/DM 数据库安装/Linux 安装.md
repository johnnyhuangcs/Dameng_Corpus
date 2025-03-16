

# 安装前准备

## 一、前言

用户在安装 DM 数据库之前需要检查或修改操作系统的配置，以保证 DM 数据库能够正确安装和运行。

本文演示环境如下：

|**操作系统**|**CPU**|**数据库**|
|----|----|----|
|CentOS7|x86_64 架构|dm8_20240116_x86_rh7_64|


 信创环境安装部署也可以参考此篇文章，但需注意 CPU 和操作系统对应的 DM 数据库版本。 

## 二、新建 dmdba 用户

> **注意**
>
> 安装前必须创建 dmdba 用户，禁止使用 root 用户安装数据库。



  1. 创建用户所在的组，命令如下：



```
groupadd dinstall -g 2001
```

  2. 创建用户，命令如下：



```
useradd  -G dinstall -m -d /home/dmdba -s /bin/bash -u 2001 dmdba
```

  3. 修改用户密码，命令如下：



```
passwd dmdba
```

## 三、修改文件打开最大数

在 Linux、Solaris、AIX 和 HP-UNIX 等系统中，操作系统默认会对程序使用资源进行限制。如果不取消对应的限制，则数据库的性能将会受到影响。

永久修改和临时修改。

  * 重启服务器后永久生效。



使用 root 用户打开 `/etc/security/limits.conf` 文件进行修改，命令如下：

```
vi /etc/security/limits.conf
```

在最后需要添加如下配置：

```
dmdba  soft      nice       0
dmdba  hard      nice       0
dmdba  soft      as         unlimited
dmdba  hard      as         unlimited
dmdba  soft      fsize      unlimited
dmdba  hard      fsize      unlimited
dmdba  soft      nproc      65536
dmdba  hard      nproc      65536
dmdba  soft      nofile     65536
dmdba  hard      nofile     65536
dmdba  soft      core       unlimited
dmdba  hard      core       unlimited
dmdba  soft      data       unlimited
dmdba  hard      data       unlimited
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202401240950366O2K7K5TONBNZZJDMA)

> **注意**
>
> 修改配置文件后重启服务器生效。



切换到 dmdba 用户，查看是否生效，命令如下：

```
su - dmdba
```

```
ulimit -a
```

参数配置已生效。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202401240959260UW7J0C11OXTBOXWK9)

  * 设置参数临时生效



可使用 dmdba 用户执行如下命令，使设置临时生效：

```
ulimit -n 65536
ulimit -u 65536
```

> **建议**
>
> 使用永久修改方式进行配置。



## 四、目录规划

1.可根据实际需求规划安装目录，本示例使用默认配置 DM 数据库安装在 /home/dmdba 文件夹下。

2.规划创建实例保存目录、归档保存目录、备份保存目录。

```
##实例保存目录
mkdir -p /dmdata/data 
##归档保存目录
mkdir -p /dmdata/arch
##备份保存目录
mkdir -p /dmdata/dmbak
```

> **注意**
>
> 使用 root 用户建立文件夹，待 dmdba 用户建立完成后需将文件所有者更改为 dmdba 用户，否则无法安装到该目录下



## 五、修改目录权限

将新建的路径目录权限的用户修改为 dmdba，用户组修改为 dinstall。命令如下：

```
chown -R dmdba:dinstall /dmdata/data
chown -R dmdba:dinstall /dmdata/arch
chown -R dmdba:dinstall /dmdata/dmbak
```

给路径下的文件设置 755 权限。命令如下：

```
chmod -R 755 /dmdata/data
chmod -R 755 /dmdata/arch
chmod -R 755 /dmdata/dmbak
```


# 数据库安装

## 一、前言

DM 数据库在 Linux 环境下支持命令行安装和图形化安装，本章节将分别进行详细介绍。

## 二、挂载镜像

切换到 root 用户，将 DM 数据库的 iso 安装包保存在任意位置，例如 /opt 目录下，执行如下命令挂载镜像：

```
cd  /opt
mount -o loop dm8_20240116_x86_rh7_64.iso /mnt
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240124111049S3S5NPD2F7DHJ0UQIQ)

## 三、命令行安装

切换至 dmdba 用户下，在 /mnt 目录下使用命令行安装数据库程序，依次执行以下命令安装 DM 数据库。

```
su - dmdba
cd /mnt
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240124111239KWYG082NT9KLK51C4T)

执行如下命令进行安装。

```
./DMInstall.bin -i
```

按需求选择安装语言，没有 key 文件选择 "n"，时区按需求选择一般选择 “21”，安装类型选择“1”，安装目录按实际情况配置，这里示例使用默认安装位置。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240124112650T19KGAG7IFVF7RE1JH)

数据库安装大概 1~2 分钟，数据库安装完成后，显示如下界面。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2024012411300992W9AYDO7OL5LIEXUO)

数据库安装完成后，需要切换至 root 用户执行上图中的命令 `/home/dmdba/dmdbms/script/root/root_installer.sh` 创建 DmAPService，否则会影响数据库备份。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2024012411312306SUH74CFZHSFOKGZU)

数据库安装完成后还需注册实例才能使用数据库，注册实例可参考[配置实例](https://eco.dameng.com/document/dm/zh-cn/start/dm-instance-linux)章节。

## 四、图形化安装

启用图形化安装界面前需要通过如下命令将图形界面权限放开：

```
[root@localhost mnt]# xhost +
access control disabled, clients can connect from any host
[root@localhost mnt]# echo $DISPLAY
[root@localhost mnt]# su - dmdba
Last login: 四 1月 25 16:41:51 CST 2024 on pts/1
[dmdba@localhost ~]$ export DISPLAY=:0.0
```

切换到 dmdba 用户，进入 /mnt 目录下，执行命令开始图形化安装。

```
[dmdba@localhost ~]$ cd /mnt
[dmdba@localhost mnt]$ ./DM
DM8 Install.pdf  DMInstall.bin  
[dmdba@localhost mnt]$ ./DMInstall.bin 
```

> **注意**
>
> 该方法为本地调用图形化界面，如果希望通过其它机器调用该图形化界面需设置 export DISPLAY=调用图形化机器的IP:0.0，例如，数据库安装机器
> IP 为 10.10.12.25，需要在 IP 为 192.132.32.12 的机器上调用图形化界面，需要设置 export
> DISPLAY=192.132.32.12:0.0



若初始化图形界面失败，当前监视器窗口不支持图形界面，请进入安装文件所在文件夹并使用"./DMInstall.bin -i"进行命令行安装。

图形化界面启动成功后，将弹出【选择语言与时区】页面，默认为简体中文和中国标准时间。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202401251600524IA5FU3Q7JAJ6JUPO5)

点击【确定】后，弹出 DM 数据库安装程序。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240125160116518L84OK7RXAGNSM33)

点击【下一步】后，为许可证协议页面，选择【接受】。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202401251602118KJO9MLDQW4N3Z2A4X)

点击【下一步】后，弹出 key 文件页面，点击【浏览】选择【key 文件】，若没有 key 文件可以直接点击【下一步】，跳过该步骤。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2024012516023504QK0GICSTPO0KOA5T)

点击【下一步】后，弹出选择组件页面，建议选择典型安装，也可根据需要，选择服务器安装、客户端安装和自定义安装。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240125160311RYCJGCGO1AOI0HFO79)

点击【下一步】后，弹出选择安装位置页面，可点击【浏览】选择安装位置，也可安装在默认路径下。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202401251603334YNKW049Q3IBQ9N8O8)

点击【下一步】后，弹出确认安装信息页面，检查安装信息是否准确，确认无误后点击【安装】。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240125160405BABOQ6NWMIXC3KYI00)

点击【安装】后，等待 1~2 分钟即可安装完成，安装完成后弹出执行配置脚本页面，按照页面要求执行该脚本即可。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240125160517S5RSDT16OU6YJJL3KJ)

重新打开一个终端，切换到 root 用户，执行弹出页面中的脚本。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240125160632VQ3FSLTGRRX3ZDNJHI)

脚本执行完成后，点击执行配置脚本页面中的【完成】，弹出提示框，提示是否关闭窗口，选择是，提示数据库安装完成，再点击【完成】按钮，完成数据库安装。

![完成安装](https://download.dameng.com/eco/docs/asset/start/ui-install-success.png)

## 五、配置环境变量

切换到 root 用户进入 dmdba 用户的根目录下，配置对应的环境变量。DM_HOME 变量和动态链接库文件的加载路径在程序安装成功后会自动导入。命令如下：

```
export PATH=$PATH:$DM_HOME/bin:$DM_HOME/tool
```

编辑 .bash_profile，使其最终效果如下图所示：

```
cd /home/dmdba/
```

```
vim .bash_profile
```

![环境变量](https://download.dameng.com/eco/docs/asset/start/dm-home-path.png)

切换至 dmdba 用户下，执行以下命令，使环境变量生效。

```
su - dmdba
```

```
source .bash_profile
```

若需要主动打开配置助手，可使用 dmdba 用户配置实例，进入到 DM 数据库安装目录下的 tool 目录中，使用 `./dbca.sh` 命令打开数据库配置助手。

启用图形界面前需要通过如下方法将图形界面权限放开：

```
[root@localhost mnt]# xhost +
access control disabled, clients can connect from any host
[root@localhost mnt]# echo $DISPLAY
[root@localhost mnt]# su - dmdba
Last login: 四 1月 25 16:41:51 CST 2024 on pts/1
[dmdba@localhost ~]$ export DISPLAY=:0.0
```

进入 DM 安装目录下的 tool 目录，使用如下命令打开 DM 服务查看器，如下所示：

```
[dmdba@localhost ~]$ cd /home/dmdba/dmdbms/tool/
[dmdba@localhost tool]$ ll
[dmdba@localhost tool]$ ./dbca.sh
```


# 配置实例

## 一、前言

DM 数据库在 Linux 环境支持命令行配置实例以及图形化配置实例，本章节将分别进行介绍。

## 二、命令行方式初始化实例

使用 dmdba 用户配置实例，进入到 DM 数据库安装目录下的 bin 目录中。

```
su - dmdba
cd /home/dmdba/dmdbms/bin
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202401241420399ZZM2862TP9GJFY6CU)

使用 dminit 命令初始化实例，dminit 命令可设置多种参数，可执行如下命令查看可配置参数。

```
./dminit help
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20241223101402UROJWBKNWGJSP78OW4)

需要注意的是 页大小 (page_size)、簇大小 (extent_size)、大小写敏感 (case_sensitive)、字符集 (charset) 、空格填充模式 (BLANK_PAD_MODE) 、页检查模式（PAGE CHECK） 等部分参数，一旦确定无法修改，在初始化实例时确认需求后谨慎设置。

部分参数解释如下：

  * page_size：数据文件使用的页大小。取值范围 4、8、16、32，单位：KB。缺省值为 8。可选参数。选择的页大小越大，则 DM 支持的元组长度也越大，但同时空间利用率可能下降。数据库创建成功后无法再修改页大小，可通过系统函数 SF_GET_PAGE_SIZE()获取系统的页大小。
  * extent_size：数据文件使用的簇大小，即每次分配新的段空间时连续的页数。取值范围 16、32、64。单位：页数。缺省值为 16。可选参数。数据库创建成功后无法再修改簇大小，可通过系统函数 SF_GET_EXTENT_SIZE()获取系统的簇大小。
  * case_sensitive： 标识符大小写敏感。当大小写敏感时，小写的标识符应用""括起，否则被系统自动转换为大写；当大小写不敏感时，系统不会转换标识符的大小写，系统比较函数会将大写字母全部转为小写字母再进行比较。取值：Y、y、1 表示敏感；N、n、0 表示不敏感。缺省值为 Y。可选参数。此参数在数据库创建成功后无法修改，可通过系统函数 SF_GET_CASE_SENSITIVE_FLAG()或 CASE_SENSITIVE()查询设置的参数置。
  * charset：字符集选项。取值范围 0、1、2。0 代表 GB18030，1 代表 UTF-8，2 代表韩文字符集 EUC-KR。缺省值为 0。可选参数。此参数在数据库创建成功后无法修改，可通过系统函数 SF_GET_UNICODE_FLAG()或 UNICODE()查询设置的参数置。
  * BLANK_PAD_MODE：设置字符串比较时，结尾空格填充模式是否兼容 ORACLE。1：兼容；0：不兼容。缺省值为 0。可选参数。此参数在数据库创建成功后无法修改，可通过查询 V$PARAMETER 中的 BLANK_PAD_MODE 参数名查看此参数的设置值。
  * PAGE_CHECK：PAGE_CHECK 为页检查模式。取值范围 0、1、2、3。0：禁用页校验；1：开启页校验并使用 CRC 校验；2：开启页校验并使用指定的 HASH 算法进行校验；3：开启页校验并使用快速 CRC 校验。缺省值为 3。可选参数。在数据库创建成功后无法修改。



更多 dminit 参数解释可参考达梦数据库安装目录下 doc 目录中《DM8_dminit 使用手册》。

> **建议**
>
> 在实际使用中，初始化时建议提前设置好 COMPATIBLE_MODE
> 的参数值，便于更好的兼容其他数据库。参数说明：是否兼容其他数据库模式。0：不兼容，1：兼容 SQL92 标准，2：部分兼容 ORACLE，3：部分兼容
> MS SQL SERVER，4：部分兼容 MYSQL，5：兼容 DM6，6：部分兼容 TERADATA，7：部分兼容 POSTGRES。



如果需要附加实例存放路径。此处以初始化实例到 /dmdata/data 目录下为例（执行初始化命令前，需要使用 root 用户授予 /dmdata/data 目录相应权限，可以参考[修改目录权限](https://eco.dameng.com/document/dm/zh-cn/start/install-dm-linux-prepare#%E7%9B%AE%E5%BD%95%E8%A7%84%E5%88%92)），初始化命令如下：

```
./dminit path=/dmdata/data
```

> **注意**
>
> 强烈建议用户在首次安装数据库初始化实例时，立即修改数据库系统用户的初始密码，并设置一定的密码强度，以保障数据安全性。



也可以自定义初始化实例的参数，参考如下示例：

以下命令设置页大小为 32 KB，簇大小为 32 KB，大小写敏感，字符集为 utf_8，数据库名为 DMTEST，实例名为 DBSERVER，端口为 5237，SYSDBA_PWD 为 ******，SYSAUDITOR_PWD 为 ******。

```
./dminit path=/dmdata/data PAGE_SIZE=32 EXTENT_SIZE=32 CASE_SENSITIVE=y
CHARSET=1 DB_NAME=DMTEST INSTANCE_NAME=DBSERVER PORT_NUM=5237 SYSDBA_PWD=******  SYSAUDITOR_PWD=*****
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240124154410DGNZYDED81C3769XEG)

> **注意**
>
> 如果此处自定义了初始化参数，在后面的注册服务和启动数据库等步骤中，请按实际的自定义参数进行操作。



## 三、图形化配置实例

使用图形化界面安装数据库安装完成后，会弹出选择是否初始化数据库页面，选择【初始化】。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240125160752681RK92LR85BOZPJOP)

点击初始化后会弹出数据库配置助手，通过数据库配置助手便可以配置数据库。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202401251608175AXW0M4HIRPSXLGMOW)

### 3.1 手动打开配置助手

若需要主动打开配置助手，可使用 dmdba 用户配置实例，进入到 DM 数据库安装目录下的 tool 目录中，使用 `./dbca.sh` 命令打开数据库配置助手。

启用图形界面前需要通过如下方法将图形界面权限放开：

```
[root@localhost mnt]# xhost +
access control disabled, clients can connect from any host
[root@localhost mnt]# echo $DISPLAY
[root@localhost mnt]# su - dmdba
Last login: 四 1月 25 16:41:51 CST 2024 on pts/1
[dmdba@localhost ~]$ export DISPLAY=:0.0
```

进入 DM 安装目录下的 tool 目录，使用如下命令打开 DM 数据库配置助手，如下所示：

```
[dmdba@localhost ~]$ cd /home/dmdba/dmdbms/tool/
[dmdba@localhost tool]$ ll
[dmdba@localhost tool]$ ./dbca.sh
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202401251657389PU9239YFE9XVF8FIA)

选择创建数据库实例，点击【开始】。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202401251608175AXW0M4HIRPSXLGMOW)

### 3.2 创建数据库模板

进入创建数据库页面的创建数据库模版页签，此处可以根据实际需求选择合适的数据库模板，一般建议选择【一般用途】其它保持默认即可，如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240125160904XEH6RLPKBDBCEOWO1V)

### 3.3 选择数据库实例目录

本例中数据库安装路径为 /dmdba/data，如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202401251610192DX62DGPRJ9IZU4SWK)

### 3.4 输入数据库标识

可自定义输入或保持默认数据库名称、实例名、端口号等参数，如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240125161119AA7X8VDX2UI27T7AHJ)

### 3.5 数据库文件所在位置

此处可选择自定义或保持默认配置路径，如下图所示：

控制文件：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240125161308N4LTJ1AG41C4R8PS8M)

数据文件：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240125161237H7J49UAX631BLBGF4T)

redo 日志文件：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240125161443AJ4TAJG6BWHKEDIN11)

初始化日志：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240125161552EBTTR6XY8V415U58P1)

用户可通过选择或输入确定数据库控制文件、数据文件、日志文件、初始化日志等文件的所在位置，并可通过右侧功能按钮，对文件进行添加或删除。

### 3.6 数据库初始化参数

此处配置可根据实际需求进行配置，如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202401251617307FPALDNILL1UOZ669V)

需要注意的是页大小 、簇大小 、大小写敏感 、字符集 、空格填充模式等部分参数， 一旦确定无法修改 ，需谨慎设置。

常见参数说明：

  1. 数据文件使用的簇大小：默认值 16，可选值： 16、 32、 64，单位：页。
  2. 数据页大小：默认值 8，可选值： 4、 8、 16、 32，单位： KB。
  3. 日志文件大小：默认值 256，单位为： MB，范围为： 64 MB~2 GB。
  4. 大小敏感：默认值 Y，可选值： Y/N， 1/0。
  5. 字符集：默认值 0，可选值： 0[GB18030]， 1[UTF-8]， 2[EUC-KR]。



### 3.7 口令管理

强烈建议用户在首次安装数据库初始化实例时，立即修改数据库系统用户的初始密码，并设置一定的密码强度，以保障数据安全性，如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20241220192551BULZQFZ1FF28SCE4OQ)

用户可输入 SYSDBA，SYSAUDITOR 的密码，如果安装版本为安全版，将会增加 SYSSSO 用户的密码修改。

### 3.8 选择创建示例库

此处建议勾选创建示例库 `BOOKSHOP` 或 `DMHR`，作为测试环境，如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240125161836S8X0TPPY9B3O5OI2QP)

### 3.9 创建数据库摘要

在安装数据库之前，将显示用户通过数据库配置工具设置的相关参数。点击【完成】进行数据库实例的初始化工作，如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240125161911AH8AV4QKJOI2U34R4K)

### 3.10 创建实例

点击【完成】，创建完成数据库实例后，按下图按提示执行脚本完成实例配置。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240125161950UYEBISPEV4QVDL0RCF)

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240125162144C455PIIOT84BSWYR6S)

执行完成后会提示参数修改完成。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240125162705WZOUA7VNNW9X5ZN562)

以 root 用户执行提示的脚本重启数据库使自动优化的参数生效。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240125162726KX43U8ZAZBX85V311X)

创建实例完成。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240125162842AEOQCTTULVUSN5SSYS)

至此达梦数据库就可以开始使用了。


# 注册服务

## 一、命令行注册服务

DM 提供了将 DM 服务脚本注册成操作系统服务的脚本，同时也提供了卸载操作系统服务的脚本。注册和卸载脚本文件所在目录为安装目录的“/script/root”子目录下。

注册服务脚本为 dm_service_installer.sh，用户可以使用注册服务脚本将服务脚本注册成为操作系统服务。注册服务需使用 root 用户进行注册，使用 root 用户进入数据库安装目录的 `/script/root` 下，如下所示：

```
cd /home/dmdba/dmdbms/script/root/
```

注册实例服务，如下所示：

```
[root@localhost root]# ./dm_service_installer.sh -t dmserver -dm_ini /dmdata/data/DMTEST/dm.ini -p DMTEST
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2024012415580996WCIN8G4XUGDF7JE6)

部分参数说明：

|**标志**|**参数**|**说明**|
|--|--|--|
|-t|服务类型|注册服务类型，支持一下服务类型：dmap、dmamon、dmserver、dmwatcher、dmmonitor、dmasmsvr、dmasmsvrm、dmcss、dmcssm。|
|-dm_ini|INI 文件路径|指定服务所需要的 dm.ini 文件路径。|
|-p|服务名后缀|指定服务名后缀，生成的操作系统服务名为“服务脚本模板名，称 + 服务名后缀”。此参数只针对 dmserver、dmwatcher、dmmonitor、dmasmsvr、dmasmsvrm、dmcss、dmcssm 服务脚本生效。|


更多参数说明和脚本使用方法可参考数据库安装目录下 doc 目录中 《DM8_Linux 服务脚本使用手册》。

进入数据安装目录下 bin 目录中可以看到已经注册好的服务 DmServiceDMTEST。

```
cd /home/dmdba/dmdbms/bin
ls
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2024012416002194QGJFRNBVILYUC2IO)

## 二、图形化注册服务

如果服务被删除可以通过 dbca 工具即 DM 数据库配置助手来重新注册服务。

启用图形界面前需要通过如下方法将图形界面权限放开：

```
[root@localhost mnt]# xhost +
access control disabled, clients can connect from any host
[root@localhost mnt]# echo $DISPLAY
[root@localhost mnt]# su - dmdba
Last login: 四 1月 25 16:41:51 CST 2024 on pts/1
[dmdba@localhost ~]$ export DISPLAY=:0.0
```

进入 DM 安装目录下的 tool 目录，使用如下命令打开达梦数据库配置助手，如下所示：

```
[dmdba@localhost ~]$ cd /home/dmdba/dmdbms/tool/
[dmdba@localhost tool]$ ll
[dmdba@localhost tool]$ ./dbca.sh
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240125165305RR5O022LX5BI2NNR66)

选择【注册数据库服务】，如下图所示：

![打开 dbca](https://download.dameng.com/eco/docs/asset/start/ui-service-dbca.png)

单击【开始】，弹出注册数据库服务页面，如下图所示：

![注册参数](https://download.dameng.com/eco/docs/asset/start/ui-service-zccs.png)

点击【完成】后，弹出执行配置脚本页面，按页面要求执行脚本即可，如下图所示：

![执行配置脚本](https://download.dameng.com/eco/docs/asset/start/ui-service-shell.png)

执行脚本成功后，该实例已启动，如下图所示：

![终端](https://download.dameng.com/eco/docs/asset/start/ui-service-zd.png)


# 启动、停止数据库

## 一、命令行启停数据库

### 1.1 服务名方式

服务注册成功后，启动数据库。

使用 dmdba 用户进入 DM 安装目录下的 bin 目录下，启动数据库，如下所示：

```
[dmdba@localhost ~]$ cd /home/dmdba/dmdbms/bin
[dmdba@localhost bin]$ ls
[dmdba@localhost bin]$ ./DmServiceDMTEST start
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2024012416062726IRD6IU245WEPVASP)

停止数据库

```
[dmdba@localhost bin]$ ./DmServiceDMTEST stop
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240124160705ALKSBC0M4D8PLC0H1J)

重启数据库

```
[dmdba@localhost bin]$ ./DmServiceDMTEST restart
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240124160913XTK64RTEK6M4IS772Z)

查看数据库状态，如下所示：

```
[dmdba@localhost bin]$ ./DmServiceDMTEST status
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240124160948NJDYYQ1T7RS192OZ4Z)

### 1.2 前台方式

前台方式启动数据库后如果启动界面会话关闭数据库也相应会关闭所以一般情况下建议采用服务的方式启动数据库。

dmdba 用户使用前台的方式启动数据库，进入 DM 安装目录下的 bin 目录下，命令如下：

```
[dmdba@localhost ~]$ cd /home/dmdba/dmdbms/bin
[dmdba@localhost bin]$ ls
[dmdba@localhost bin]$ ./dmserver /dmdata/data/DMTEST/dm.ini
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240124161515RPBKX835X4V08NJ62P)

该启动方式为前台启动，界面输出“SYSTEM IS READY”后表示前台启动成功，若想关闭数据库，输入 exit 退出即可。

## 二、图形化启停数据库

启用图形界面前需要通过如下方法将图形界面权限放开：

```
[root@localhost mnt]# xhost +
access control disabled, clients can connect from any host
[root@localhost mnt]# echo $DISPLAY
[root@localhost mnt]# su - dmdba
Last login: 四 1月 25 16:41:51 CST 2024 on pts/1
[dmdba@localhost ~]$ export DISPLAY=:0.0
```

进入 DM 安装目录下的 tool 目录，使用如下命令打开 DM 服务查看器，如下所示：

```
[dmdba@localhost ~]$ cd /home/dmdba/dmdbms/tool/
[dmdba@localhost tool]$ ll
[dmdba@localhost tool]$ ./dmservice.sh
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2024012516492348VDPQ6PFJDEIQ7VGO)

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240125164423L76YHJW6ODJ3PCZAZR)

可以通过【DM 服务查看器】查看到对应的 DM 服务，可选择【启动】或【停止】对应的服务，如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240125164447WYK4R5JZ7E9IZHALKH)
