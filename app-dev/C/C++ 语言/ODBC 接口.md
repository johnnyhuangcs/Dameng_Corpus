

# ODBC 接口

## 一、前言

ODBC 提供访问不同类型的数据库的途径。结构化查询语言 SQL 是一种用来访问数据库的语言。通过使用 ODBC，应用程序能够使用相同的源代码和各种各样的数据库交互。这使得开发者不需要以特殊的数据库管理系统 DBMS 为目标，或者了解不同支撑背景的数据库的详细细节，就能够开发和发布客户/服务器应用程序。

DM ODBC 3.0 遵照 Microsoft ODBC 3.0 规范设计与开发，实现了 ODBC 应用程序与 DM 数据库的互连接口。用户可以直接调 DM ODBC 3.0 接口函数访问 DM，也可以使用可视化编程工具如 C++ Builder、PowerBuilder 等利用 DM ODBC 3.0 访问 DM 数据库。

## 二、开发环境准备

### 2.1 达梦数据库版本

|**名称**|**版本**|
|--|--|
|DM 数据库|DM 8.0 及以上版本|


### 2.2 达梦数据库安装

请参考[数据库环境准备](https://eco.dameng.com/document/dm/zh-cn/app-dev/#%E6%95%B0%E6%8D%AE%E5%BA%93%E7%8E%AF%E5%A2%83%E5%87%86%E5%A4%87)。

### 2.3 安装 UNIX ODBC

  1. 下载 [unixODBC](https://developer.aliyun.com/packageSearch?word=unixodbc) 和 [unixODBC-devel](https://developer.aliyun.com/packageSearch?word=unixodbc)。
  2. 安装 unixODBC 和 unixODBC-devel。



```
[root@RS1821 wt]# rpm -ivh unixODBC-devel-2.3.1-14.el7.x86_64.rpm unixODBC-2.3.1-14.el7.x86_64.rpm
warning: unixODBC-devel-2.3.1-14.el7.x86_64.rpm: Header V3 RSA/SHA256 Signature, key ID f4a80eb5: NOKEY
Preparing...                          ################################# [100%]
Updating / installing...
   1:unixODBC-2.3.1-14.el7            ################################# [ 50%]
   2:unixODBC-devel-2.3.1-14.el7      ################################# [100%]
[root@RS1821 wt]#
```

  3. 查看安装结果。



```
[root@RS1821 odbc]# odbcinst -j
unixODBC 2.3.1
DRIVERS............: /etc/odbcinst.ini
SYSTEM DATA SOURCES: /etc/odbc.ini
FILE DATA SOURCES..: /etc/ODBCDataSources
USER DATA SOURCES..: /root/.odbc.ini
SQLULEN Size.......: 8
SQLLEN Size........: 8
SQLSETPOSIROW Size.: 8
```

### 2.4 配置 DM 数据源

  1. 配置 /etc/odbcinst.ini 文件，在 odbcinst.ini 文件中添加以下信息：



```
[DM8 ODBC DRIVER]
Description = DM ODBC DRIVER FOR DM8
Driver      = /opt/dm8/bin
```

> **注意**
>
> /opt/dm8/bin 对应 $DM_HOME/bin。



  2. 配置 /etc/odbc.ini 文件，在 odbc.ini 文件中添加以下信息：



```
[DM]
DRIVER = DM8 ODBC DRIVER
SERVER = 192.168.104.21
UID = SYSDBA
PWD = ******
TCP_PORT = 51236
```

### 2.5 gcc 环境检查

```
gcc --version
```

执行结果如下：

```
[root@localhost odbctest]# gcc --version 
gcc (GCC) 4.8.5 20150623 (Red Hat 4.8.5-36)
 Copyright (C) 2015 Free Software Foundation, Inc. 
This is free software; see the source for copying conditions. There is NO warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
```

### 2.6 项目目录结构

```
odbctest
├── odbc_bind.c             // 绑定变量示例
├── odbc_conn.c             // 数据库连接示例
├── odbc_dml.c              // 基本操作示例
├── odbc_lob.c              // 大字段操作示例
├── file                   // 大字段操作示例中导入和导出文件目录
|   ├── DM8_SQL.pdf        // 大字段操作示例中导入文件
├── makfile                // makefile 编译脚本
```

> **注意**
>
> 代码编译和运行前请确认相关文件的用户权限，建议全部使用dmdba用户。



makefile 脚本如下：

```
[dmdba@localhost odbctest]# cat makefile
CC=gcc

includepath=$(DM_HOME)/include
libpath=$(DM_HOME)/bin
vpath=./

CFLAGS=-I$(includepath) -DDM64 -Wall
LINKFLAGS=-L$(libpath) -ldodbc -Wall -Wl,-rpath $(libpath)

%.o:%.c
    $(CC) -g -c $(CFLAGS) $< -o $@

object_file1=odbc_conn.o
object_file2=odbc_dml.o
object_file3=odbc_bind.o
object_file4=odbc_lob.o

object_files=odbc_conn.o odbc_dml.o odbc_bind.o odbc_lob.o

final_objects=odbc_conn odbc_dml odbc_bind odbc_lob

all : $(final_objects)

.PHONY : all clean rebuild

odbc_conn : $(object_file1)
    $(CC) -o $@ $(object_file1) -g $(LINKFLAGS)
    @echo make ok.

odbc_dml : $(object_file2)
    $(CC) -o $@ $(object_file2) -g $(LINKFLAGS)
    @echo make ok.

odbc_bind : $(object_file3)
    $(CC) -o $@ $(object_file3) -g $(LINKFLAGS)
    @echo make ok.

odbc_lob : $(object_file4)
    $(CC) -o $@ $(object_file4) -g $(LINKFLAGS)
@echo make ok.

clean :
    @rm -rf $(object_files)
    @rm -rf $(final_objects)

rebuild : clean all
```

makefile 编译截图：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221214104250NE72TFTMVBFFPAIXOP)

## 三、数据库连接

### 3.1 数据库连接示例

  1. ODBC 接口登录登出示例程序 odbc_conn.c 如下：



```
#include <stdio.h>
#include <stdlib.h>
#include <sql.h>
#include <sqltypes.h>
#include <sqlext.h>

/* 检测返回代码是否为成功标志，当为成功标志返回 TRUE，否则返回 FALSE */
#define RC_SUCCESSFUL(rc) ((rc) == SQL_SUCCESS || (rc) == SQL_SUCCESS_WITH_INFO)
/* 检测返回代码是否为失败标志，当为失败标志返回 TRUE，否则返回 FALSE */
#define RC_NOTSUCCESSFUL(rc) (!(RC_SUCCESSFUL(rc)))

HENV henv;/* 环境句柄 */
HDBC hdbc;/* 连接句柄 */
HSTMT hsmt;/* 语句句柄 */
SQLRETURN sret; /* 返回代码 */

int main(void)
{
    /* 申请一个环境句柄 */
    SQLAllocHandle(SQL_HANDLE_ENV, NULL, &henv);
    /* 设置环境句柄的 ODBC 版本 */
    SQLSetEnvAttr(henv, SQL_ATTR_ODBC_VERSION, (SQLPOINTER)SQL_OV_ODBC3,
        SQL_IS_INTEGER);
    /* 申请一个连接句柄 */
    SQLAllocHandle(SQL_HANDLE_DBC, henv, &hdbc);

    sret = SQLConnect(hdbc, (SQLCHAR *)"DM", SQL_NTS, (SQLCHAR *)"SYSDBA", SQL_NTS, (SQLCHAR *)"******", SQL_NTS);
    if (RC_NOTSUCCESSFUL(sret)) {
        /* 连接数据源失败! */
        printf("odbc: fail to connect to server!\n");
        SQLFreeHandle(SQL_HANDLE_DBC, hdbc);
        SQLFreeHandle(SQL_HANDLE_ENV, henv);
        exit(0);
    }
    printf("odbc: connect to server success!\n");

    /* 断开与数据源之间的连接 */
    SQLDisconnect(hdbc);
    /* 释放连接句柄 */
    SQLFreeHandle(SQL_HANDLE_DBC, hdbc);
    /* 释放环境句柄 */
    SQLFreeHandle(SQL_HANDLE_ENV, henv);

    return 0;
}
```

  2. 执行结果如下：



```
[dmdba@localhost odbctest]# ./odbc_conn 
odbc: connect to server success!
```

## 四、开发示例

### 4.1 基础操作示例

  1. ODBC 接口增、删、改、查四个基本操作，示例程序 odbc_dml.c 如下：



```
#include <stdio.h>
#include <stdlib.h>
#include <sql.h>
#include <sqltypes.h>
#include <sqlext.h>

/* 检测返回代码是否为成功标志，当为成功标志返回 TRUE，否则返回 FALSE */
#define RC_SUCCESSFUL(rc) ((rc) == SQL_SUCCESS || (rc) == SQL_SUCCESS_WITH_INFO)
/* 检测返回代码是否为失败标志，当为失败标志返回 TRUE，否则返回 FALSE */
#define RC_NOTSUCCESSFUL(rc) (!(RC_SUCCESSFUL(rc)))

HENV henv;/* 环境句柄 */
HDBC hdbc;/* 连接句柄 */
HSTMT hstmt;/* 语句句柄 */
SQLRETURN sret; /* 返回代码 */

int main(void)
{
    int     out_c1 = 0;
    SQLCHAR out_c2[20]= { 0 };
    SQLLEN out_c1_ind = 0;
    SQLLEN out_c2_ind = 0;

    /* 申请句柄 */
    SQLAllocHandle(SQL_HANDLE_ENV, NULL, &henv);
    SQLSetEnvAttr(henv, SQL_ATTR_ODBC_VERSION, (SQLPOINTER)SQL_OV_ODBC3, SQL_IS_INTEGER);
    SQLAllocHandle(SQL_HANDLE_DBC, henv, &hdbc);

    sret = SQLConnect(hdbc, (SQLCHAR *)"DM", SQL_NTS, (SQLCHAR *)"SYSDBA", SQL_NTS, (SQLCHAR *)"******", SQL_NTS);
    if (RC_NOTSUCCESSFUL(sret)) {
        printf("odbc: fail to connect to server!\n");
        SQLFreeHandle(SQL_HANDLE_DBC, hdbc);
        SQLFreeHandle(SQL_HANDLE_ENV, henv);
        exit(0);
    }
    printf("odbc: connect to server success!\n");

    /* 申请一个语句句柄 */
    SQLAllocHandle(SQL_HANDLE_STMT, hdbc, &hstmt);

    //清空表，初始化测试环境
    sret = SQLExecDirect(hstmt, (SQLCHAR *) "delete from PRODUCTION.PRODUCT_CATEGORY", SQL_NTS);

    //插入数据
    sret = SQLExecDirect(hstmt, (SQLCHAR *) "insert into PRODUCTION.PRODUCT_CATEGORY(NAME) values('语文'), ('数学'), ('英语'), ('体育') ", SQL_NTS);
    if (RC_NOTSUCCESSFUL(sret)) {
        printf("odbc: insert fail\n");
    }
    printf("odbc: insert success\n");

    //删除数据
    sret = SQLExecDirect(hstmt, (SQLCHAR *) "delete from PRODUCTION.PRODUCT_CATEGORY where name='数学' ", SQL_NTS);
    if (RC_NOTSUCCESSFUL(sret)) {
        printf("odbc: delete fail\n");
    }
    printf("odbc: delete success\n");


    //更新数据
    sret = SQLExecDirect(hstmt, (SQLCHAR *) "update PRODUCTION.PRODUCT_CATEGORY set name = '英语-新课标' where name='英语' ", SQL_NTS);
    if (RC_NOTSUCCESSFUL(sret)) {
        printf("odbc: update fail\n");
    }
    printf("odbc: update success\n");

    //查询数据
    SQLExecDirect(hstmt, (SQLCHAR *) "select * from PRODUCTION.PRODUCT_CATEGORY", SQL_NTS);
    SQLBindCol(hstmt, 1, SQL_C_SLONG, &out_c1, sizeof(out_c1), &out_c1_ind);
    SQLBindCol(hstmt, 2, SQL_C_CHAR, &out_c2, sizeof(out_c2), &out_c2_ind);

    printf("odbc: select from table...\n");
    while(SQLFetch(hstmt) != SQL_NO_DATA)
    {
        printf("c1 = %d, c2 = %s ,\n", out_c1, out_c2);
    }
    printf("odbc: select success\n");

    SQLFreeHandle(SQL_HANDLE_STMT, hstmt);

    SQLDisconnect(hdbc);
    SQLFreeHandle(SQL_HANDLE_DBC, hdbc);
    SQLFreeHandle(SQL_HANDLE_ENV, henv);
    return 0;
}
```

  2. 执行结果如下：



```
[dmdba@localhost odbctest]# ./odbc_dml
odbc: connect to server success!
odbc: insert success
odbc: delete success
odbc: update success
odbc: select from table...
c1 = 19, c2 = 语文 ,
c1 = 21, c2 = 英语-新课标 ,
c1 = 22, c2 = 体育 ,
odbc: select success
```

### 4.2 绑定变量示例

  1. ODBC 接口绑定变量示例程序 odbc_bind.c 如下：



```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sql.h>
#include <sqltypes.h>
#include <sqlext.h>

/* 检测返回代码是否为成功标志，当为成功标志返回 TRUE，否则返回 FALSE */
#define RC_SUCCESSFUL(rc) ((rc) == SQL_SUCCESS || (rc) == SQL_SUCCESS_WITH_INFO)
/* 检测返回代码是否为失败标志，当为失败标志返回 TRUE，否则返回 FALSE */
#define RC_NOTSUCCESSFUL(rc) (!(RC_SUCCESSFUL(rc)))

HENV henv;/* 环境句柄 */
HDBC hdbc;/* 连接句柄 */
HSTMT hstmt;/* 语句句柄 */
SQLRETURN sret; /* 返回代码 */

int main(void)
{
    SQLCHAR     sql[]="insert into PRODUCTION.PRODUCT_CATEGORY(NAME) values(?)";
    SQLCHAR     in_c1[20] = { 0 };
    SQLLEN      in_c1_ind_ptr;

    memcpy(in_c1, "物理", 8);
    in_c1_ind_ptr = 8; 

    int     out_c1 = 0;
    SQLCHAR out_c2[20]= { 0 };
    SQLLEN  out_c1_ind = 0;
    SQLLEN  out_c2_ind = 0;

    /* 申请句柄 */
    SQLAllocHandle(SQL_HANDLE_ENV, NULL, &henv);
    SQLSetEnvAttr(henv, SQL_ATTR_ODBC_VERSION, (SQLPOINTER)SQL_OV_ODBC3, SQL_IS_INTEGER);
    SQLAllocHandle(SQL_HANDLE_DBC, henv, &hdbc);

    sret = SQLConnect(hdbc, (SQLCHAR *)"DM", SQL_NTS, (SQLCHAR *)"SYSDBA", SQL_NTS, (SQLCHAR *)"******", SQL_NTS);
    if (RC_NOTSUCCESSFUL(sret)) {
        printf("odbc: fail to connect to server!\n");
        SQLFreeHandle(SQL_HANDLE_DBC, hdbc);
        SQLFreeHandle(SQL_HANDLE_ENV, henv);
        exit(0);
    }
    printf("odbc: connect to server success!\n");

    /* 申请一个语句句柄 */
    SQLAllocHandle(SQL_HANDLE_STMT, hdbc, &hstmt);

    //清空表，初始化测试环境
    sret = SQLExecDirect(hstmt, (SQLCHAR *) "delete from PRODUCTION.PRODUCT_CATEGORY", SQL_NTS);

    //绑定参数方式插入数据
    printf( "insert with bind..\nsql: %s\npara: %s\n", (char*)sql, (char*)in_c1);
    sret = SQLPrepare(hstmt, sql, SQL_NTS);
    sret = SQLBindParameter(hstmt, 1, SQL_PARAM_INPUT, SQL_C_CHAR, SQL_CHAR, sizeof(in_c1), 0, in_c1, 0, &in_c1_ind_ptr);
    sret = SQLExecute(hstmt);
    if (RC_NOTSUCCESSFUL(sret)) {
        printf( "odbc: insert into table with bind fail!\n" );
    }
    printf( "odbc: insert into table with bind success!\n" );

    //查询数据
    SQLExecDirect(hstmt, (SQLCHAR *) "select * from PRODUCTION.PRODUCT_CATEGORY", SQL_NTS);
    SQLBindCol(hstmt, 1, SQL_C_SLONG, &out_c1, sizeof(out_c1), &out_c1_ind);
    SQLBindCol(hstmt, 2, SQL_C_CHAR, &out_c2, sizeof(out_c2), &out_c2_ind);

    printf("odbc: select from table...\n");
    while(SQLFetch(hstmt) != SQL_NO_DATA)
    {
        printf("c1 = %d, c2 = %s ,\n", out_c1, out_c2);
    }
    printf("odbc: select success\n");

    SQLFreeHandle(SQL_HANDLE_STMT, hstmt);

    SQLDisconnect(hdbc);
    SQLFreeHandle(SQL_HANDLE_DBC, hdbc);
    SQLFreeHandle(SQL_HANDLE_ENV, henv);
    return 0;
}
```

  2. 执行结果如下：



```
[dmdba@localhost odbctest]# ./odbc_bind
odbc: connect to server success!
insert with bind..
sql: insert into PRODUCTION.PRODUCT_CATEGORY(NAME) values(?)
para: 物理
odbc: insert into table with bind success!
odbc: select from table...
c1 = 23, c2 = 物理 ,
odbc: select success
```

### 4.3 大字段操作示例

  1. ODBC 接口大字段操作包括大字段的插入，查询等。示例程序 odbc_lob.c 如下：



```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sql.h>
#include <sqltypes.h>
#include <sqlext.h>

/* 检测返回代码是否为成功标志，当为成功标志返回 TRUE，否则返回 FALSE */
#define RC_SUCCESSFUL(rc) ((rc) == SQL_SUCCESS || (rc) == SQL_SUCCESS_WITH_INFO)
/* 检测返回代码是否为失败标志，当为失败标志返回 TRUE，否则返回 FALSE */
#define RC_NOTSUCCESSFUL(rc) (!(RC_SUCCESSFUL(rc)))

#define IN_FILE "./file/DM8_SQL.pdf"
#define OUT_FILE "./file/DM8_SQL1.pdf"
#define CHARS 80*1024 //一次读取和写入的字节数 80 KB

HENV henv;/* 环境句柄 */
HDBC hdbc;/* 连接句柄 */
HSTMT hstmt;/* 语句句柄 */
SQLRETURN sret; /* 返回代码 */

SQLSMALLINT errmsglen;
SQLINTEGER errnative;
UCHAR errmsg[255];
UCHAR errstate[5];

int main(void)
{
    FILE*   pfile = NULL;
    SQLCHAR tmpbuf[CHARS];
    SQLLEN  len = 0;
    SQLLEN  val_len = 0;

    SQLLEN      c1 =1; 
    SQLLEN      c2 = SQL_DATA_AT_EXEC;
    SQLLEN      c1_ind_ptr = 0;
    SQLLEN      c2_ind_ptr = SQL_DATA_AT_EXEC;
    PTR         c2_val_ptr;

    /* 申请句柄 */
    SQLAllocHandle(SQL_HANDLE_ENV, NULL, &henv);
    SQLSetEnvAttr(henv, SQL_ATTR_ODBC_VERSION, (SQLPOINTER)SQL_OV_ODBC3, SQL_IS_INTEGER);
    SQLAllocHandle(SQL_HANDLE_DBC, henv, &hdbc);

    sret = SQLConnect(hdbc, (SQLCHAR *)"DM", SQL_NTS, (SQLCHAR *)"SYSDBA", SQL_NTS, (SQLCHAR *)"******", SQL_NTS);
    if (RC_NOTSUCCESSFUL(sret)) {
        printf("odbc: fail to connect to server!\n");
        SQLFreeHandle(SQL_HANDLE_DBC, hdbc);
        SQLFreeHandle(SQL_HANDLE_ENV, henv);
        exit(0);
    }
    printf("odbc: connect to server success!\n");

    /* 申请一个语句句柄 */
    SQLAllocHandle(SQL_HANDLE_STMT, hdbc, &hstmt);

    //清空表，初始化测试环境
    sret = SQLExecDirect(hstmt, (SQLCHAR *)"drop table PRODUCTION.BIG_DATA", SQL_NTS);
    sret = SQLExecDirect(hstmt, (SQLCHAR *)"create table PRODUCTION.BIG_DATA(c1 int, c2 blob)", SQL_NTS);

    //读取文件，插入到 LOB 列
    pfile = fopen(IN_FILE, "rb");
    if (pfile == NULL)
    {
        printf("open %s fail\n", IN_FILE);
        return SQL_ERROR;
    }

    sret = SQLPrepare(hstmt, (SQLCHAR *)"insert into PRODUCTION.BIG_DATA(c1,c2) values(?,?)", SQL_NTS);
    sret = SQLBindParameter(hstmt, 1, SQL_PARAM_INPUT, SQL_C_SLONG,  SQL_INTEGER, sizeof(c1), 0, &c1, sizeof(c1), NULL);
    sret = SQLBindParameter(hstmt, 2, SQL_PARAM_INPUT, SQL_C_BINARY,  SQL_VARBINARY, sizeof(c2), 0, (void *)1, sizeof(c2), &c2_ind_ptr);
    sret = SQLExecute(hstmt);
    if (sret == SQL_NEED_DATA)
    {
        if (SQLParamData(hstmt, &c2_val_ptr) == SQL_NEED_DATA) /* 绑定数据 */
        {
            while (!feof(pfile))
            {
                len = fread(tmpbuf, sizeof(char), CHARS, pfile);
                if (len <= 0)
                {
                    return SQL_ERROR;
                }
                SQLPutData(hstmt, tmpbuf, len);
            }
        }
        SQLParamData(hstmt, &c2_val_ptr); /* 绑定数据 */
    }else if( sret == SQL_ERROR ) 
    {
        SQLGetDiagRec(SQL_HANDLE_STMT, hstmt, 1, errstate, &errnative, errmsg, sizeof(errmsg), &errmsglen);
        printf( "error:%s\n", errmsg );
    }

    printf("odbc: insesret data into col of lob success\n");
    fclose(pfile);

    //读取 LOB 列数据，写入文件
    pfile = fopen((const char *)OUT_FILE, "wb");
    if (pfile == NULL)
    {
        printf("open %s fail\n", OUT_FILE);
        return SQL_ERROR;
    }

    sret = SQLExecDirect(hstmt, (SQLCHAR *)"select c1, c2 from PRODUCTION.BIG_DATA", SQL_NTS);
    sret = SQLBindCol(hstmt, 1, SQL_C_SLONG, &c1, sizeof(c1), &c1_ind_ptr); 
    while(SQLFetch(hstmt) != SQL_NO_DATA)
    {
        while(1)
        {
            sret = SQLGetData(hstmt, 2, SQL_C_BINARY, tmpbuf, CHARS, &val_len);
            if ((sret) == SQL_SUCCESS || (sret) == SQL_SUCCESS_WITH_INFO)
            {
                len = val_len > CHARS ? CHARS : val_len;
                fwrite(tmpbuf, sizeof(char), len, pfile);
                continue;
            }
            break;
        }
    }

    fclose(pfile);
    printf("odbc: get data from col of lob success\n");

    SQLFreeHandle(SQL_HANDLE_STMT, hstmt);

    SQLDisconnect(hdbc);
    SQLFreeHandle(SQL_HANDLE_DBC, hdbc);
    SQLFreeHandle(SQL_HANDLE_ENV, henv);
    return 0;
}
```

  2. 执行结果如下：



```
[dmdba@localhost odbctest]# ./odbc_lob
odbc: connect to server success!
odbc: insesret data into col of lob success
odbc: get data from col of lob success
```

## 五、常见问题

1. 动态链接库文件不存在

【问题描述】：odbcinstall.ini 配置文件编写错误，无法找到文件，报错信息如下图：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221214105156K6IKCUGZFK3HA9RTCD)

【问题解决】：修改 odbcinstall.ini 配置文件。

2. ODBC 连接失败

【问题描述】：报错信息如下图：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202212141052359FKXDRGIZYPF0YQD5R)

【问题解决】：odbc.ini 文件中服务名配置错误，导致无法找到对应服务名，修改 odbc.ini 文件中服务名即可。

## 六、参考

  1. 示例代码下载：请下载附件示例代码 ([odbc_code.zip](https://eco.dameng.com/eco-file-server/file/eco/download/202212221157245TC87HQZ3KNUMI8ES5))。
  2. 以上文档内容参考过程中遇到任何问题，可到[达梦技术社区](https://eco.dameng.com/community/question/)提问交流。


