

# ODBC接口

本章节主要介绍使用 GO 语言开发时，使用 ODBC 驱动连接达梦数据库。

## Windows 环境下 go 连接数据库

### 一、环境准备

下载并安装 go，git 。

go 下载地址为 [https://studygolang.com/dl](https://studygolang.com/dl) 。

git 下载地址为 [https://git-scm.com/](https://git-scm.com/) 。

本次 Windows 使用 go 版本为 go1.22.0.windows-amd64，git 版本为 2.43.0.windows.1 。

可以使用 Idea 或 litedex 进行编译。

Idea 下载地址为 [https://www.jetbrains.com/zh-cn/idea/](https://www.jetbrains.com/zh-cn/idea/) 。

Litedex 下载地址为 [liteide download | SourceForge.net](https://sourceforge.net/projects/liteide/) 。

本次 Windows 使用 Idea 版本为 IntelliJ IDEA 2022.3.1 (Ultimate Edition) , litedex 版本为 liteidex38.3-win64-qt5.15.2.zip 。

数据库安装请参考[数据库环境准备](https://eco.dameng.com/document/dm/zh-cn/app-dev/#%E6%95%B0%E6%8D%AE%E5%BA%93%E7%8E%AF%E5%A2%83%E5%87%86%E5%A4%87)。

### 二、环境变量配置

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240221162218WFG8HW8CQDVYMYBXDG)

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240221162142ZTC3JJZOR5GAL695QD)

指定 GOPATH 和 GOROOT 。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240221162341UJW32VXDFBBPPCFI4E)

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240221162447FCZFM0YFEEG2L2CD9X)

### 三、创建 ODBC 数据源

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240223105311ZXOA3TJYQM1TTD9JPS)

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202402231053312R3ZI65QGIJWYYSSB1)

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240223105401DTXZSC1VJ8RULCLXWG)

### 四、测试连接

  1. govendor 方式进行依赖。
  2. 下载模块。



方法一：使用终端下载好所需的模块，这种方法是使用 git 进行下载。

```
go env -w GO111MODULE=on
go get  github.com/alexbrainman/odbc
go get github.com/go-xorm/xorm
go env -w GO111MODULE=off
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240223110302SBHL2LIZPK7ZMR166S)

方法二：使用 git 单独下载。

先使用终端执行：

```
go env -w GO111MODULE=off
```

再使用 git 工具执行以下命令，注意要把文件下载到 govendor 方式能读取到的目录，本次例子下载 mod 到的目录是 go 安装目录的 src 目录下。

```
git clone https://github.com/alexbrainman/odbc "C:\go\go\src\github.com\alexbrainman\odbc" 
git clone https://github.com/go-xorm/xorm "C:\go\go\src\github.com\go-xorm\xorm"
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240223165150IEY90B8GZFPZJ6ZA3J)

  3. 可能出现的缺失。



由于操作系统以及版本的不同，部分系统缺少 github 包不同，使用 git 进行单独下载。

![图片 1.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240318143509Z9R4Q92CBZVIDJEVD0)

例如，执行以下命令：

```
git clone https://github.com/jinzhu/gorm "C:\达梦\go\go\src\github.com\jinzhu/gorm"
```

像这样一个个下载引用即可。

  4. 使用 git clone 命令可能出现需要登陆的情况。



进入 github 官网注册账号，地址为 [https://github.com/](https://github.com/) 。

![图片 2.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240318143556Z3AR82GHJNUGLLA4BN)

或者出现此页面

![图片 3.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240318143611R34F7K4XT38VE6DYHU)

输入账号密码登陆，或者一直执行 git clone 命令，每次需要登陆，取消此命令执行，然后再次执行，有时候不用登陆就能下载成功。

![图片 4.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240318143627R17ONCN4AWII9EDHGN)

![图片 5.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240318143639NZX6WFTEJ4BQ7Y1HNZ)

  5. 创建 goodbc.go，执行代码。



```
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  package main

import (
"fmt"
_ "github.com/alexbrainman/odbc"  // google's odbc driver
"github.com/go-xorm/xorm"
"xorm.io/core"
)

type Person struct {
	PersonId     int		 `xorm:"PERSONID"`
	Sex          string		 `xorm:"SEX"`
	Name         string		 `xorm:"NAME"`
	Email        string		 `xorm:"EMAIL"`
	Phone        string 	 `xorm:"PHONE"`
}

func main() {

	Engine, err := xorm.NewEngine("odbc", "driver={DM8 ODBC DRIVER};server=192.168.40.1:5237;database=DM8;uid=SYSDBA;pwd=*****;charset=utf8")
	if err != nil {
		fmt.Println("new engine got error:", err)
		return
	}
	if err := Engine.Ping(); err != nil {
		fmt.Println("ping got error:", err)
		return
	}

	Engine.SetTableMapper(core.SameMapper{})
	Engine.ShowSQL(true)
	Engine.SetMaxOpenConns(5)
	Engine.SetMaxIdleConns(5)

	total, err := Engine.Table("PRODUCTION.PRODUCT").Count(&Person{})
	if nil != err {
		fmt.Println(`engine query got error:`, err.Error())
		return
	}

	fmt.Println("total count is:",total)
}

```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202402231103308PVK6XGS3EDZHM1MHQ)

使用 go mod 方式进行依赖，使用此方法需要 git 和 GO111MODULE 为 on 状态。

为确保环境先执行以下代码：

```
go env -w GOSUMDB=on
go env -w GOSUMDB=auto
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240223152847STJKJCOH94RXXXWCFY)

为确保网络下载可以配置代理：

```
go env -w GOPROXY=https://goproxy.cn,direct
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202402231545215HXSNDMX0AYH07PKZI)

然后执行以下命令初始化 go mod ,下载依赖更新：

```
go mod init godm 
go get  github.com/alexbrainman/odbc
go get github.com/go-xorm/xorm
go get github.com/xorm.io
go mod tidy
```

> **注意**
>
> 以上命令需要在 $gopath/src 下执行，go mod 命令会初始化 go modules ,会生成 go.mod 文件，命令格式为，go mod
> init [MODULE_PATH]，只能初始化一次，go mod tidy，扫描当前项目的源文件，拉取源码需要的依赖库并将它们添加到 go.mod
> 中，从 go.mod 中移除源码中不需要的依赖库。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240223161558X52W6SU1C8F0DE1N4M)

根据配置会生成以下文件和文件夹：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2024022315521068WZ0BURCYFOJXWYRW)

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240223164242X8I492XWT46P1Q0E9Q)

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240223164627C9WZASO8BLBQ47TECA)

## Linux 环境下 go 连接数据库

### 一、环境准备

下载并安装 go，git 。

go 下载地址为 [https://studygolang.com/dl](https://studygolang.com/dl) 。

本次 Linux 使用 go 版本为 go1.22.0.linux-amd64 。

使用命令下载 git ：

```
 yum install git
 yum install git-svn git-email gitk
```

### 二、环境变量配置

修改 /etc/profile 添加 go 环境变量：

```
vi /etc/profile
```

根据自己环境添加 PATH , GOPATH , GOROOT ：

```
export PATH=$PATH:/go/go/bin
export GOPATH=/root/go
export GOROOT=/go/go
```

使环境变量生效：

```
. /etc/profile
```

### 三、配置 odbc 数据源

DMODBC 在 Linux 操作系统依赖于 UnixODBC 库。

此链接为 2.3.12 版本安装包，[https://www.unixodbc.org/unixODBC-2.3.12.tar.gz](https://www.unixodbc.org/unixODBC-2.3.12.tar.gz) 。

如果需要下载其他版本 ,点击链接进入官网 [ https://www.unixodbc.org/](https://www.unixodbc.org/) ，选择合适版本进行下载。

  1. 下载好后，进行解压，使用命令进行配置：



```
./configure --enable -gui=no
```

  2. 配置好后进行编译：



```
make && make install
```

  3. 编译后查找配置文件位置：



```
odbcinst -j
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240202171242YAYY6Q34U0XRV81793)

> **注意**
>
> 如果出现 so 文件缺失的情况，大部分可以通过 yum install unixODBC-devel 方式进行解决，如果还有部分缺失，需要自行下载。



  4. 编辑 \`/etc/odbcinst.ini\` ，如下所示：



```
[DM8 ODBC DRIVER]
Description = ODBC DRIVER FOR DM8
Driver = /home/dmdba/dmdbms/bin/libdodbc.so
```

  5. 编辑 \`/etc/odbc.ini\` ，如下所示：



```
[dm8]
Description = DM ODBC DSN
Driver = DM8 ODBC DRIVER
SERVER = localhost
UID = SYSDBA
PWD = *****
TCP_PORT = 5236
```

> **注意**
>
> odbc.ini 中的 Driver 内容一定要与 odbcinst.ini 中的 DM 驱动定义的节点名称相同。 odbc.ini 中的 SERVER
> 可以输入数据库服务器的 IP 。



  6. 测试连接，出现以下内容说明连接成功：



```
isql  dm8
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202402021732527L6X6W9RT5NJ9GJIV6)

### 四、开发步骤 （go mod 方式）

  1. 执行以下命令： 
    
        go env -w GO111MODULE=on
    go env -w GOPROXY=https://goproxy.cn,direct 
    go mod init godm
    go get  github.com/alexbrainman/odbc
    go get github.com/go-xorm/xorm
    go get github.com/xorm.io
    go mod tidy
    

  2. 创建 goodbc.go ，使用 go run goodbc.go 执行代码： 
    
        package main
    
    import (
    	"fmt"
    	_ "github.com/alexbrainman/odbc"  // google's odbc driver
    	"github.com/go-xorm/xorm"
    	"xorm.io/core"
    )
    
    type Person struct {
    	PersonId     int		 \`xorm:"PERSONID"\`
    	Sex          string		 \`xorm:"SEX"\`
    	Name         string		 \`xorm:"NAME"\`
    	Email        string		 \`xorm:"EMAIL"\`
    	Phone        string 	 \`xorm:"PHONE"\`
    }
    
    func main() {
    
    	Engine, err := xorm.NewEngine("odbc", "driver={DM8 ODBC DRIVER};server=192.168.40.1:5237;database=DM8;uid=SYSDBA;pwd=*****;charset=utf8")
    	if err != nil {
    		fmt.Println("new engine got error:", err)
    		return
    	}
    	if err := Engine.Ping(); err != nil {
    		fmt.Println("ping got error:", err)
    		return
    	}
    
    	Engine.SetTableMapper(core.SameMapper{})
    	Engine.ShowSQL(true)
    	Engine.SetMaxOpenConns(5)
    	Engine.SetMaxIdleConns(5)
    
    	total, err := Engine.Table("PRODUCTION.PRODUCT").Count(&Person{})
    	if nil != err {
    		fmt.Println(\`engine query got error:\`, err.Error())
    		return
    	}
    
    	fmt.Println("total count is:",total)
    }
    
    
    



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240223111548U7KC7BYPGS8CZCG50I)

### 五、开发步骤（go vendor 方式）

执行以下命令，下载依赖后关闭 go mod ：

```
go env -w GO111MODULE=on
go env -w GOPROXY=https://goproxy.cn,direct 
go mod init godm
go get github.com/golang/snappy
go get golang.org/x/text
go get github.com/xorm.io
go mod tidy
go env -w GO111MODULE=off
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240223161428NJV223NH4CFRNRDJSE)

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240223165508ZXEWP0HUWMWGO4HNXW)

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240223165529RCIU5ROP71EOH2B2SK)

而复制文件到/go/go/src 下并改名：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202402231617455D9VRC4ACRMNTVF6HP)

> **注意**
>
> 下载的文件都有版本号，而 go mod 会进行版本管理，而 govendor 不会管理版本，所以需要复制后并去掉文件名后缀。windows 自带
> odbc 驱动，而 linux 环境如果使用odbc驱动则需要 xorm.io。



## 参考

  1. 示例代码下载： [goodbc.zip](https://eco.dameng.com/eco-file-server/file/eco/download/202402271618546NRJU8BCSSRTAQBM2Q) 。
  2. 以上文档内容参考过程中遇到任何问题，可到 [达梦技术社区](https://eco.dameng.com/community/share/) 提问交流。


