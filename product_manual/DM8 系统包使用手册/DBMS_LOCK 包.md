

# DBMS_LOCK 包

DBMS_LOCK 包提供了锁管理服务器的接口。该包可以完成下列功能：

  1. 提供对设备的独占访问；
  2. 提供程序级的读锁；
  3. 判断程序之后何时释放锁；
  4. 同步程序以及强制顺序执行。



DM MPP 环境下不支持 DBMS_LOCK 包。

## 8.1 封锁规则

1、封锁类型

DBMS_LOCK 包过程函数对于封锁的类型定义如下表所示，其数据类型为 INTEGER。

|**名称**|**意义**|**值**|**描述**|
|--|--|--|--|
|NL_MODE|NULL|1||
|SS_MODE|Sub Shared|2|可以使用在聚合对象以指定在该对象的子部分上获取的共享锁|
|SX_MODE|Sub eXclusive Row Exclusive Mode|3|可以使用在聚合对象以指定在该对象的子部分上获取的独占锁|
|S_MODE|Shared Row Exclusive Mode Intended Exclusive|4|共享锁|
|SSX_MODE|Shared Sub eXclusive Share Row Exclusive Mode|5|该模式表名整个聚合对象占有共享锁，但其某些组成部分会上独占锁（S_MODE+SX_MODE）|
|X_MODE|eXclusive|6|独占锁|


2、兼容规则

若当前锁对象已被其他程序封锁，新封锁的类型兼容规则如下：

|**当前持有**|**新加**|****|****|****|****|****|
|----|----|----|----|----|----|----|
||NL|SS|SX|S|SSX|X|
|NL|Success|Success|Success|Success|Success|Success|
|SS|Success|Success|Success|Success|Success|Fail|
|SX|Success|Success|Success|Fail|Fail|Fail|
|S|Success|Success|Fail|Success|Fail|Fail|
|SSX|Success|Success|Fail|Fail|Fail|Fail|
|X|Success|Fail|Fail|Fail|Fail|Fail|


## 8.2 相关方法

  1. ALLOCATE_UNIQUE



申请一个锁对象。已经申请的锁对象可在 SYS.DBMS_LOCK_ALLOCATED 表查询。

语法如下：

```
PROCEDURE ALLOCATE_UNIQUE (
	LOCK_NAME			IN		VARCHAR2,
	LOCK_HANDLE			OUT		VARCHAR2,
	EXPIRATION_SECS		IN		INTEGER	DEFAULT 864000
);
```

参数详解

  * LOCK_NAME

输入参数，锁对象名称。

  * LOCK_HANDLE

输出参数，锁对象句柄，根据锁名生成的唯一标识；后续过程可以使用该标识对锁对象进行操作。会话对一个新的锁名调用该过程时，会在系统中新生成一个锁对象，在
DBMS_LOCK_ALLOCATED 表中插入一行数据记录锁的相关信息。

  * EXPIRATION_SECS

输入参数，锁对象的过期时间。最后一次调用 ALLOCATE_UNIQUE 后允许从 DBMS_LOCK_ALLOCATED
表中删除数据的等待时间，单位为秒。



  2. REQUEST



根据指定类型进行封锁。

语法如下：

```
FUNCTION  REQUEST(
	LOCK_ID				IN		INTEGER,
	LOCK_MODE			IN		INTEGER	DEFAULT	X_MODE,
	TIMEOUT				IN		INTEGER	DEFAULT	MAXWAIT,
	RELEASE_ON_COMMIT	IN		BOOLEAN	DEFAULT	FALSE
)RETURN INTEGER;
FUNCTION  REQUEST(
	LOCK_HANDLE			IN		VARCHAR2,
	LOCK_MODE			IN		INTEGER DEFAULT X_MODE,
	TIMEOUT				IN		INTEGER DEFAULT MAXWAIT,
	RELEASE_ON_COMMIT	IN		BOOLEAN DEFAULT FALSE
)RETURN INTEGER;
```

参数详解

  * LOCK_ID 或 LOCK_HANDLE

输入参数，申请进行封锁动作的锁对象 id 或句柄。

  * LOCK_MODE

输入参数，上锁的模式。

  * TIMEOUT

输入参数，尝试上锁的等待时间，单位为秒。

  * RELEASE_ON_COMMIT

输入参数，指明在事务结束时是否释放锁。



返回值说明：

|**返回值**|**描述**|
|---|---|
|0|成功|
|1|超时|
|2|死锁|
|3|参数错误|
|4|没有拥有锁|
|5|无效的锁 id 或句柄|


使用说明：

若使用 LOCK_ID 进行封锁，锁对象的 ID 取值范围为 0~1073741823。否则返回 3（参数错误）。

使用名称 ALLOCATE_UNIQUE 的锁对象的 ID 范围为 1073741824~1999999999。也就是说，使用 ALLOCATE_UNIQUE 方式创建的表对象不能通过 LOCK_ID 的方式进行封锁转化和释放。同时可以使用 LOCK_ID 不进行 ALLOCATE_UNIQUE 而直接 REQUEST，但通过 LOCK_ID 方式使用的锁对象不会插入到 DBMS_LOCK_ALLOCATED 表中。

  3. CONVERT/CONVERT2



对封锁类型进行修改。

语法如下：

```
FUNCTION  CONVERT(
	LOCK_ID			IN		INTEGER,
	LOCK_MODE		IN		INTEGER,
	TIMEOUT			IN		NUMBER	DEFAULT	MAXWAIT
)RETURN INTEGER;
FUNCTION  CONVERT( 
	LOCK_HANDLE		IN		VARCHAR2,
	LOCK_MODE		IN		INTEGER,
	TIMEOUT			IN		NUMBER	DEFAULT	MAXWAIT
)RETURN INTEGER;
FUNCTION  CONVERT2(
	LOCK_ID			IN		INTEGER,
	LOCK_MODE		IN		INTEGER,
	TIMEOUT			IN		NUMBER	DEFAULT	MAXWAIT
)RETURN INTEGER;
FUNCTION  CONVERT2( 
	LOCK_HANDLE		IN		VARCHAR2,
	LOCK_MODE		IN		INTEGER,
	TIMEOUT			IN		NUMBER	DEFAULT	MAXWAIT
)RETURN INTEGER;
```

参数详解

  * LOCK_ID 或 LOCK_HANDLE

输入参数，要修改的锁对象 ID 或根据过程 ALLCOATE_UNIQUE 得到的锁对象句柄；LOCK_ID 的说明同 REQUEST。

  * LOCK_MODE

对锁对象新的封锁模式。

  * TIMEOUT

尝试修改的等待时间，如果在该阶段内没有成功，返回 1（超时）。



返回值说明：

|**返回值**|**描述**|
|---|---|
|0|成功|
|1|超时|
|2|死锁|
|3|参数错误|
|4|没有拥有锁|
|5|无效的锁的 id 或句柄|


使用说明：

如果只有当前程序对该锁对象进行了封锁，可以成功转化到其他任意封锁模式；

如果与其他程序并发封锁，新的上锁模式是否能够成功要取决于其他程序所持锁的模式与新的封锁模式是否兼容，如果不兼容，则上锁失败；

由于 CONVERT 是 DM 数据库的保留字，调用 CONVERT 函数时需要使用“”对其进行引用，或者使用同样功能的 CONVERT2。

  4. RELEASE 函数



DBMS_LOCK 包默认在会话结束时释放对锁对象的封锁，该函数用于显式地释放当前会话持有的一个锁对象的封锁。

语法如下：

```
FUNCTION  RELEASE (
	LOCK_ID			IN		INTEGER
)RETURN INTEGER;
FUNCTION  RELEASE (
	LOCK_HANDLE		IN		VARCHAR2
)RETURN INTEGER;
```

参数详解

  * LOCK_ID 或 LOCK_HANDLE

输入参数，要释放封锁的锁对象 ID 或根据过程 ALLCOATE_UNIQUE 返回的句柄；LOCK_ID 的说明同 REQUEST。



返回值说明：

|**返回值**|**描述**|
|---|---|
|0|成功|
|3|参数错误|
|4|没有拥有锁|
|5|无效的锁的 id 或句柄|


  5. SLEEP 过程



使会话休眠一段时间。

语法如下：

```
PROCEDURE SLEEP (
	SECONDS		IN		NUMBER
);
```

参数详解

  * SECONDS

输入参数，休眠时间，单位为秒。



## 8.3 举例说明

使用包内的过程和函数之前，如果还未创建过系统包，请先调用系统过程创建系统包。

```
SP_CREATE_SYSTEM_PACKAGES (1,'DBMS_LOCK');
SET SERVEROUTPUT ON;  //PRINT需要设置这条语句，才能打印出消息
```

如果要通过名称来进行封锁，首先要通过 ALLOCATE_UNIQUE 过程申请封锁句柄，之后通过该句柄进行封锁和释放操作。如下例以‘my_lock’申请句柄并上 X 锁。

```
declare
	hdl varchar(128);
	ret int;
begin
	DBMS_LOCK.ALLOCATE_UNIQUE('my_lock', hdl);
	select DBMS_LOCK.REQUEST(hdl, 6) into ret;
	print ret;
end;
/
```

执行成功后，其他会话同上例执行封锁，会因封锁不兼容而进行等待。

若已经进行了封锁，需要对封锁的类型进行修改，比如，上述例子中进行 X 封锁的级别太高，导致其他会话无法对’my_lock’进行封锁，可将其转换为 S 锁。

```
declare
	hdl varchar(128);
	ret int;
begin
	DBMS_LOCK.ALLOCATE_UNIQUE('my_lock', hdl);
	select DBMS_LOCK."CONVERT"(hdl, 4) into ret;
	print ret;
end;
/
```

转换成功后，其他会话以与 S 锁兼容的模式（比如 NL，SS，S 模式）进行封锁均可成功。

若已经完成了封锁内需要的操作，可以以如下方式释放该封锁。

```
declare
	hdl varchar(128);
begin
	DBMS_LOCK.ALLOCATE_UNIQUE('my_lock', hdl);
	DBMS_LOCK.RELEASE(hdl);
end;
/
```

上述各例中，均通过句柄进行封锁操作，用户也可直接通过 id 进行封锁。封锁和转换时省略超时时间表示一直等待直至成功，用户可根据需要设置超时时间，也可将超时时间设置为 0 使其立刻返回。

通过名称申请句柄的锁对象，会添加在系统表 SYS.DBMS_LOCK_ALLOCATED 中查询得到。该系统表中的数据在锁对象过期后根据情况进行淘汰。

如上操作后，查询可以得到‘my_lock’的相关信息，包括名称、id 及过期时间。

```
SQL>select * from SYS.DBMS_LOCK_ALLOCATED;
//查询结果如下：
行号 NAME   LOCKID      EXPIRATION
---------- ------- ----------- ---------------------------
1 my_lock 1073741824 2014-07-20 16:19:59.708000
```

DM 也提供动态性能视图 V$DBMS_LOCKS 供用户查看当前系统中 DBMS_LOCK 封锁的情况。如两个会话并发申请‘my_lock’，其中 S 锁封锁成功，X 锁封锁等待的情况，查询可得如下结果：

```
SQL>select * from V$DBMS_LOCKS;
//查询结果如下：
行号HANDLE   ID   MODE   OWN_SESSWAIT_SESS
----------- ---- --------------- ------ ------ ------ -----------
1 10737418241073741824176 1073741824 S 515604448 NULL
2 10737418241073741824176 1073741824 X 516931992 515604448
```

通过记录可以看出 X 封锁在等待 S 封锁。
