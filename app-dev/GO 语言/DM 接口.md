

# DM 接口

本章节主要介绍使用 GO 语言开发时，使用 DM 驱动连接达梦数据库。

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

### 三、添加 DM GO 驱动

达梦 GO 驱动位于 C:\dmdbms\drivers\go 下，进行解压后复制到 go 安装目录的 src 目录下。

![图片 2.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240221165240TTY2ZBAM3MNAFB4FLT)

![图片 3.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202402211652499B7S29YJXSCUKZWZJ1)

> **注意**
>
> windows 下部分版本 dm 驱动可能有循环调用问题，可以使用下面的 dm 包，经测试在 linux 下也可以使用



[dm.zip](https://eco.dameng.com/eco-file-server/file/eco/download/202403181403465QFEUR24ICPVBCN42C)

### 四、开发步骤（idea 开发）

本次例子是使用 govendor 方式进行依赖。

  1. 提前准备好图片, 本次例子图片在 C:\Users\Administrator\Desktop\th.jpg 。
  2. 下载模块。



方法一：使用终端下载好所需的模块,这种方法是使用 git 进行下载，不需要 go.mod 。

```
go env -w GO111MODULE=on
go get github.com/golang/snappy
go get golang.org/x/text
go env -w GO111MODULE=off
```

![图片 1.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2024022116382594F4U3B4WSQ7KNEIWR)

方法二：使用 git 单独下载。

执行以下命令，注意要把文件下载到 govendor 方式能读取到的目录，本次例子下载 mod 到的目录是 go 安装目录的 src 目录下。

先使用终端执行：

```
go env -w GO111MODULE=off
```

再使用 git 工具执行：

```
git clone https://github.com/golang/text.git "C:\go\go\src\golang.org\x\text" 
git clone https://github.com/golang/snappy "C:\go\go\src\github.com\golang\snappy"
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202402231524381WA7MES7IG7XMW4D4X)

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240223152624SVJC0WMJNA251C1WCH)

  3. 可能出现的缺失。



由于操作系统以及版本的不同，部分系统缺少 github 包不同，使用 git 进行单独下载。

![图片 1.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240318140649WLEP4S6Z289M9LWGA5)

例如，执行以下命令：

```
git clone https://github.com/jinzhu/gorm "C:\达梦\go\go\src\github.com\jinzhu/gorm"
```

像这样一个个下载引用即可。

  4. 使用 git clone 命令可能出现需要登陆的情况。



进入 github 官网注册账号，地址为 [https://github.com/](https://github.com/) 。

![图片 2.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240318140828KR5QH41YSJXOZYLSY5)

或者出现此页面

![图片 3.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240318140847MBRIBQQE14BEP3ECI0)

输入账号密码登陆，或者一直执行 git clone 命令，每次需要登陆，取消此命令执行，然后再次执行，有时候不用登陆就能下载成功。

![图片 4.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240318140945DGPS3G0W8VM2BNV8BZ)

![图片 5.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240318140954GTVEUMF0MBBM183IM7)

  5. 编写 godm.go ,执行代码。



```
/*该例程实现插入数据，修改数据，删除数据，数据查询等基本操作。*/
package main
// 引入相关包
import (
	"database/sql"
	"dm"
	"fmt"
	"io/ioutil"
	"time"
)
var db *sql.DB
var err error
func main() {
	driverName := "dm"
	dataSourceName := "dm://SYSDBA:*****@localhost:5236"
	if db, err = connect(driverName, dataSourceName); err != nil {
		fmt.Println(err)
		return
	}
	if err = insertTable(); err != nil {
		fmt.Println(err)
		return
	}
	if err = updateTable(); err != nil {
		fmt.Println(err)
		return
	}
	if err = queryTable(); err != nil {
		fmt.Println(err)
		return
	}
	if err = deleteTable(); err != nil {
		fmt.Println(err)
		return
	}
	if err = disconnect(); err != nil {
		fmt.Println(err)
		return
	}
}
/* 创建数据库连接 */
func connect(driverName string, dataSourceName string) (*sql.DB, error) {
	var db *sql.DB
	var err error
	if db, err = sql.Open(driverName, dataSourceName); err != nil {
		return nil, err
	}
	if err = db.Ping(); err != nil {
		return nil, err
	}
	fmt.Printf("connect to \"%s\" succeed.\n", dataSourceName)
	return db, nil
}
/* 往产品信息表插入数据 */
func insertTable() error {
	var inFileName = "C:\\Users\\Administrator\\Desktop\\th.jpg"
	var sql = `INSERT INTO production.product(name,author,publisher,publishtime,
product_subcategoryid,productno,satetystocklevel,originalprice,nowprice,discount,
description,photo,type,papertotal,wordtotal,sellstarttime,sellendtime)
VALUES(:1,:2,:3,:4,:5,:6,:7,:8,:9,:10,:11,:12,:13,:14,:15,:16,:17);`
	data, err := ioutil.ReadFile(inFileName)
	if err != nil {
		return err
	}
	t1, _ := time.Parse("2006-Jan-02", "2005-Apr-01")
	t2, _ := time.Parse("2006-Jan-02", "2006-Mar-20")
	t3, _ := time.Parse("2006-Jan-02", "1900-Jan-01")
	_, err = db.Exec(sql, "三国演义", "罗贯中", "中华书局", t1, 4, "9787101046126", 10, 19.0000, 15.2000,
		8.0,
		"《三国演义》是中国第一部长篇章回体小说，中国小说由短篇发展至长篇的原因与说书有关。",
		data, "25", 943, 93000, t2, t3)
	if err != nil {
		return err
	}
	fmt.Println("insertTable succeed")
	return nil
}
/* 修改产品信息表数据 */
func updateTable() error {
	var sql = "UPDATE production.product SET name = :name WHERE productid = 11;"
	if _, err := db.Exec(sql, "三国演义（上）"); err != nil {
		return err
	}
	fmt.Println("updateTable succeed")
	return nil
}
/* 查询产品信息表 */
func queryTable() error {
	var productid int
	var name string
	var author string
	var description dm.DmClob
	var photo dm.DmBlob
	var sql = "SELECT productid,name,author,description,photo FROM production.product WHERE productid=11"
	rows, err := db.Query(sql)
	if err != nil {
		return err
	}
	defer rows.Close()
	fmt.Println("queryTable results:")
	for rows.Next() {
		if err = rows.Scan(&productid, &name, &author, &description, &photo); err != nil {
			return err
		}
		blobLen, _ := photo.GetLength()
		fmt.Printf("%v %v %v %v %v\n", productid, name, author, description, blobLen)
	}
	return nil
}
/* 删除产品信息表数据 */
func deleteTable() error {
	var sql = "DELETE FROM production.product WHERE productid = 12;"
	if _, err := db.Exec(sql); err != nil {
		return err
	}
	fmt.Println("deleteTable succeed")
	return nil
}
/* 关闭数据库连接 */
func disconnect() error {
	if err := db.Close(); err != nil {
		fmt.Printf("db close failed: %s.\n", err)
		return err
	}
	fmt.Println("disconnect succeed")
	return nil
}
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202402211641319D35D8IAZ0JE9YYS5T)

### 五、开发步骤（litedex 开发）

本次例子是使用 go mod 方式进行依赖，使用此方法需要 git 和 GO111MODULE 为 on 状态。

  1. 为确保环境先执行以下代码：



```
go env -w GOSUMDB=on
go env -w GOSUMDB=auto
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240223152847STJKJCOH94RXXXWCFY)

  2. 为确保网络下载可以配置代理：



```
go env -w GOPROXY=https://goproxy.cn,direct
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202402231545215HXSNDMX0AYH07PKZI)

  3. 然后执行以下命令初始化 go mod,下载依赖更新：



```
go mod init godm 
go get github.com/golang/snappy
go get golang.org/x/text
go mod tidy
```

> **注意**
>
> 以上命令需要在 $gopath/src 下执行，go mod 命令会初始化 go modules ,会生成 go.mod 文件，命令格式为，go mod
> init [MODULE_PATH]，只能初始化一次，go mod tidy，扫描当前项目的源文件，拉取源码需要的依赖库并将它们添加到 go.mod
> 中，从 go.mod 中移除源码中不需要的依赖库。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240223161558X52W6SU1C8F0DE1N4M)

  4. 根据配置会生成以下文件和文件夹：



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2024022315521068WZ0BURCYFOJXWYRW)

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240223154956UERNTOKX9I47UFVJS2)

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240223155711DIXDKPHMN7XU68L36M)

> **注意**
>
> 创建项目必须在 gopath 的 src 下，否则无法使用 go mod 读取模块。



  5. 可能出现的缺失。



由于操作系统以及版本的不同，部分系统缺少 github 包不同，使用 git 进行单独下载。

![图片 6.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240318141245MSAOLKRTIBM9E6XIO7)

例如，执行以下命令：

```
go get golang.org/x/text/language
```

像这样一个个下载引用即可。

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

根据自己环境添加 PATH , GOPATH , GOROOT：

```
export PATH=$PATH:/go/go/bin
export GOPATH=/root/go
export GOROOT=/go/go
```

使环境变量生效：

```
. /etc/profile
```

### 三、添加 DM GO 驱动

  1. 达梦 GO 驱动位于达梦安装目录下的/drivers/go/下，解压后会生成 dm 文件夹。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240223101325DZ3O1E5B1O7VYBMLM3)

  2. 复制 dm 文件夹到 go 安装目录下的 src 下，本次例子对应的就是 /go/go/src 下。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2024022310171657MT4GB524UX9UXKK2)

### 四、开发步骤（go mod 方式）

  1. 提前准备好图片,本次例子图片是位于 /root/go 下的 th.jpg 。

  2. 提前确定 go mod 开启，如果未开启并执行以下命令。
    
        go env -w GO111MODULE=on
    go env -w GOPROXY=https://goproxy.cn,direct 
    go mod init godm
    go get github.com/golang/snappy
    go get golang.org/x/text
    go mod tidy
    

  3. 编写 godm.go,使用 go run godm.go 执行代码。
    
        /*该例程实现插入数据，修改数据，删除数据，数据查询等基本操作。*/
    package main
    // 引入相关包
    import (
    	"database/sql"
    	"dm"
    	"fmt"
    	"io/ioutil"
    	"time"
    )
    var db *sql.DB
    var err error
    func main() {
    	driverName := "dm"
    	dataSourceName := "dm://SYSDBA:*****@localhost:5236"
    	if db, err = connect(driverName, dataSourceName); err != nil {
    		fmt.Println(err)
    		return
    	}
    	if err = insertTable(); err != nil {
    		fmt.Println(err)
    		return
    	}
    	if err = updateTable(); err != nil {
    		fmt.Println(err)
    		return
    	}
    	if err = queryTable(); err != nil {
    		fmt.Println(err)
    		return
    	}
    	if err = deleteTable(); err != nil {
    		fmt.Println(err)
    		return
    	}
    	if err = disconnect(); err != nil {
    		fmt.Println(err)
    		return
    	}
    }
    /* 创建数据库连接 */
    func connect(driverName string, dataSourceName string) (*sql.DB, error) {
    	var db *sql.DB
    	var err error
    	if db, err = sql.Open(driverName, dataSourceName); err != nil {
    		return nil, err
    	}
    	if err = db.Ping(); err != nil {
    		return nil, err
    	}
    	fmt.Printf("connect to \"%s\" succeed.\n", dataSourceName)
    	return db, nil
    }
    /* 往产品信息表插入数据 */
    func insertTable() error {
    	var inFileName = "/root/go/th.jpg"
    	var sql = \`INSERT INTO production.product(name,author,publisher,publishtime,
    product_subcategoryid,productno,satetystocklevel,originalprice,nowprice,discount,
    description,photo,type,papertotal,wordtotal,sellstarttime,sellendtime)
    VALUES(:1,:2,:3,:4,:5,:6,:7,:8,:9,:10,:11,:12,:13,:14,:15,:16,:17);\`
    	data, err := ioutil.ReadFile(inFileName)
    	if err != nil {
    		return err
    	}
    	t1, _ := time.Parse("2006-Jan-02", "2005-Apr-01")
    	t2, _ := time.Parse("2006-Jan-02", "2006-Mar-20")
    	t3, _ := time.Parse("2006-Jan-02", "1900-Jan-01")
    	_, err = db.Exec(sql, "三国演义", "罗贯中", "中华书局", t1, 4, "9787101046126", 10, 19.0000, 15.2000,
    		8.0,
    		"《三国演义》是中国第一部长篇章回体小说，中国小说由短篇发展至长篇的原因与说书有关。",
    		data, "25", 943, 93000, t2, t3)
    	if err != nil {
    		return err
    	}
    	fmt.Println("insertTable succeed")
    	return nil
    }
    /* 修改产品信息表数据 */
    func updateTable() error {
    	var sql = "UPDATE production.product SET name = :name WHERE productid = 11;"
    	if _, err := db.Exec(sql, "三国演义（上）"); err != nil {
    		return err
    	}
    	fmt.Println("updateTable succeed")
    	return nil
    }
    /* 查询产品信息表 */
    func queryTable() error {
    	var productid int
    	var name string
    	var author string
    	var description dm.DmClob
    	var photo dm.DmBlob
    	var sql = "SELECT productid,name,author,description,photo FROM production.product WHERE productid=11"
    	rows, err := db.Query(sql)
    	if err != nil {
    		return err
    	}
    	defer rows.Close()
    	fmt.Println("queryTable results:")
    	for rows.Next() {
    		if err = rows.Scan(&productid, &name, &author, &description, &photo); err != nil {
    			return err
    		}
    		blobLen, _ := photo.GetLength()
    		fmt.Printf("%v %v %v %v %v\n", productid, name, author, description, blobLen)
    	}
    	return nil
    }
    /* 删除产品信息表数据 */
    func deleteTable() error {
    	var sql = "DELETE FROM production.product WHERE productid = 12;"
    	if _, err := db.Exec(sql); err != nil {
    		return err
    	}
    	fmt.Println("deleteTable succeed")
    	return nil
    }
    /* 关闭数据库连接 */
    func disconnect() error {
    	if err := db.Close(); err != nil {
    		fmt.Printf("db close failed: %s.\n", err)
    		return err
    	}
    	fmt.Println("disconnect succeed")
    	return nil
    }
    



### 五、开发步骤（go vendor 方式）

执行以下命令，下载依赖后关闭 go mod ：

```
go env -w GO111MODULE=on
go env -w GOPROXY=https://goproxy.cn,direct 
go mod init godm
go get github.com/golang/snappy
go get golang.org/x/text
go mod tidy
go env -w GO111MODULE=off
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240223161428NJV223NH4CFRNRDJSE)

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2024022316124945DLV7ULCZO30XY817)

而复制文件到/go/go/src 下并改名：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202402231617455D9VRC4ACRMNTVF6HP)

> **注意**
>
> 下载的文件都有版本号，而 go mod 会进行版本管理，而 govendor 不会管理版本，所以需要复制后并去掉文件名后缀。



代码和 go mod 方式一样。

## 参考

  1. 示例代码下载：[godm.zip](https://eco.dameng.com/eco-file-server/file/eco/download/20240227161430WHOZEY0KQLXRENNBNK) 。
  2. 以上文档内容参考过程中遇到任何问题，可到 [达梦技术社区](https://eco.dameng.com/community/share/) 提问交流。


