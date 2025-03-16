

# GORM 框架

## 一、前言

目前 GO 语言主流的 ORM 框架为 GORM 。GORM 又分为 V1：github.com/jinzhu/gorm 和 V2：gorm.io/gorm 两个版本。两个版本的 GORM 互相不兼容，为此达梦数据库提供了 2 套方言包分别适配。

方言包是位于达梦安装目录的 drivers/go 目录下的 gorm_v1_dialect.zip 和 gorm_v2_dialect.zip 。解压后用户需自行将其添加到包依赖路径中，默认包名为 dm 。

DM GORM 方言包需要依赖 dm 驱动包。如果方言包中引用 DM 驱动包的路径不是 “dm” ，那么需要在方言包的  dialect_dm.go/dm.go 文件中重新调整引用 DM 驱动包的实际路径。由于 GORM V1 自身对标识符是否加双引号策略不一，所以使用 GORM V1 时，仅推荐使用 INI 参数 CASE_SENSITIVE=N 的数据库。

## 二、环境准备

### 2.1 开发环境准备

下载并安装 go、git 。

go 下载地址为 [https://studygolang.com/dl](https://studygolang.com/dl) 。

git 下载地址为 [https://git-scm.com/](https://git-scm.com/) 。

本次 Windows 使用 go 版本为 go1.22.0.windows-amd64， git 版本为 2.43.0.windows.1 。

可以使用 idea 或 litedex 进行编译。

本次 Windows 使用 idea 版本为 IntelliJ IDEA 2022.3.1 (Ultimate Edition) ，Idea 下载地址为 [https://www.jetbrains.com/zh-cn/idea/](https://www.jetbrains.com/zh-cn/idea/) 。

### 2.2 数据库环境准备

数库安装请参考[ 数据库环境准备 ](https://eco.dameng.com/document/dm/zh-cn/app-dev/index.html#%E6%95%B0%E6%8D%AE%E5%BA%93%E7%8E%AF%E5%A2%83%E5%87%86%E5%A4%87)。

## 三、下载框架

使用 git 单独下载，执行以下命令，注意要把文件下载到 govendor 方式能读取到的目录，本次例子下载 mod 到的目录是 go 安装目录的 src 目录下，本次测试 go src 在 C:\go\go\src 下。

使用 git 工具执行：

```
git clone
https://github.com/jinzhu/gorm "C:\go\go\src\github.com\jinzhu/gorm"

git clone
"https://github.com/go-gorm/gorm"  "C:\go\go\src\gorm.io\gorm"
```

## 四、配置依赖模块

1、进入达梦安装目录下 driver/go 目录，复制到 go 安装目录的 src 下，当前环境下 go 驱动位于 C:\dmdbms\drivers\go 下，go src 在 C:\go\go\src 下。

![图片 1.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240516154001JYGN0AUZX5SWBJBZHT)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml29160\wps1.jpg)

2、达梦 GO 驱动位于 C:\dmdbms\drivers\go 下，进行解压后复制到 go 安装目录的 src 目录下。

![图片 2.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240516154031LTVMHA9LRZKR9G73PF)

![图片 3.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240516154044A5IDQPH008GUFEHBYR)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml29160\wps2.jpg)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml29160\wps3.jpg)

> **注意**
>
> windows 下部分版本 dm 驱动可能有循环调用问题，可以使用 dm.zip 包，下载地址为 https://eco.dameng.com/eco-
> file-server/file/eco/download/20240516161152HIOIETMICHH5XFLIRK ，经测试在 linux
> 环境下也可以使用。



![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml29160\wps4.png)

![图片 4.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240516154324GROXBX6F24G2D5FZGU)

![图片 5.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2024051615433488R3QDDQGNXOMME7GN)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml29160\wps5.jpg)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml29160\wps6.jpg)

> **注意**
>
> 如果发生报错等情况，使用下面已整合 gorm1 的 dm 模块压缩包和 gorm2 的 dm 模块压缩包，名称分别为
> dmgorm1.zip、dmgorm2.zip，dmgorm1.zip 下载地址为 https://eco.dameng.com/eco-file-
> server/file/eco/download/20240516154422HH39DNI7AFTUKE3DF9 ，dmgorm2.zip 下载地址为
> https://eco.dameng.com/eco-file-
> server/file/eco/download/202405161544298G5RUFTK0FTE8VJ06W 。



![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml29160\wps7.png)

## 五、示例代码

### 5.1 使用 gorm1 示例

创建 gorm_v1.go 文件，将 code 字段的内容从 L1212 更新为 D42，代码如下：

```
package main
import (
   _ "dmgorm1" // DM驱动包路径
   "github.com/jinzhu/gorm"
)
type Product struct {
   gorm.Model
   Code string
   Price uint
}
func main() {
   db, err := gorm.Open("dm", "dm://SYSDBA:*****@localhost:5238")
   if err != nil {
      panic("failed to connect database")
   }
   defer db.Close()

   // Migrate the schema
   db.AutoMigrate(&Product{})

   // 创建
   db.Create(&Product{Code: "L1212", Price: 1000})

   // 读取
   var product Product
   //此处数字好像无任何作用，只占位，表示取一条数据，与和下一句合并查询
   db.First(&product,1)
   db.First(&product, "\"code\" = ?", "L1212") // 查询code为l1212的product

   db.Model(&product).Update("code", "D42")
   // 更新 - 更新product的price为2000
   db.Model(&product).Update("price", 2000)

   // 删除 - 删除product，运行时会加上删除时间
   db.Delete(&product,1)

   //全部删除
   //db.Unscoped().Delete(&product)
}
```

控制台结果：

![111.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202412231256518UHKNQ5ZNS4KN8VPCI)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml29160\wps9.jpg)

查询数据库中的结果：

![图片 7.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240516161438DV5UYLVA808DU3RKRS)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml29160\wps10.jpg)

### 5.2 使用 gorm2 示例

创建 gorm_v2.go 文件，将 code 字段的内容从 D42 更新为 F42，内容如下：

```
package main
import (
   "dmgorm2"
   "gorm.io/gorm"
   "gorm.io/gorm/logger"
)
type Product struct {
   gorm.Model
   Code string
   Price uint
}
func main() {
   db, err := gorm.Open(dm.Open("dm://SYSDBA:*****@localhost:5236"),
      &gorm.Config{Logger: logger.Default.LogMode(logger.Info),})
   if err != nil {
      panic("failed to connect database")
   }
   // 迁移 schema
   db.AutoMigrate(&Product{})
   // Create
   db.Create(&Product{Code: "D42", Price: 100})
   // Read
   var product Product
   //此处加上会带上id和下一句拼接，查询语句为取一条delete_at为空的id，并把id作为条件加到后面语句
   //db.First(&product) // 根据整型主键查找
   db.First(&product, "\"code\" = ?", "D42") // 查找 code 字段值为 D42 的记录
   // Update - 将 product 的 price 更新为 200
   db.Model(&product).Update("price", 200)
   // Update - 更新多个字段
   db.Model(&product).Updates(Product{Price: 200, Code: "F42"}) // 仅更新非零值字段
   db.Model(&product).Updates(map[string]interface{}{"Price": 200, "Code": "F42"})
   // Delete - 删除 product
   db.Delete(&product,1)
}
```

控制台结果：

![图片 8.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240516161740W21LCWDX6T07902YVL)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml29160\wps11.jpg)

查询数据库中的结果：

![图片 9.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240516161754T43XP4ACUUBGIP2XKU)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml29160\wps12.jpg)

## 六、参考

1、示例代码 [gorm.zip](https://eco.dameng.com/eco-file-server/file/eco/download/20240516161902Z530NSSHH4OYFJNT3J) 。

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml29160\wps13.png)
