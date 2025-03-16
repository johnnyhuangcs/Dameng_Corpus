

# msgparse编程指南

本章介绍如何使用 msgparse 接口。它是达梦提供的消息解析 C 接口，供应用程序直接调用。接口依赖的静态库 dmmsgparse.lib 和头文件 dmmsgparse_pub.h 位于 DM 安装目录\include 目录下。

## 13.1 接口介绍

静态库 dmmsgparse.lib 内含 9 个函数。下面分别进行介绍。

### 13.1.1 获取请求数据包类型

函数原型

```
int ReqType(
	unsigned char* buf, 
	int buflen, 
	int* type
);
```

功能说明

对消息解析后返回消息类型。

参数说明

buf：输入参数，通讯包首地址。

buflen: 输入参数，数据长度。

type: 输出参数，以 *type 返回数据包类型。type 返回内容见下表 13.1。

表13.1 type返回内容

|**输出消息报文的宏定义**|**输出消息报文的含义**|**类型值**|
|----------|----------|----------|
|DM_MSG_LOGIN|登录消息|101|
|DM_MSG_LOGOUT|登出消息|102|
|DM_MSG_STMT_ALLOCATE|分配语句句柄|103|
|DM_MSG_STMT_FREE|语句句柄释放|104|
|DM_MSG_PREPARE|准备语句|105|
|DM_MSG_FETCH|获取结果集|106|
|DM_MSG_COMMIT|提交|107|
|DM_MSG_ROLLBACK|回滚|108|
|DM_MSG_CANCLE|取消|109|
|DM_MSG_EXECUTE2|绑定参数|110|
|DM_MSG_PUT_DATA|写入大字段数据|111|
|DM_MSG_GET_DATA|获取数据请求|112|
|DM_MSG_CREATE_BLOB|创建大字段|113|
|DM_MSG_CLOSE_STMT|关闭语句句柄|114|
|DM_MSG_TIME_OUT|超时|115|
|DM_MSG_CURSOR_PREPARE|准备游标|116|
|DM_MSG_CURSOR_EXECUTE|执行游标|117|
|DM_MSG_EXPLAIN|查询计划|118|
|DM_MSG_GET_DATA_ARR|获取一组大字段的完整数据|119|
|DM_MSG_GET_ROW_NUM|获取行数|120|
|DM_MSG_GET_LOB_LEN|获取大字段长度|121|
|DM_MSG_GET_LOB_DATA|获取大字段数据|122|
|DM_MSG_MORE_RESULT|获取更多结果集|123|
|DM_MSG_EXEC_RETURN_AUTO_VALUE|获取 return into 值|124|
|DM_MSG_RESET_SESS|重置会话|125|
|DM_MSG_PRE_EXEC|DM_MSG_PRE_EXEC 是 DM_MSG_EXECUTE 中 PUT_DATA 时生成计划所需的命令字|126|
|DM_MSG_EXEC_DIRECT|常量参数化执行 sql|127|
|DM_MSG_STARTUP|启动|128|


返回值

0：成功。

\<0：错误。

### 13.1.2 检测响应数据是否为登录成功响应

函数原型

```
int IsLogined(
	unsigned char* buf, 
	int buflen
);
```

功能说明

判断消息是否为登录成功响应。

参数说明

buf：通讯包首地址。

buflen：数据长度。

返回值

1：是。

0：否，表示非登录成功响应，包括不能识别或错误的数据。

### 13.1.3 检测请求数据是否含有完整包长

函数原型

```
int HaveAFullReqPack(
	unsigned char* buf, int buflen, int* pPackLen);
```

功能说明

判断消息是否有完整包长。pPackLen 不为 NULL 时，指针返回消息的总长度=数据包的消息头 + 数据包的消息体的长度。为 NULL 则不返回。

参数说明

buf：输入参数，通讯包首地址。

buflen： 输入参数，数据长度。

pPackLen: 输出参数，包含完整的数据包时，以*pPackLen 返回第一个完整数据包的长度，长度为包含协议头的总长度

返回值

0：是。

\<0：否，或发生错误。

### 13.1.4 检测请求数据是否为新 SQL 指令

函数原型

```
int IsNewSQL(
	unsigned char* buf, 
	int buflen
);
```

功能说明

所谓新 SQL 指令，通常是指请求数据为一次新的 SQL 请求。

相对的，比如一个 SQL 查询，结果集数量比较多，从而从通讯看看，存在多次收发数据，此时不应认为是新 SQL；

消息类型是 CMD_PREPARE、CMD_EXEC_DIRECT、CMD_EXECUTE2（一次 prepare, 绑定不同参数 execute）都是第一次 SQL 请求，返回 0。

参数说明

buf：通讯包首地址。

buflen：数据长度。

返回值

0：是。

\<0：否，或发生错误。

### 13.1.5 构建错误应答报文

函数原型

```
int CrtErrResp(
	int errno, 
	char* errmsg, 
	unsigned char* respbuf, 
	int* respbuflen
);
```

功能说明

此方法用于拦截异常连接，构建满足达梦标准的响应包，以错误形式通知到异常连接的客户端。

由于不能预先获知构建报文所需的实际大小，拟当输入参数 respbuf 为 NULL 时，方法计算出所需空间，通过 * respbuflen 返回；

调用者申请空间后，以非 NULL 的 respbuf 再次调用此方法，同时用 * respbuflen 给出空间长度，如空间长度足够时，真正生成报文。

消息体填写英文错误描述。不设置消息体中的 crc。（自行发送时设置）。

参数说明

errno：输入参数，错误码，预定义几种错误码（需补充已知常用的错误码），用于映射为达梦标准错误码。已知常用的错误码包括成功、动态库内部错误码、异常连接错误码。部分错误码见下表 13.2。

errmsg：输入参数，错误信息。以 '\0' 为结束符的 C 语言形式的 ANSI 编码的错误消息描述；如果允许，则用于填充错误内容。如果不标准，则自动更正成标准错误码后再填充错误内容。

respbuflen：输入时为 respbuf 的有效空间，输出时为构建响应数据的真实长度。

表13.2 部分错误码

|**错误码**|**错误描述**|**宏定义**|
|----|----|----|
|0|成功|DM_EC_SUCCESS|
|-1|消息长度不够|DM_INVALID_LEN|
|-2|数据无法识别|DM_WR_MSG|
|-3|非法消息|DM_INVALID_MSG_TYPE|
|-5|缓冲区长度过小|DM_OUT_OF_MSG_BUFF|
|-11|客户端版本错误|DM_EC_INVALID_LEN|
|-12|UKEY 认证失败|DM_EC_UKEY_AUTH_MISMATCH|
|-13|证书验证失败|DM_EC_CONNECT_CAN_NOT_ESTABLISHED|
|-14|无效的用户名|DM_EC_RN_INVALID_USER_NAME|
|-15|解密失败|DM_EC_ENC_DECRYPT_FAIL|
|-16|口令失败|DM_EC_INVALID_PASSWORD|
|-17|请求执行过程中连接断开|DM_EC_RECV_OOB|
|-18|连接失败|DM_EC_CONNECT_LOST|


返回值

0：成功。

\<0：错误。

### 13.1.6 提取请求数据中的 SQL 和参数

函数原型

```
int ParseReq(
	unsigned char* buf, 
	int buflen, char* sql, 
	int* sqllen, 
	PARAMINFO* pParams, 
	int* iParam
);
```

功能说明

提取请求数据中的 SQL 和参数。

有以下几种情况：

1）sql 为 NULL 的情况下：

消息是 CMD_PREPARE 类型的只返回 SQL 长度*sqllen， CMD_EXEC_DIRECT 类型的返回 SQL 长度*sqllen。

2）pParams 为 NULL 的情况下：

消息是 CMD_EXECUTE2 类型的只返回参数个数*iParam。CMD_EXEC_DIRECT 类型的返回参数个数*iParam。

3）sql 不为 NULL 的情况下：

消息是 CMD_PREPARE 则只返回 sql 语句串。CMD_EXEC_DIRECT 类型返回 sql 语句串。pParams 不为 NULL, iParam 也不返回。

4）pParams 不为 NULL 的情况下：sql 不为 NULL，sqllen 也不返回。

消息是 CMD_PRE_EXEC 只填参数 PARAMINFO 部分信息（名称、序号、类型）、参数长度 0 内容不填。

消息是 CMD_PUT_DATA 因为只有 clob 控制头信息，包括行外地址。只填参数 PARAMINFO 部分信息（类型为大字段，序号，名称不填），参数长度为实际字节个数、内容不填。CMD_PUT_DATA 之后的 CMD_EXECUTE2 消息，只填参数 PARAMINFO 部分信息（类型为大字段，序号）， 其他信息-1，参数长度-1 和内容不填。

消息是 CMD_EXECUTE2 类型则只填参数 PARAMINFO 信息（名称、序号、类型）、 参数长度、 参数内容。(消息是 CMD_EXEC_DIRECT,有可能有参数，参数还有可能是常量参数化信息。CLT_CONST_TO_PARAM 开关打开，非绑定列、绑定参数的 sql 都进 exec_direct。不带常量就没有参数。）参数长度-2 表示 NULL。参数类型为-2 表示输出参数。消息是 CMD_EXEC_DIRECT 类型也填上述参数信息。复合类型（ARRAY/ CLASS /RECORD/SARRAY）/游标类型 CURSOR/罕见类型 的参数数据暂不获取。参数长度是实际长度，内容不填。多行批量执行的只取第一行参数内容。

参数描述是从序号 1 开始的。参数名用英文返回。参数类型支持：CHAR/VARCHAR2/ VARCHAR/BIT/TINYINT/SMALLINT/INT/INT64/FLOAT/DOUBLE/BINARY/ VARBINARY/BLOB/TEXT/INTERVAL_YM/INTERVAL_DT/ROWID/XDEC/DATE/TIME/DATETIME/TIME_TZ/DATETIME_TZ/复合类型/游标类型/罕见类型。不支持的参数类型返回-7。下表为支持的参数类型。

表 13.3 支持的参数类型

|**类型代号**|**DM 类型**|**说明**|
|-----|-----|-----|
|1|DM_DATATYPE_CHAR|字符串类型。包括 CHAR、VARCHAR2、VARCHAR|
|2|DM_DATATYPE_BIT|整型 1 字节。包括 BIT、TINYINT|
|3|DM_DATATYPE_SMALLINT|整型 2 字节|
|4|DM_DATATYPE_INT|整型|
|5|DM_DATATYPE_INT64|长整型|
|6|DM_DATATYPE_FLOAT|单浮点类型|
|7|DM_DATATYPE_DOUBLE|双精度浮点类型|
|8|DM_DATATYPE_BINARY|十六进制类型。包括 BINARY、VARBINARY|
|9|DM_DATATYPE_LOB|大字段类型。包括 BLOB、TEXT|
|10|DM_DATATYPE_IVYM|间隔年月类型|
|11|DM_DATATYPE_IVDT|间隔日时类型|
|12|DM_DATATYPE_ROWID|ROWID 类型|
|13|DM_DATATYPE_DEC|DEC 类型|
|14|DM_DATATYPE_DATE|日期|
|15|DM_DATATYPE_TIME|时间|
|16|DM_DATATYPE_DATETIME|日期时间|
|17|DM_DATATYPE_TIMETZ|时间带时区|
|18|DM_DATATYPE_DATETIME_TZ|日期时间带时区|
|19|DM_DATATYPE_COMP|复合类型。包括 SARRAY、ARRAY、CLASS、RECORD|
|20|DM_DATATYPE_CURSOR|游标类型|
|21|DM_DATATYPE_OTHER|罕见类型。包括 PLTYPE_VOID、PLTYPE_OBJECT、  DATA_RECORD|
|22|DM_DATATYPE_DATETIME2|datetime2 类型|
|23|DM_DATATYPE_DATETIME2_TZ|DATETIME2  WITH TIME ZONE|


参数说明

buf：输入参数，通讯包首地址

buflen：输入参数，数据长度

sql：输出参数，以*sql 返回解析后的 SQL 语句；当 sql 为 NULL 时，不真正解析，而是通过*sqllen 返回所需空间长度，以及用*iParam 返回可能的参数个数

sqllen： 输入时为 sql 的有效空间，输出时为 sql 数据的真实长度。

pParams：输出参数，以 pParams 为首地址，返回 iParam 个参数信息。PARAMINFO 的定义为：

```
typedef struct { 
	int serno;   // 参数序号
   	int type;   // 用整数表示的参数类型，如：0,整数; 1,浮点数; 2:字符串; 3:二进制数据 等等
   	char name[256]; // 参数名称
   	void* val;   // 参数内容，放在*val
   	int vallen;    // 参数内容有效长度.
} PARAMINFO; 
```

iParam：输入时为 pParams 的有效个数，输出时为 pParams 的实际个数。

返回值

0：成功。

\<0：错误。

### 13.1.7 展示可解析的消息格式版本

函数原型

```
Int GetMsgVer()
```

功能说明

获取消息格式的当前版本号

参数说明

无

返回值

可解析的消息格式的当前版本号（固定为当前最新值 9），宏定义为 DM_COMM_VER 9。

### 13.1.8 获取数据库登录请求的附加用户信息

函数原型

```
int LoginUser(
	unsigned char* buf, 
	int buflen, 
	msg_startup_info_t* info, 
	msg_info_t*     msg_info  
);
```

功能说明

获取登录请求消息中的附加用户信息。

参数说明

buf：输入参数，通讯包首地址。

buflen：输入参数，数据长度。

info：startup 输入参数，消息包含信息，STARTUP 消息通过 StartupInfo 接口解析获取。

msg_info：输出参数，登录消息中的附加用户属性。

返回值

-1：长度不够。

-2：数据无法识别。

-3：非法消息。不在 DM_MSG_LOGIN~ DM_MSG_STARTUP 范围。

0：成功。

### 13.1.9 获取 STARTUP 启动的附加信息

函数原型

```
int StartupInfo(
  byte*        buf,  
  lint        buflen,   
  msg_startup_info_t* info  
);
```

功能说明

获取 STARTUP 消息中的 UKEY 认证标记。

参数说明

buf：输入参数，通讯包首地址。

buflen：输入参数，数据长度。

info：输出参数，startup 消息包含信息。

返回值

-1：长度不够。

-2：数据无法识别。

0：成功。

### 13.1.10 提取请求消息中的信息接口

函数原型

```
lint ParseReqInfo(
    schar*              buf,
    lint                 buflen,
    req_info_t*        req_info
);
```

功能说明

解析请求消息中的信息，并填充到输出参数 req_info 中。

参数说明

buf：输入参数，通讯包首地址。

buflen：输入参数，通讯包数据长度。

req_info：输出参数，解析后的请求消息中的信息。结构体 req_info_t 用于提取请求消息，具体定义如下：

```
typedef struct req_info_struct req_info_t;
struct req_info_struct
{
	lint  cmd_type;  //对应ReqType接口的返回值，表示请求数据包类型
	magic_declare    //用于校验结构体是否为req_info
};
```

返回值

-1：长度不够。

-2：数据无法识别。

-3：请求消息类型不合法。

0：成功。

### 13.1.11 提取响应消息中的信息接口

函数原型

```
lint ParseRes(
	unsigned char*    buf, 
	lint 			  buflen,
	req_info_t*       req_info,
	res_info_t* 	  res_info
);
```

功能说明

解析响应消息中的信息，并填充到输出参数 res_info 中。

使用该接口有以下限制与说明：

  1. 提取响应消息中的内容，必须先调用 ParseReqInfo 获取对应请求的消息填充 req_info；然后将 req_info 作为输入参数给 ParseRes 解析对应的响应消息获取 res_info；若 req_info 与解析的响应消息不对应，则无法保证 res_info 正确性；
  2. 本驱动仅能用于解析 DM 服务器与接口间明文通信消息。服务器与集群间通信格式不同，无法正确识别；
  3. 不同会话的语句句柄 ID 可能相同，因此只有语句句柄 ID 时无法区分是否为当前会话的增删改查，需要由用户自己确认拦截消息对应的会话，否则获取的影响行数数据可能错乱；
  4. 增、删、改响应中只有 total_rows 有意义，表示操作影响的总行数；affect_rows 无意义，恒为 0；
  5. 查询和获取响应中 total_rows 表示查询的总行数，尚未获取时填充-1；affect_rows 表示当前响应消息中填充的数据行数；分批获取时，滚动游标的情况下，affect_rows 的累加值可能与 total_rows 不同；
  6. ParseRes 不支持分析语句块，由于语句块中的增、删、改无法统计总行数 total_rows。



参数说明

buf：输入参数，通讯包首地址。

buflen：输入参数，通讯包数据长度。

req_info：输入参数，请求消息中的信息，通过 ParseReqInfo 接口获得。

res_info：输出参数，解析后的响应消息中的信息。结构体 res_info_t 用于提取响应消息，具体定义如下：

```
typedef struct res_info_struct res_info_t; 
struct res_info_struct
{
	int type;			    //表示响应消息类型
	ulint	handle;         //表示执行的语句句柄ID号
	lint64  affect_rows;    //表示当前响应消息中的填充部分的数据行数
	lint64  total_rows;     //表示当前响应消息中对应的原始请求消息中SQL的影响总行数
	magic_declare            //用于校验结构体是否为res_info
};
```

对该结构体的属性进行说明：

type：表示响应消息类型。支持的响应类型如下：



handle：表示执行的语句句柄 ID 号，仅对 type 为 1001~1005 的响应消息有效，type 为 1006(其他响应消息类型)时返回-1（无符号整型对应 4294967295）。

affect_rows：表示当前响应消息中的填充的数据行数。只对 type 为 1004(SELECT)和 1005(FETCH)才有效，表示当前查询的响应消息中的填充部分影响的数据行数；当 type 为 1001(INSERT)、1002(DELETE)或 1003(UPDATE)时没有意义，恒返回 0；当 type 为 1006(其他响应消息类型)时，返回-1。

total_rows：表示当前响应消息中对应的原始请求消息中 SQL(增、删，改、查)影响的总行数。type 为 1004(SELECT)和 1005(FETCH)时，若响应消息中没有填充影响的总行数时返回-1，否则返回真实的影响的总行数；type 为 1001(INSERT)、1002(DELETE)或 1003(UPDATE)时，响应消息中填充了影响的总行数时，返回真实的影响的总行数；type 为 1006(其他响应消息类型)时，返回-1。

magic_declare：用于校验结构体是否为 res_info。

返回值

-1：长度不够。

-2：数据无法识别。

0：成功。

## 13.2 基本示例

开启服务器和客户端/接口之间通信的抓包工具不作介绍，这里只介绍如何使用 msgparse 接口。

不同的操作系统，用法略有不同。下面分别进行介绍。

### 13.2.1 Windows 环境示例

下面以 Windows 环境为例进行说明。

第一步 配置头文件。

需要用到头文件 msgparse_pub.h。头文件位于 include\msgparse_pub.h。将头文件引入工程中。

第二步 配置静态库。

需要用到的静态库为 dmmsgparse.lib。静态库位于 drivers\msgparse 文件夹下。

配置静态库：VS2010 工程中，右键项目名称 → 点击属性 → 选择链接器 → 输入 → 附加依赖项配置到 drivers\msgparse\dmmsgparse.lib。选择常规 → 工作目录配置到 drivers\msgparse\。

第三步  编写代码，展示函数的用法。

```
#include "msgparse_pub.h"
#include <stdio.h>

#pragma comment(lib, "dmmsgparse.lib")

/*---------------------Define fun names--------------------*/
#define  REQTYPE          "ReqType"                 //ReqType 
#define  ISLOGINED        "IsLogined"               //IsLogined 
#define  HAVEAFULLREQPACK "HaveAFullReqPack"        //HaveAFullReqPack 
#define  ISNEWSQL         "IsNewSQL"                //IsNewSQL 
#define  CRTERRRESP       "CrtErrResp"              //CrtErrResp 
#define  PARSEREQ         "ParseReq"                //ParseReq 
#define  GETMSGVER        "GetMsgVer"               //GetMsgVer


// 变量声明
const schar*			proc_name;
lint					type, ret;
dmcode_t				code = 0;
byte*					respbuf;
lint					respbuflen = 32767;

res_info_t   			res_info; 
req_info_t				req_info; 

lint					pPackLen;
char*					sql = NULL;       /* INOUT：返回解析后的SQL语句 */
int						sqllen;   /* INOUT：输入时为sql的有效空间，输出时为sql数据的真实长度 */
PARAMINFO*				pParams = NULL;    /* INOUT：iParam个参数的信息存储空间*/ 
int						iParam;     /* INOUT：输入时为pParams的有效个数, 输出时为pParams的实际个数 */
int						i;
msg_startup_info_t   	info; 

int test_StartupInfo(unsigned char *buf, int len) {
	int ret;
	ret = StartupInfo(buf, len, &info);
	if(ret == 0){
		printf("success!\n"); 
	}
	else {
		printf("ret = %d not startup message! \n");
	}
	return ret;
}

int test_LoginUser(unsigned char *buf, int len) {
	int ret;
	msg_info_t  msg_login;
	byte flag = 0;
	ret = LoginUser(buf, len, &info, &msg_login);
	if(ret == 0){
		printf("%s\n", msg_login.u);
	}
	else
		printf("not login message! \n");
	return ret;
}

// 检测响应数据是否是登录成功 isLogined
int test_isLogined(unsigned char *buf, int len){
	int ret;
	ret = IsLogined(buf, len);
	if(ret==0){
		printf("logined: false\n");
	}
	else if (ret == 1){
		printf("logined: true\n");
	}
	else{
		printf("logined: ret = %d, it's maybe a bug.\n", ret);
	}
	return ret;
}

// 测试ReqType，获取消息类型
int test_ReqType(unsigned char *buf, int len){
	int type, ret;
	ret = ReqType(buf, len, &type);
	if(ret != 0){
		switch(ret) {
		case DM_INVALID_LEN:
			printf("ReqType:Invalid msg lenth\n");
			break;
		case DM_WR_MSG:
			printf("ReqType:Wrong message.\n");
			break;
		case DM_INVALID_MSG_TYPE:
			printf("ReqType:Invalid message type.\n");
			break;
		default:
			printf("ReqType:Really really error, it's maybe a bug\n");
			break;
		}
		return ret;
	} else {
		if(type>999){
			printf("definitely a bug: type = %d\n", type);
		}
		printf("type = %d\n", type);
	}
	return ret;
}

// 检测请求数据是否含有完整包长 HaveAFullReqPack
int test_HaveAFullReqPack(unsigned char *buf, int len, int pPackLen){
	int ret;
	ret = HaveAFullReqPack(buf, len, &pPackLen);//pPackLen
	if(ret != 0){
		printf("test_HaveAFullReqPack error, can't get packLen\n");
		return ret;
	} else {
		printf("pPackLen = %d\n", pPackLen);
	}
	return ret;
}

// 检测请求数据是否为新SQL指令IsNewSQL
int test_isNewSQL(unsigned char *buf, int len){
	int ret;
	ret = IsNewSQL(buf, len);
	switch(ret) {
	case DM_INVALID_LEN:
		printf("isNewSQL:Invalid msg lenth\n");
		break;
	case DM_WR_MSG:
		printf("isNewSQL:Wrong message.\n");
		break;
	case DM_INVALID_MSG_TYPE:
		printf("isNewSQL:Invalid message type.\n");
		break;
	case DM_RN_NEW_SQL:
		printf("isNewSQL:Is not new sql.\n");
		break;
	case DM_EC_SUCCESS:
		printf("isNewSQL:Ec success.\n");
		break;
	default:
		printf("isNewSQL:Really really error, it's maybe a bug\n");
		break;
	}
	return ret;
}

// 构建错误应答报文CrtErrResp
byte* test_crtErrResp(int i){
	int ret;
	respbuf = NULL;
	ret = CrtErrResp(i, "", respbuf, &respbuflen);
	if(ret != 0)
	{
		if(ret<0)
		{
			printf("crtErrResp: ret<0, It' OK\n");
		}
		else{
			printf("crtErrResp error, it's maybe a bug\n");
		}
	} else {
		printf("respbuflen = %d\n", respbuflen);

		// 再次调用，利用respbuflen构造respbuf
		respbuf = (byte*)malloc(respbuflen);
		ret = CrtErrResp(i, "message size is out of buffer.", respbuf, &respbuflen);

	}
	return respbuf;
}

// 提取请求数据中的SQL和参数ParseReq
int test_ParseReq(unsigned char *buf, int len){
	int ret, code, i;
	// 第一次调用之前，sql/sqllen/pParams，都要给NULL或0，这样ParseReq函数第一次调用会返回sqllen和iParam，用户自己利用sqllen和iParam给sql和pParams分配空间
	sql = NULL;
	sqllen = 0;
	pParams = NULL;
	ret = ParseReq(buf, len, sql, &sqllen, pParams, &iParam);
	if (ret == 0)
	{
		if (sqllen > 0)
		{
			sql = (char*)malloc(sqllen);
		}
		if (iParam > 0)
			pParams = (PARAMINFO*)malloc(sizeof(PARAMINFO) * iParam);

		//再次调用 解析sql和参数信息
		ret = ParseReq(buf, len, sql, &sqllen, pParams, &iParam);//获取iParam,pParams
		if (ret == 0)
		{
			code = DM_EC_INVALID_MSG;
			if (sqllen > 0){
				printf("sql = %s\n", sql);
				free(sql);
			}
			if (iParam > 0){
				printf("paramCount = %d\n", iParam);
				for(i=0; i<iParam; i++){
					printf("pParams->type = %d\n", pParams[i].type);
					printf("pParams->name = %s\n", pParams[i].name);
					printf("pParams->lenth = %d\n", pParams[i].vallen);
					switch(pParams[i].type){
					case 1:
						printf("pParams->val = %s\n", pParams[i].val.str);
						break;
					case 2:
					case 3:
					case 4:
						printf("pParams->val = %d\n", pParams[i].val.int_val);
						break;
					case 5:
						printf("pParams->val = %lld\n", pParams[i].val.int64_val);
						break;
					case 6:
						printf("pParams->val = %f\n", pParams[i].val.float_val);
						break;
					case 7:
						printf("pParams->val = %lf\n", pParams[i].val.double_val);
						break;
					case 8:
						printf("pParams->val = %s\n", pParams[i].val.bin_val);
						break;
					case 9:
						printf("pParams->val = %s\n", pParams[i].val.lob_val);
						break;
					case 10:
					case 11:
						printf("pParams->val = %s\n", pParams[i].val.intv_str);
						break;
					case 12:
						printf("pParams->val = %s\n", pParams[i].val.rowidstr);
						break;
					case 13:
						printf("pParams->val = %s\n", pParams[i].val.xdec_str);
						break;
					case 14:
					case 15:
					case 16:
					case 17:
					case 18:
						printf("pParams->val = %s\n", pParams[i].val.dt_str);
						break;
					default:
						break;
					}
					printf("\n");
				}
				free(pParams);
			}
		}
		else{
			printf("test_ParseReq:ret = %d\n", ret);
			if (sqllen > 0){
				printf("sql = %s\n", sql);
				free(sql);
			}
			if (iParam > 0){
				printf("paramCount = %d\n", iParam);
				free(pParams);
			}
		}
	}
	else if (ret != DM_NO_SQL_OR_PARAM) // 消息报错返回不是-6
	{
		code = DM_EC_INVALID_MSG;
		printf("test_ParseReq:ret = %d\n", ret);
		printf("test_ParseReq: type is in (126)/(111)/(105)/(110)/(127), is type right?\n");
	}
	else{ // 直接打印报错
		printf("test_ParseReq:ret = %d\n", ret);
	}

	return code;
}

// 展示可解析的消息格式版
int test_GetMsgVer(){
	int ret;
	ret = GetMsgVer();
	if(ret != 10){
		printf("GetMsgVer error: %d\n", ret);
	}
	return ret;
}


// 主函数
int main()
{
	// 这里放置测试程序截获的客户端发给服务器的消息
	// 此测例中的消息使用wireshark抓包工具获取，包头中带有其他信息，真正的消息从偏移54位开始
	static const unsigned char pkt6[179] = {
		0xa4, 0xae, 0x12, 0x2a, 0x6e, 0xb4, 0xe0, 0x24, 
		0x7f, 0xc3, 0x90, 0xa2, 0x08, 0x00, 0x45, 0x00, 
		0x00, 0xa5, 0xff, 0x58, 0x40, 0x00, 0x3f, 0x06, 
		0x55, 0x07, 0xc0, 0xa8, 0x64, 0x7b, 0xc0, 0xa8, 
		0x01, 0x27, 0x18, 0x74, 0x43, 0xca, 0x6b, 0x6c, 
		0xe9, 0x62, 0x87, 0xf4, 0x16, 0x08, 0x50, 0x18, 
		0x00, 0x03, 0x05, 0x84, 0x00, 0x00, 0x00, 0x00,
		0x00, 0x00, 0x9d, 0x00, 0x3d, 0x00, 0x00, 0x00, 
		0xc6, 0xf7, 0xff, 0xff, 0x00, 0x00, 0x00, 0x00, 
		0x00, 0x91, 0x9d, 0x00, 0x00, 0x00, 0x00, 0x00, 
		0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 
		0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 
		0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 
		0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 
		0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 
		0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 
		0x00, 0x00, 0x2d, 0x00, 0x00, 0x00, 0xb5, 0xda, 
		0x31, 0x20, 0xd0, 0xd0, 0xb8, 0xbd, 0xbd, 0xfc, 
		0xb3, 0xf6, 0xcf, 0xd6, 0xb4, 0xed, 0xce, 0xf3, 
		0x3a, 0x0a, 0xce, 0xde, 0xd0, 0xa7, 0xb5, 0xc4, 
		0xb1, 0xed, 0xbb, 0xf2, 0xca, 0xd3, 0xcd, 0xbc, 
		0xc3, 0xfb, 0x5b, 0x43, 0x4c, 0x4f, 0x42, 0x5f, 
		0x31, 0x57, 0x5d                        
	};

	// startup消息
	static const unsigned char pkt7[198] = {
		0xf4, 0x6b, 0x8c, 0x88, 0xdf, 0x62, 0xa4, 0xae, 
		0x12, 0x2a, 0x6e, 0xb4, 0x08, 0x00, 0x45, 0x00, 
		0x00, 0xb8, 0x53, 0x3d, 0x40, 0x00, 0x80, 0x06, 
		0x00, 0x00, 0xc0, 0xa8, 0x01, 0x27, 0xc0, 0xa8, 
		0x01, 0x65, 0x4d, 0xc4, 0x14, 0x74, 0x90, 0x4a, 
		0x77, 0x9c, 0x37, 0xf7, 0xcf, 0xab, 0x50, 0x18, 
		0x10, 0x0a, 0x84, 0x87, 0x00, 0x00, 0x00, 0x00, 
		0x00, 0x00, 0xc8, 0x00, 0x50, 0x00, 0x00, 0x00, 
		0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 
		0x00, 0x98, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 
		0x00, 0x00, 0x01, 0x02, 0x00, 0x00, 0x00, 0x00, 
		0x01, 0x0a, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 
		0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 
		0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 
		0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x07, 0x00, 
		0x00, 0x00, 0x38, 0x2e, 0x31, 0x2e, 0x30, 0x2e, 
		0x30, 0x00, 0x40, 0x00, 0x00, 0x00, 0x29, 0x35, 
		0x46, 0xbf, 0xff, 0x09, 0x64, 0x62, 0x91, 0x3c, 
		0xc1, 0xeb, 0xc1, 0x40, 0x80, 0x52, 0x5c, 0xa0, 
		0x22, 0x3c, 0x8c, 0x80, 0x1c, 0xd7, 0x5d, 0x14, 
		0xe2, 0x8c, 0xc2, 0x9e, 0xf9, 0xd1, 0xa8, 0xb5, 
		0x00, 0x3c, 0xae, 0x90, 0x91, 0x7d, 0xdb, 0x9c, 
		0x07, 0x25, 0x85, 0x35, 0x8c, 0xb8, 0x66, 0xb3, 
		0x77, 0x59, 0x07, 0x80, 0xbf, 0x60, 0x70, 0x44, 
		0x8f, 0x7c, 0xb0, 0xe5, 0x56, 0xd2      
	};

	// 登录消息 
	static const unsigned char pkt8[182] = {
		0xf4, 0x6b, 0x8c, 0x88, 0xdf, 0x62, 0xa4, 0xae, 
		0x12, 0x2a, 0x6e, 0xb4, 0x08, 0x00, 0x45, 0x00, 
		0x00, 0xa8, 0x53, 0x3f, 0x40, 0x00, 0x80, 0x06, 
		0x00, 0x00, 0xc0, 0xa8, 0x01, 0x27, 0xc0, 0xa8, 
		0x01, 0x65, 0x4d, 0xc4, 0x14, 0x74, 0x90, 0x4a, 
		0x78, 0x2c, 0x37, 0xf7, 0xd0, 0x54, 0x50, 0x18, 
		0x10, 0x09, 0x84, 0x77, 0x00, 0x00, 0x00, 0x00, 
		0x00, 0x00, 0x01, 0x00, 0x40, 0x00, 0x00, 0x00, 
		0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 
		0x00, 0x00, 0x02, 0x00, 0x00, 0x00, 0xff, 0xff, 
		0xff, 0xff, 0x00, 0x00, 0x00, 0x00, 0x00, 0xe0, 
		0x01, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x01, 
		0x00, 0x01, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 
		0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 
		0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x04, 0x00, 
		0x00, 0x00, 0x5b, 0xd0, 0xc5, 0x7e, 0x09, 0x00, 
		0x00, 0x00, 0xd8, 0x00, 0x3c, 0x9e, 0x9b, 0x72, 
		0xb0, 0x80, 0x29, 0x00, 0x00, 0x00, 0x00, 0x0a, 
		0x00, 0x00, 0x00, 0x57, 0x69, 0x6e, 0x64, 0x6f, 
		0x77, 0x73, 0x20, 0x31, 0x30, 0x08, 0x00, 0x00, 
		0x00, 0x4c, 0x56, 0x4a, 0x49, 0x41, 0x59, 0x55, 
		0x45, 0x00, 0x06, 0x00, 0x75, 0x3d, 0xb2, 0xe2, 
		0xca, 0xd4, 0x87, 0xed, 0x49, 0xed      
	};

	int len = sizeof(pkt6);
	byte* errorMessage = NULL;

	// 展示可解析的消息格式版
	test_GetMsgVer();

	// 判断消息是否是登录消息
	test_isLogined((unsigned char*)pkt6+54, len-54);

	// 获取消息类型
	test_ReqType((unsigned char*)pkt6+54, len-54);

	// 判断消息是否含有是完整的包长
	test_HaveAFullReqPack((unsigned char*)pkt6+54, len-54, pPackLen);

	// 判断消息是否是一次新的sql请求
	test_isNewSQL((unsigned char*)pkt6+54, len-54);

	// 解析消息中的sql与参数信息
	test_ParseReq((unsigned char*)pkt6+54, len-54);

	// 构建错误应答报文
	errorMessage = test_crtErrResp(DM_EC_UKEY_AUTH_MISMATCH);

	len = sizeof(pkt7);
	// 是否是ukey认证
	test_StartupInfo((unsigned char*)pkt7+54, len-54);

	len = sizeof(pkt8);
	// 获取登录用户名
	code = test_LoginUser((unsigned char*)pkt8+54, len-54);

	//请求消息
	static const unsigned char pkt9[] = {
     /`````````按实际情况插入请求信息`````````/  };
	//响应消息
	static const unsigned char pkt10[] = {
     /`````````按实际情况插入响应信息`````````/	  };

	int len1 = sizeof(pkt9); 
	//获取请求消息中的信息
	code = ParseReqInfo(pkt9+54, len1-54, &req_info);

len1 = sizeof(pkt10); 
	//获取响应消息中的信息
     code = ParseRes(pkt10+54, len1-54, &req_info, &res_info);

	return code;
}
```

第四步 编译代码。

VS2010 工程中，右键项目名称，点击生成。

### 13.2.2 Linux 环境示例

下面以 Linux 环境为例进行说明。

第一步 编写代码，展示函数的用法。

准备文件 dm_cmpp_test_linux.c。文件内容与上面 Windows 的示例一样，只需去掉 windows 环境引用的头文件 windows.h。

第二步 编译 dm_cmpp_test_linux.c 文件。

使用-I 配置头文件 msgparse_pub.h 所在目录，使用-L 配置 libdmmsgparse.so 文件所在目录。

```
gcc -g dm_cmpp_test_linux.c -o dm_cmpp_test_linux -I/dmdbms/drivers/msgparse/include -L/dmdbms/drivers/msgparse -ldmmsgparse -ldl
```

第三步 执行 dm_cmpp_test_linux 可执行文件。

```
./dm_cmpp_test_linux 
```
