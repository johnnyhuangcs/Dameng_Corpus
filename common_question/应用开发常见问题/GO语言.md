

# GO语言

## 前言

本章节主要介绍 GO 语言应用开发常见问题，为用户提供 GO 语言应用开发常见问题的分析和解决思路。除此之外，用户还可前往[达梦技术社区](https://eco.dameng.com/community/question/)参与更多问题讨论。

## 目录

  * unrecognized relocation (0x2a) in section '.text'
  * ODBC 的连接方式，golang 开发，text 类型不支持过滤查询，会报 data type mismatch 的错误
  * 数据库字段为 text 类型，go 使用 string 类型查询数据库报错
  * Go 驱动包不放在 go 安装目录下的 src 目录下可以吗？
  * 使用 go 连接数据库报错
  * Go 使用 zorm 连接 dm 数据库在 Linux 下启动报错 invalid memory address or nil pointer dereference
  * Go 语言达梦数据库是否支持 gorm 连接
  * Go 连接达梦数据库失败，因为密码中包含特殊字符，怎么处理？
  * Github 上的代码和 gitee 上的代码没有同步更新么?
  * Go 驱动使用 time 类型查询，结果异常，有时区的问题
  * Go 语言连接达梦数据库报错 SQLDriverConnect: {HY000} 加密模块加载失败
  * Go 设置 autoCommit 为 false 以后，插入语句成功，但数据库中查不到对应的数据



## 正文

### unrecognized relocation (0x2a) in section '.text'

【问题描述】

在 centos7 环境下运行可能会报该错误，报错信息如下：

![图片 1.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202405211119581OWVTROWLZDKBGQMY5)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml24260\wps1.jpg)

【问题解决】

操作系统的 GNU 的 Binutils 版本过低，升级该版本到 2.26 。

```
[root@localhost go_code]# ld -v
GNU ld version 2.23.52.0.1-30.el7 20130226
```

binutils 的下载地址为：https://ftp.gnu.org/gnu/binutils/

```
## 前往官网下载 binutils 包
## 解压 binutils 包
[root@localhost go_code]# tar -zxvf binutils-2.26.tar.gz

## 编译运行
[root@localhost go_code]# cd binutils-2.26
[root@localhost binutils-2.26]# ./configure --prefix=/usr/local/binutils
[root@localhost binutils-2.26]# make
[root@localhost binutils-2.26]# make install

## 配置环境变量
[root@localhost ~]# vi ~/.bash_profile
# 添加 binutils 的目录
export PATH=/usr/local/binutils/bin:$PATH
[root@localhost ~]# source ~/.bash_profile 

## 查看版本
[root@s11g binutils-2.26]# ld -v
GNU ld (GNU Binutils) 2.26.201601
```

### ODBC 的连接方式，golang 开发，text 类型不支持过滤查询，会报 data type mismatch 的错误

【问题描述】

ODBC 的连接方式，golang 开发，发现 text 类型不支持过滤查询，会报 data type mismatch 的错误，同样 blob 也是这个问题，因为客户端的数据只能是 string 和 varbinary 类型。

【问题解决】

大字段类型不支持比较和排序，此类需求可以采用 TEXT_EQUAL 函数进行处理。例如：

```
db.Prepare(" select * from t_t1 WHERE text_equal(C2,?)“);
```

### 数据库字段为 text 类型，go 使用 string 类型查询数据库报错

【问题描述】

数据库字段为 text 类型，报错如下：

```
sql: Scan error on column index 1, name “CONTENT”: unsupported Scan, storing driver.Value type *dm.DmClob into type *string
```

【问题解决】

Go 驱动的 text 是 dm.DmClob  类型，不能使用 string 类型接收，需改为大字段类型。

### GO 驱动包不放在 go 安装目录下的 src 目录下可以吗？

【问题描述】

Go 驱动包不放在 go 安装目录下的 src 目录下可以吗

【问题解决】

govendor 方式项目必须放在 GOPATH/src 下，包查找顺序为 vendor->GOPATH/src->GOROOT/src ；go mod 方式项目不必须放在 GOPATH/src 下，包查找顺序为 GOPATH/pkg/mod->GOROOT/src 。

### 使用 go 连接数据库报错

【问题描述】

go 1.19.1 中使用 dm8 作为数据库，打开数据库始终报错，报错如下：

```
Error 9001: error.dsn.invalidSchema stack info:
```

【问题解决】

特殊符号问题，特殊符号需要转换。特殊符号可参考以下表。

如果密码有特殊字符，在 URL 上使用报错，则参考 w3schools 网站编码参考表，用特殊字符替换。

|**Character**|**From Windows-1252**|**From UTF-8**|
|-----------------|-----------------|-----------------|
|space|%20|%20|
|!|%21|%21|
|"|%22|%22|
|#|%23|%23|
|$|%24|%24|
|%|%25|%25|
|&|%26|%26|
|'|%27|%27|
|(|%28|%28|
|)|%29|%29|
|*|%2A|%2A|
|+|%2B|%2B|
|,|%2C|%2C|
|-|%2D|%2D|
|.|%2E|%2E|
|/|%2F|%2F|
|0|%30|%30|
|1|%31|%31|
|2|%32|%32|
|3|%33|%33|
|4|%34|%34|
|5|%35|%35|
|6|%36|%36|
|7|%37|%37|
|8|%38|%38|
|9|%39|%39|
|:|%3A|%3A|
|;|%3B|%3B|
|\<|%3C|%3C|
|=|%3D|%3D|
|>|%3E|%3E|
|?|%3F|%3F|
|@|%40|%40|
|A|%41|%41|
|B|%42|%42|
|C|%43|%43|
|D|%44|%44|
|E|%45|%45|
|F|%46|%46|
|G|%47|%47|
|H|%48|%48|
|I|%49|%49|
|J|%4A|%4A|
|K|%4B|%4B|
|L|%4C|%4C|
|M|%4D|%4D|
|N|%4E|%4E|
|O|%4F|%4F|
|P|%50|%50|
|Q|%51|%51|
|R|%52|%52|
|S|%53|%53|
|T|%54|%54|
|U|%55|%55|
|V|%56|%56|
|W|%57|%57|
|X|%58|%58|
|Y|%59|%59|
|Z|%5A|%5A|
|[|%5B|%5B|
|\|%5C|%5C|
|]|%5D|%5D|
|^|%5E|%5E|
|_|%5F|%5F|
|\`|%60|%60|
|a|%61|%61|
|b|%62|%62|
|c|%63|%63|
|D|%44|%44|
|E|%45|%45|
|F|%46|%46|
|G|%47|%47|
|H|%48|%48|
|I|%49|%49|
|J|%4A|%4A|
|K|%4B|%4B|
|L|%4C|%4C|
|M|%4D|%4D|
|N|%4E|%4E|
|O|%4F|%4F|
|P|%50|%50|
|Q|%51|%51|
|R|%52|%52|
|S|%53|%53|
|T|%54|%54|
|U|%55|%55|
|V|%56|%56|
|W|%57|%57|
|X|%58|%58|
|Y|%59|%59|
|Z|%5A|%5A|
|[|%5B|%5B|
|\|%5C|%5C|
|]|%5D|%5D|
|^|%5E|%5E|
|_|%5F|%5F|
|\`|%60|%60|
|a|%61|%61|
|b|%62|%62|
|c|%63|%63|
|d|%64|%64|
|e|%65|%65|
|f|%66|%66|
|g|%67|%67|
|h|%68|%68|
|i|%69|%69|
|j|%6A|%6A|
|k|%6B|%6B|
|l|%6C|%6C|
|m|%6D|%6D|
|n|%6E|%6E|
|o|%6F|%6F|
|p|%70|%70|
|q|%71|%71|
|r|%72|%72|
|s|%73|%73|
|t|%74|%74|
|u|%75|%75|
|v|%76|%76|
|w|%77|%77|
|x|%78|%78|
|y|%79|%79|
|z|%7A|%7A|
|{|%7B|%7B|
|||%7C|
|}|%7D|%7D|
|~|%7E|%7E|
||%7F|%7F|
|\`|%80|%E2%82%AC|
||%81|%81|
|‚|%82|%E2%80%9A|
|ƒ|%83|%C6%92|
|„|%84|%E2%80%9E|
|…|%85|%E2%80%A6|
|†|%86|%E2%80%A0|
|‡|%87|%E2%80%A1|
|ˆ|%88|%CB%86|
|‰|%89|%E2%80%B0|
|Š|%8A|%C5%A0|
|‹|%8B|%E2%80%B9|
|Œ|%8C|%C5%92|
||%8D|%C5%8D|
|Ž|%8E|%C5%BD|
||%8F|%8F|
||%90|%C2%90|
|‘|%91|%E2%80%98|
|’|%92|%E2%80%99|
|“|%93|%E2%80%9C|
|”|%94|%E2%80%9D|
|•|%95|%E2%80%A2|
|–|%96|%E2%80%93|
|—|%97|%E2%80%94|
|˜|%98|%CB%9C|
|™|%99|%E2%84|
|š|%9A|%C5%A1|
|›|%9B|%E2%80|
|œ|%9C|%C5%93|
||%9D|%9D|
|ž|%9E|%C5%BE|
|Ÿ|%9F|%C5%B8|
||%A0|%C2%A0|
|¡|%A1|%C2%A1|
|¢|%A2|%C2%A2|
|£|%A3|%C2%A3|
|¤|%A4|%C2%A4|
|¥|%A5|%C2%A5|
|¦|%A6|%C2%A6|
|§|%A7|%C2%A7|
|¨|%A8|%C2%A8|
|©|%A9|%C2%A9|
|ª|%AA|%C2%AA|
|«|%AB|%C2%AB|
|¬|%AC|%C2%AC|
||%AD|%C2%AD|
|®|%AE|%C2%AE|
|¯|%AF|%C2%AF|
|°|%B0|%C2%B0|
|±|%B1|%C2%B1|
|²|%B2|%C2%B2|
|³|%B3|%C2%B3|
|´|%B4|%C2%B4|
|µ|%B5|%C2%B5|
|¶|%B6|%C2%B6|
|·|%B7|%C2%B7|
|¸|%B8|%C2%B8|
|¹|%B9|%C2%B9|
|º|%BA|%C2%BA|
|»|%BB|%C2%BB|
|¼|%BC|%C2%BC|
|½|%BD|%C2%BD|
|¾|%BE|%C2%BE|
|¿|%BF|%C2%BF|
|À|%C0|%C3%80|
|Á|%C1|%C3%81|
|Â|%C2|%C3%82|
|Ã|%C3|%C3%83|
|Ä|%C4|%C3%84|
|Å|%C5|%C3%85|
|Æ|%C6|%C3%86|
|Ç|%C7|%C3%87|
|È|%C8|%C3%88|
|É|%C9|%C3%89|
|Ê|%CA|%C3%8A|
|Ë|%CB|%C3%8B|
|Ì|%CC|%C3%8C|
|Í|%CD|%C3%8D|
|Î|%CE|%C3%8E|
|Ï|%CF|%C3%8F|
|Ð|%D0|%C3%90|
|Ñ|%D1|%C3%91|
|Ò|%D2|%C3%92|
|Ó|%D3|%C3%93|
|Ô|%D4|%C3%94|
|Õ|%D5|%C3%95|
|Ö|%D6|%C3%96|
|×|%D7|%C3%97|
|Ø|%D8|%C3%98|
|Ù|%D9|%C3%99|
|Ú|%DA|%C3%9A|
|Û|%DB|%C3%9B|
|Ü|%DC|%C3%9C|
|Ý|%DD|%C3%9D|
|Þ|%DE|%C3%9E|
|ß|%DF|%C3%9F|
|à|%E0|%C3%A0|
|á|%E1|%C3%A1|
|â|%E2|%C3%A2|
|ã|%E3|%C3%A3|
|ä|%E4|%C3%A4|
|å|%E5|%C3%A5|
|æ|%E6|%C3%A6|
|ç|%E7|%C3%A7|
|è|%E8|%C3%A8|
|é|%E9|%C3%A9|
|ê|%EA|%C3%AA|
|ë|%EB|%C3%AB|
|ì|%EC|%C3%AC|
|í|%ED|%C3%AD|
|î|%EE|%C3%AE|
|ï|%EF|%C3%AF|
|ð|%F0|%C3%B0|
|ñ|%F1|%C3%B1|
|ò|%F2|%C3%B2|
|ó|%F3|%C3%B3|
|ô|%F4|%C3%B4|
|õ|%F5|%C3%B5|
|ö|%F6|%C3%B6|
|÷|%F7|%C3%B7|
|ø|%F8|%C3%B8|
|ù|%F9|%C3%B9|
|ú|%FA|%C3%BA|
|û|%FB|%C3%BB|
|ü|%FC|%C3%BC|
|ý|%FD|%C3%BD|
|þ|%FE|%C3%BE|
|ÿ|%FF|%C3%BF|


ASCII 控制字符 %00-%1F 最初设计用于控制硬件设备。控制字符在 URL 中没有任何作用，如果有控制字符则参考控制字符编码参考表：

|**ASCII Character**|**Description**|**URL-encoding**|
|---------------|---------------|---------------|
|NUL|null character|%00|
|SOH|start of header|%01|
|STX|start of text|%02|
|ETX|end of text|%03|
|EOT|end of transmission|%04|
|ENQ|enquiry|%05|
|ACK|acknowledge|%06|
|BEL|bell (ring)|%07|
|BS|backspace|%08|
|HT|horizontal tab|%09|
|LF|line feed|%0A|
|VT|vertical tab|%0B|
|FF|form feed|%0C|
|CR|carriage return|%0D|
|SO|shift out|%0E|
|SI|shift in|%0F|
|DLE|data link escape|%10|
|DC1|device control 1|%11|
|DC2|device control 2|%12|
|DC3|device control 3|%13|
|DC4|device control 4|%14|
|NAK|negative acknowledge|%15|
|SYN|synchronize|%16|
|ETB|end transmission block|%17|
|CAN|cancel|%18|
|EM|end of medium|%19|
|SUB|substitute|%1A|
|ESC|escape|%1B|
|FS|file separator|%1C|
|GS|group separator|%1D|
|RS|record separator|%1E|
|US|unit separator||


### Go 使用 zorm 连接 dm 数据库在 Linux 下启动报错 invalid memory address or nil pointer dereference

【问题描述】

Go 使用 zorm 连接 dm 数据库在 Linux 下启动，报错如下：

```
invalid memory address or nil pointer dereference 
```

【问题解决】

原因是获取系统默认的 dm_svc.conf 配置文件失败，在 linux 虚拟机的 /etc 路径下创建 dm_svc.conf 配置文件，内容与 Windows 的 os.Getenv("SystemRoot") + "\system32\dm_svc.conf" 路径下的 dm_svc.conf 配置文件保持一致即可。

![图片 2.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202405211131066Z2870N5E4DNCIDG2X)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml24260\wps2.jpg)

### Go 语言达梦数据库是否支持 gorm 连接

【问题描述】

达梦数据库如何使用 gorm 连接，官方提供的是 xorm 连接。

【问题解决】

达梦支持 gorm 和 xorm 连接，请参考[达梦在线服务平台](https://eco.dameng.com/) -> 文档 -> 应用开发指南 -> GO 语言 -> ODBC 接口和 DM 接口部分。

### Go 连接达梦数据库失败，因为密码中包含特殊字符，怎么处理？

【问题描述】

密码是：abc!@#$%^ ，无论是将这密码用双引号或单引号包含都报错误：net/url: invalid userinfo 。

【问题解决】

先下载 go 的 .net 包，命令为 `go get golang.org/x/net`，使用 url.PathEscape 进行转义后再放入连接串。

![图片 3.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240521113415ZU02Q5874757I80B1W)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml24260\wps3.jpg)

### Github 上的代码和 gitee 上的代码没有同步更新么?

【问题描述】

怎么在 github 上获取 dm 相关 go 驱动。

【问题解决】

Go 驱动达梦只在安装包里提供，git 上的代码不是官方上传和维护的，建议从达梦安装包里获取新的驱动。

达梦的 go 驱动目前官方没有提供线上下载地址，最新的 go 驱动，请下载最新的数据库安装包后，在 drivers\go 文件夹中获取。

### Go 驱动使用 time 类型查询，结果异常，有时区的问题

【问题描述】

Go 驱动使用 time 类型查询，结果异常，有时区的问题。

【问题解决】

time.Parse 使用的是格林威治时区即 0 时区，达梦使用的是操作系统时区默认 +8:00 时区，可以使用 `time.ParseInLocation` 来绕过此时区差异问题。

### Go 语言连接达梦数据库报错 SQLDriverConnect:  加密模块加载失败

【问题描述】

使用加密时，go 语言连接达梦数据库报错：SQLDriverConnect: {HY000} 加密模块加载失败 。

【问题解决】

将数据库安装目录下的 bin 目录下的 libcrypto.so 和 libssl.so 文件拷贝到 /usr/lib 或者 /usr/lib64 目录下即可。

### Go 设置 autoCommit 为 false 以后，插入语句成功，但数据库中查不到对应的数据

【问题描述】

Go 设置 autoCommit 为 false 以后，插入语句成功，但数据库中查不到对应的数据。

【问题解决】

设置 connection.setautocommit(false) 后，只有程序调用 connection.commit() 的时候才会将先前执行的语句一起提交到数据库，这样就实现了数据库的事务。设置为 false 以后，任何插入都需要提交操作 connection.commit() 。
