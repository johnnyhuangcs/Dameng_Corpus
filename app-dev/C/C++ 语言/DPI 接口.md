

# DPI 接口

## 一、前言

DM DPI 驱动程序是 DM 数据库的 C/C++ 类驱动程序，是访问 DM 数据库的最直接的途径。

DM DPI 的实现参考了 Microsoft ODBC 3.0 标准，函数功能以及调用过程与 ODBC 3.0 十分类似，命名统一采用 dpi 开头的小写英文字母方式，各个单词之间以下划线分（例：ODBC 函数 SQLAllocStmt 对应的 DPI 函数就是 dpi_alloc_stmt），用户可以参考《 Microsoft ODBC 3.0 程序员参考手册（第二卷）》之 API 参考部分的函数说明及调用方法。

## 二、开发环境准备

### 2.1 达梦数据库版本

|**名称**|**版本**|
|--|--|
|DM 数据库|DM 8.0 及以上版本|


### 2.2 达梦数据库安装

请参考[数据库环境准备](https://eco.dameng.com/document/dm/zh-cn/app-dev/#%E6%95%B0%E6%8D%AE%E5%BA%93%E7%8E%AF%E5%A2%83%E5%87%86%E5%A4%87)。

### 2.3 gcc 环境检查

```
gcc --version
```

执行结果如下：

```
[root@localhost dpitest]# gcc --version
gcc (GCC) 4.8.5 20150623 (Red Hat 4.8.5-36)
Copyright (C) 2015 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
```

### 2.4 项目目录结构

```
dpitest
├── dpi_bind.c             // 绑定变量示例
├── dpi_conn.c             // 数据库连接示例
├── dpi_dml.c              // 基本操作示例
├── dpi_lob.c              // 大字段操作示例
├── file                   // 大字段操作示例中导入和导出文件目录
|   ├── DM8_SQL.pdf        // 大字段操作示例中导入文件
├── makfile                // makefile 编译脚本
```

makefile 脚本如下：

```
CC=gcc

includepath=$(DM_HOME)/include
libpath=$(DM_HOME)/bin
vpath=./

CFLAGS=-I$(includepath) -DDM64 -Wall
LINKFLAGS=-L$(libpath) -ldmdpi -Wall

%.o:%.c
    $(CC) -g -c $(CFLAGS) $< -o $@

object_file1=dpi_conn.o
object_file2=dpi_dml.o
object_file3=dpi_bind.o
object_file4=dpi_lob.o

object_files=dpi_conn.o dpi_dml.o dpi_bind.o dpi_lob.o

final_objects=dpi_conn dpi_dml dpi_bind dpi_lob

all : $(final_objects)

.PHONY : all clean rebuild

dpi_conn : $(object_file1)
    $(CC) -o $@ $(object_file1) -g $(LINKFLAGS)
    @echo make ok.

dpi_dml : $(object_file2)
    $(CC) -o $@ $(object_file2) -g $(LINKFLAGS)
    @echo make ok.

dpi_bind : $(object_file3)
    $(CC) -o $@ $(object_file3) -g $(LINKFLAGS)
    @echo make ok.

dpi_lob : $(object_file4)
    $(CC) -o $@ $(object_file4) -g $(LINKFLAGS)
    @echo make ok.

clean :
    @rm -rf $(object_files)
    @rm -rf $(final_objects)

rebuild : clean all
```

## 三、数据库连接

DPI 接口驱动程序为 libdmdpi.so，存放目录位于 $DM_HOME/bin。

连接 API 说明如下：

```
DPIRETURN
dpi_login(
 dhcon dpi_hcon,
 sdbyte* svr,
 sdbyte* user,
 sdbyte* pwd,
);
```

  * dpi_hcon 输入参数，连接句柄。
  * svr 输入参数，数据库服务器所对应的地址。示例为：IP : PORT。
  * user 输入参数，登录数据库服务器的登录名。
  * pwd 输入参数，登录数据库服务器的口令。



### 3.1 数据库连接示例

  1. DPI 接口登录、登出示例程序 dpi_conn.c 如下：



```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#include "DPI.h"
#include "DPIext.h"
#include "DPItypes.h"

#define DM_SVR "192.168.117.223:5236"
#define DM_USER "SYSDBA"
#define DM_PWD "*****"

dhenv henv; /* 环境句柄 */
dhcon hcon; /* 连接句柄 */
dhstmt hstmt; /* 语句句柄 */
dhdesc hdesc; /* 描述符句柄 */
DPIRETURN rt; /* 函数返回值 */

/******************************************************
Notes：
获取错误信息
*******************************************************/
void dpi_err_msg_print(sdint2 hndl_type, dhandle hndl)
{
    sdint4 err_code;
    sdint2 msg_len;
    sdbyte err_msg[SDBYTE_MAX];

    /* 获取错误信息集合 */
    dpi_get_diag_rec(hndl_type, hndl, 1, &err_code, err_msg, sizeof(err_msg), &msg_len);
    printf("err_msg = %s, err_code = %d\n", err_msg, err_code);
}

/*
入口函数
*/
int main(int argc, char *argv[])
{

    //连接数据库
    /* 申请环境句柄 */
    rt = dpi_alloc_env(&henv);

    /* 申请连接句柄 */
    rt = dpi_alloc_con(henv, &hcon);

    /* 连接数据库服务器 */
    rt = dpi_login(hcon, (sdbyte *)DM_SVR, (sdbyte *)DM_USER, (sdbyte *)DM_PWD);
    if(!DSQL_SUCCEEDED(rt))
    {
        dpi_err_msg_print(DSQL_HANDLE_DBC, hcon);
        return rt;
    }
    printf("dpi: connect to server success!\n");


    //断开数据库连接
    /* 断开连接 */
    rt = dpi_logout(hcon);
    if(!DSQL_SUCCEEDED(rt))
    {
        dpi_err_msg_print(DSQL_HANDLE_DBC, hcon);
    return rt;
    }
    printf( "dpi: disconnect from server success!\n" );

    /* 释放连接句柄和环境句柄 */
    rt = dpi_free_con(hcon);
    rt = dpi_free_env(henv);

    return rt;
}
```

  2. 执行结果如下：



```
[root@RS1821 dpitest]# ./dpi_conn
dpi: connect to server success!
dpi: disconnect from server success!
```

## 四、开发示例

### 4.1 基础操作示例

  1. DPI 接口增、删、改、查四个基本操作，示例程序 dpi_dml.c 如下：



```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#include "DPI.h"
#include "DPIext.h"
#include "DPItypes.h"

#define DM_SVR "192.168.117.223:5236"
#define DM_USER "SYSDBA"
#define DM_PWD "*****"

dhenv henv; /* 环境句柄 */
dhcon hcon; /* 连接句柄 */
dhstmt hstmt; /* 语句句柄 */
dhdesc hdesc; /* 描述符句柄 */
DPIRETURN rt; /* 函数返回值 */

/******************************************************
Notes：
获取错误信息
*******************************************************/
void dpi_err_msg_print(sdint2 hndl_type, dhandle hndl)
{
    sdint4 err_code;
    sdint2 msg_len;
    sdbyte err_msg[SDBYTE_MAX];

    /* 获取错误信息集合 */
    dpi_get_diag_rec(hndl_type, hndl, 1, &err_code, err_msg, sizeof(err_msg), &msg_len);
    printf("err_msg = %s, err_code = %d\n", err_msg, err_code);
}

/*
入口函数
*/
int main(int argc, char *argv[])
{
    sdint4  out_c1 = 0;
    sdbyte  out_c2[20]= { 0 };
    slength out_c1_ind = 0;
    slength out_c2_ind = 0;
    ulength row_num;

    //连接数据库
    rt = dpi_alloc_env(&henv);
    rt = dpi_alloc_con(henv, &hcon);
    rt = dpi_login(hcon, (sdbyte *)DM_SVR, (sdbyte *)DM_USER, (sdbyte *)DM_PWD);
    if(!DSQL_SUCCEEDED(rt))
    {
        dpi_err_msg_print(DSQL_HANDLE_DBC, hcon);
        return rt;
    }

    rt = dpi_alloc_stmt(hcon, &hstmt);

    //清空表，初始化测试环境
    rt = dpi_exec_direct(hstmt, (sdbyte*) "delete from PRODUCTION.PRODUCT_CATEGORY");

    //插入数据
    rt = dpi_exec_direct(hstmt, (sdbyte*) "insert into PRODUCTION.PRODUCT_CATEGORY(NAME) values('语文'), ('数学'), ('英语'), ('体育') ");
    if(!DSQL_SUCCEEDED(rt))
    {
        dpi_err_msg_print(DSQL_HANDLE_STMT, hstmt);
        return rt;
    }
    printf("dpi: insert success\n");

    //删除数据
    rt = dpi_exec_direct(hstmt, (sdbyte*) "delete from PRODUCTION.PRODUCT_CATEGORY where name='数学' ");
    if(!DSQL_SUCCEEDED(rt))
    {
    dpi_err_msg_print(DSQL_HANDLE_STMT, hstmt);
        return rt;
    }
    printf("dpi: delete success\n");

    //更新数据
    rt = dpi_exec_direct(hstmt, (sdbyte*) "update PRODUCTION.PRODUCT_CATEGORY set name = '英语-新课标' where name='英语' ");
    if(!DSQL_SUCCEEDED(rt))
    {
    dpi_err_msg_print(DSQL_HANDLE_STMT, hstmt);
    return rt;
    }
    printf("dpi: update success\n");

    //查询数据
    dpi_exec_direct(hstmt, (sdbyte*) "select * from PRODUCTION.PRODUCT_CATEGORY");
    dpi_bind_col(hstmt, 1, DSQL_C_SLONG, &out_c1, sizeof(out_c1), &out_c1_ind);
    dpi_bind_col(hstmt, 2, DSQL_C_NCHAR, &out_c2, sizeof(out_c2), &out_c2_ind);

    printf("dpi: select from table...\n");
    while(dpi_fetch(hstmt, &row_num) != DSQL_NO_DATA)
    {
        printf("c1 = %d, c2 = %s ,\n", out_c1, out_c2);
    }
    printf("dpi: select success\n");

    //断开数据库连接
    rt = dpi_logout(hcon);
    if(!DSQL_SUCCEEDED(rt))
    {
        dpi_err_msg_print(DSQL_HANDLE_DBC, hcon);
        return rt;
    }

    rt = dpi_free_con(hcon);
    rt = dpi_free_env(henv);

    return rt;
}
```

  2. 执行结果如下：



```
[root@RS1821 dpitest]# ./dpi_dml
dpi: insert success
dpi: delete success
dpi: update success
dpi: select from table...
c1 = 59, c2 = 语文 ,
c1 = 61, c2 = 英语-新课标 ,
c1 = 62, c2 = 体育 ,
dpi: select success
[root@RS1821 dpitest]#
```

### 4.2 绑定变量示例

  1. DPI 接口绑定变量示例程序 dpi_bind.c 如下：



```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#include "DPI.h"
#include "DPIext.h"
#include "DPItypes.h"

#define DM_SVR "192.168.117.223:5236"
#define DM_USER "SYSDBA"
#define DM_PWD "*****"

dhenv henv; /* 环境句柄 */
dhcon hcon; /* 连接句柄 */
dhstmt hstmt; /* 语句句柄 */
dhdesc hdesc; /* 描述符句柄 */
DPIRETURN rt; /* 函数返回值 */

/******************************************************
Notes：
获取错误信息
*******************************************************/
void dpi_err_msg_print(sdint2 hndl_type, dhandle hndl)
{
    sdint4 err_code;
    sdint2 msg_len;
    sdbyte err_msg[SDBYTE_MAX];

    /* 获取错误信息集合 */
    dpi_get_diag_rec(hndl_type, hndl, 1, &err_code, err_msg, sizeof(err_msg), &msg_len);
    printf("err_msg = %s, err_code = %d\n", err_msg, err_code);
}

/*
入口函数
*/
int main(int argc, char *argv[])
{
    sdbyte  sql[]="insert into PRODUCTION.PRODUCT_CATEGORY(NAME) values(?)";
    sdbyte  in_c1[20] = { 0 };
    slength in_c1_ind_ptr;

    memcpy(in_c1, "物理", 8);
    in_c1_ind_ptr = 8; 

    sdint4  out_c1 = 0;
    sdbyte  out_c2[20]= { 0 };
    slength out_c1_ind = 0;
    slength out_c2_ind = 0;
    ulength row_num;

    //连接数据库
    rt = dpi_alloc_env(&henv);
    rt = dpi_alloc_con(henv, &hcon);
    rt = dpi_login(hcon, (sdbyte *)DM_SVR, (sdbyte *)DM_USER, (sdbyte *)DM_PWD);
    if(!DSQL_SUCCEEDED(rt))
    {
        dpi_err_msg_print(DSQL_HANDLE_DBC, hcon);
        return rt;
    }

    rt = dpi_alloc_stmt(hcon, &hstmt);

    //清空表，初始化测试环境
    dpi_exec_direct(hstmt, (sdbyte*) "delete from PRODUCTION.PRODUCT_CATEGORY");

    //绑定参数方式插入数据
    printf( "insert with bind..\nsql: %s\npara: %s\n", (char*)sql, (char*)in_c1);
    rt = dpi_prepare(hstmt, sql);
    rt = dpi_bind_param(hstmt, 1, DSQL_PARAM_INPUT, DSQL_C_NCHAR, DSQL_VARCHAR, sizeof(in_c1), 0, in_c1, sizeof(in_c1), &in_c1_ind_ptr);
    rt = dpi_exec(hstmt);
    if(!DSQL_SUCCEEDED(rt))
    {
        dpi_err_msg_print(DSQL_HANDLE_DBC, hcon);
        return rt;
    }
    printf( "dpi: insert into table with bind success!\n" );

    //查询数据
    dpi_exec_direct(hstmt, (sdbyte*) "select * from PRODUCTION.PRODUCT_CATEGORY");
    dpi_bind_col(hstmt, 1, DSQL_C_SLONG, &out_c1, sizeof(out_c1), &out_c1_ind);
    dpi_bind_col(hstmt, 2, DSQL_C_NCHAR, &out_c2, sizeof(out_c2), &out_c2_ind);

    printf("\ndpi: select from table......\n");
    while(dpi_fetch(hstmt, &row_num) != DSQL_NO_DATA)
    {
        printf("c1 = %d, c2 = %s ,\n", out_c1, out_c2);
    }
    printf("dpi: select from table success\n\n");

    //断开数据库连接
    rt = dpi_logout(hcon);
    if(!DSQL_SUCCEEDED(rt))
    {
        dpi_err_msg_print(DSQL_HANDLE_DBC, hcon);
        return rt;
    }

    rt = dpi_free_con(hcon);
    rt = dpi_free_env(henv);

    return rt;
}
```

  2. 执行结果如下：



```
[root@RS1821 dpitest]# ./dpi_bind
insert with bind..
sql: insert into PRODUCTION.PRODUCT_CATEGORY(NAME) values(?)
para: 物理
dpi: insert into table with bind success!

dpi: select from table......
c1 = 63, c2 = 物理 ,
dpi: select from table success

[root@RS1821 dpitest]#
```

### 4.3 大字段操作示例

  1. DPI 接口大字段操作包括大字段的插入、查询等。示例程序 dpi_lob.c 如下：



```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#include "DPI.h"
#include "DPIext.h"
#include "DPItypes.h"

#define DM_SVR "192.168.117.223:5236"
#define DM_USER "SYSDBA"
#define DM_PWD "*****"
#define IN_FILE "./file/DM8_SQL.pdf"
#define OUT_FILE "./file/DM8_SQL1.pdf"
#define CHARS 80*1024 //一次读取和写入的字节数 80 KB

dhenv henv; /* 环境句柄 */
dhcon hcon; /* 连接句柄 */
dhstmt hstmt; /* 语句句柄 */
dhdesc hdesc; /* 描述符句柄 */
DPIRETURN rt; /* 函数返回值 */

/******************************************************
Notes：
获取错误信息
*******************************************************/
void dpi_err_msg_print(sdint2 hndl_type, dhandle hndl)
{
    sdint4 err_code;
    sdint2 msg_len;
    sdbyte err_msg[SDBYTE_MAX];

    /* 获取错误信息集合 */
    dpi_get_diag_rec(hndl_type, hndl, 1, &err_code, err_msg, sizeof(err_msg), &msg_len);
    printf("err_msg = %s, err_code = %d\n", err_msg, err_code);
}

/*
入口函数
*/
int main(int argc, char *argv[])
{
    FILE*  pfile = NULL;
    sdbyte  tmpbuf[CHARS];
    slength len = 0;
    ulength row_num = 0;
    slength val_len = 0;

    sdint4     c1 =1; 
    sdint4     c2 = DSQL_DATA_AT_EXEC;
    slength     c1_ind_ptr = 0;
    slength     c2_ind_ptr = DSQL_DATA_AT_EXEC;
    dpointer     c2_val_ptr;

    //连接数据库
    rt = dpi_alloc_env(&henv);
    rt = dpi_alloc_con(henv, &hcon);
    rt = dpi_login(hcon, (sdbyte *)DM_SVR, (sdbyte *)DM_USER, (sdbyte *)DM_PWD);
    if(!DSQL_SUCCEEDED(rt))
    {
        dpi_err_msg_print(DSQL_HANDLE_DBC, hcon);
        return rt;
    }

    rt = dpi_alloc_stmt(hcon, &hstmt);

    //清空表，初始化测试环境
    rt = dpi_exec_direct(hstmt, (sdbyte *)"drop table PRODUCTION.BIG_DATA");
    rt = dpi_exec_direct(hstmt, (sdbyte *)"create table PRODUCTION.BIG_DATA(c1 int, c2 blob)");

    //读取文件数据，插入LOB列
    pfile = fopen(IN_FILE, "rb");
    if (pfile == NULL)
    {
        printf("open %s fail\n", IN_FILE);
        return DSQL_ERROR;
    }

    rt = dpi_prepare(hstmt, (sdbyte *)"insert into PRODUCTION.BIG_DATA(c1,c2) values(?,?)");
    rt = dpi_bind_param(hstmt, 1, DSQL_PARAM_INPUT, DSQL_C_SLONG,  DSQL_INT, sizeof(c1), 0, &c1, sizeof(c1), NULL);
    rt = dpi_bind_param(hstmt, 2, DSQL_PARAM_INPUT, DSQL_C_BINARY,  DSQL_BLOB, sizeof(c2), 0, &c2, sizeof(c2), &c2_ind_ptr);
    if (dpi_exec(hstmt) == DSQL_NEED_DATA)
    {
        if (dpi_param_data(hstmt, &c2_val_ptr) == DSQL_NEED_DATA) /* 绑定数据 */
        {
            while (!feof(pfile))
            {
                len = fread(tmpbuf, sizeof(char), CHARS, pfile);
                if (len <= 0)
                {
                    return DSQL_ERROR;
                }
                dpi_put_data(hstmt, tmpbuf, len);
            }
        }
        dpi_param_data(hstmt, &c2_val_ptr); /* 绑定数据 */
    }
    printf("dpi: insert data into col of lob success\n");
    fclose(pfile);

    //读取 LOB 列数据，写入文件
    pfile = fopen((const char *)OUT_FILE, "wb");
    if (pfile == NULL)
    {
        printf("open %s fail\n", OUT_FILE);
        return DSQL_ERROR;
    }

    dpi_exec_direct(hstmt, (sdbyte *)"select c1, c2 from PRODUCTION.BIG_DATA");
    dpi_bind_col(hstmt, 1, DSQL_C_SLONG, &c1, sizeof(c1), &c1_ind_ptr);
    while(dpi_fetch(hstmt, &row_num) != DSQL_NO_DATA)
    {
        while(DSQL_SUCCEEDED(dpi_get_data(hstmt, 2, DSQL_C_BINARY, tmpbuf, CHARS, &val_len)))
        {
            len = val_len > CHARS ? CHARS : val_len;
            fwrite(tmpbuf, sizeof(char), len, pfile);
        }
    }

    fclose(pfile);
    printf("dpi: get data from col of lob success\n");

    //断开数据库连接
    rt = dpi_logout(hcon);
    if(!DSQL_SUCCEEDED(rt))
    {
        dpi_err_msg_print(DSQL_HANDLE_DBC, hcon);
        return rt;
    }

    rt = dpi_free_con(hcon);
    rt = dpi_free_env(henv);

    return rt;
}
```

  2. 执行结果如下：



```
[root@RS1821 dpitest]# ./dpi_lob
dpi: insert data into col of lob success
dpi: get data from col of lob success
[root@RS1821 dpitest]#
```

## 五、参考

  1. 示例代码下载：[dpi_code.zip](https://eco.dameng.com/eco-file-server/file/eco/download/202212221159430LFOBIBK7063BPKP64)。
  2. 以上文档内容参考过程中遇到任何问题，可到 [达梦技术社区](https://eco.dameng.com/community/question/) 提问交流。


