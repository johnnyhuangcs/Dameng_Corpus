

# DBMS_ERRLOG 包

DBMS_ERRLOG 包提供一种方法给指定表创建对应的错误日志表。

创建错误日志表的方法有两种，一是通过 DBMS_ERRLOG 包中的 CREATE_ERROR_LOG 来创建，二是通过 SQL 语法手动创建。因为使用 DBMS_ERRLOG 包创建错误日志表简单易行，建议用户使用方法一。

## 41.1 相关方法

给指定表创建对应的错误日志表。不支持指定表中含有列类型为 LONG、CLOB、 BLOB、 BFILE、自定义类型的列。也不支持指定的表中和要创建的错误日志表含有同名列。

语法如下：

```
PROCEDURE CREATE_ERROR_LOG(
	DML_TABLE_NAME			IN 		VARCHAR,
	ERR_LOG_TABLE_NAME		IN 		VARCHAR 		DEFAULT NULL,
	ERR_LOG_TABLE_OWNER		IN 		VARCHAR 		DEFAULT NULL,
	ERR_LOG_TABLE_SPACE		IN 		VARCHAR 		DEFAULT NULL,
	SKIP_UNSUPPORTED		IN 		BOOLEAN  		DEFAULT FALSE
);
```

参数详解

● DML_TABLE_NAME   输入参数， 错误日志记录表所基于的数据库表，支持以前缀形式指定其他用户的表。

● ERR_LOG_TABLE_NAME  输入参数，  用户创建的错误日志记录表的名字，默认使用数据库表名加上前缀’ERR$_’。错误日志记录表前 5 列的固定结构如下：

|**序号**|**列**|**数据类型**|**说明**|
|----|----|----|----|
|1|DM_ERR_NUMBER$|INTEGER|错误码|
|2|DM_ERR_MESG$|VARCHAR(2000)|错误信息|
|3|DM_ERR_ROWID$|ROWID|保存出错的行号，INSERT 时为 0（0 将会使用 ROWID 格式显示）|
|4|DM_ERR_OPTYPE$|VARCHAR(2)|出错的 DML 类型, I 表示插入, U 表示更新, D 表示删除|
|5|DM_ERR_TAG$|VARCHAR(2000)|log errors 语法(expression)设定的值, 未设置时为空|


● ERR_LOG_TABLE_OWNER   输入参数，错误日志记录表的拥有者，默认使用当前连接的用户。

● ERR_LOG_TABLE_SPACE   输入参数，错误日志记录表所在的表空间，需要有权限，默认使用当前用户使用的表空间。

● SKIP_UNSUPPORTED   输入参数，设置为 TRUE 时，列类型不支持的将会被跳过并且不会添加到错误日志记录表，设置为 FALSE 时，一个不支持的列类型将会引发过程中断，默认为 FALSE。

通过 SQL 语法手动创建错误日志表（例如，表名为 LOG_T1）。为了减少出错环节，不推荐用户自己手动创建。

```
CREATE TABLE LOG_T1(
DM_ERR_NUMBER$ INTEGER,
DM_ERR_MESG$ VARCHAR(2000),
DM_ERR_ROWID$ ROWID,
DM_ERR_OPTYPE$ VARCHAR(2),
DM_ERR_TAG$ VARCHAR(2000)
);
```

错误日志表的其他列可以有源表的列, 当存在与源表同名的列时, 会将源表该列的出错值插入错误日志表的对应列。使用系统包提供的方法创建的错误日志表, 除固定的前 5 列外， 其余列类型固定为 VARCHAR(4000)，用户自定义列也应尽量使用相同类型, 避免出现不必要的错误导致插入数据失败，无法达到预期效果。

## 41.2 使用说明

  1. 使用该包需要有包执行权限，表的操作权限，建表权限。
  2. 支持对日志表进行 I/D/U 操作。
  3. 暂时不支持对视图建错误日志表。
  4. 暂时不支持对系统表建错误日志表。
  5. 暂时不支持批量绑定参数执行*\<*log erros> 语法。
  6. 只有错误发生在 I/D/U 的自身操作符执行期间才会记录日志。
  7. 部分错误在进行错误日志记录时无法登记操作相关的数据，如字符串转换出错。更新操作的一些错误发生在获取原始数据前，因此无法登记操作相关数据，比如违反非空约束、数据超过数据类型限制等。
  8. 暂时不支持 INSERT ALL 语句中使用*\<*log erros> 语法。
  9. 虚拟列暂不进行记录数据。
  10. 暂时不支持 DBLINK。
  11. 错误日志表记录的内容长度须小于 4000 字节，数据超长时将会报错处理。
  12. 暂时不支持 DPC 环境。
  13. 不支持分区子表作为错误日志表。



## 41.3 使用错误日志表

在数据插入语句、数据修改语句或数据删除语句的尾部使用*\<*log errors>，可以将错误操作生成错误记录，登记到错误日志表中。

log errors 语法:

```
<数据插入语句><log errors>;
<数据修改语句><log errors>;
<数据删除语句><log errors>;
<log errors>::=LOG ERRORS [INTO <表名>][(<expression>)] REJECT LIMIT <UNLIMITED | <错误行数>>
<数据插入语句>::=请参考《DM8_SQL语言使用手册》第5.1节
<数据修改语句>::=请参考《DM8_SQL语言使用手册》第5.2节
<数据删除语句>::=请参考《DM8_SQL语言使用手册》第5.3节
```

参数详解

● [INTO \< 表名 >]  错误日志表的表名。缺省使用的是默认错误日志表名。

● *\<expression>  是一个表达式，为错误日志记录的一个标记，即每一行错误日志会有一列存储此列表达式的值，该 \<*expression> 需要是字符串或者是可以转换为字符串的内容。

● REJECT LIMIT \<UNLIMITED | \< 错误行数 >>  指定允许的最大错误行数。UNLIMITED 表示没有限制；\< 错误行数 > 指定的允许的最大错误行数，取值(0~ 2147483647)，超过此值将回滚 DML 操作，但仍保留已经生成的错误日志。错误日志表生成记录后，不会因为 DML 语句的回滚而回滚，也不会因为基表被删除而同步被删除。其中，空间不足导致的回滚，不会在错误日志表中记录回滚记录。 针对不同的 DML 操作，错误日志表中记录的错误类型如下表所示：

|**DML 操作**|**字符串转换出错**|**违反唯一约束条件**|**不允许 NULL 值**|**数据超过数据类型限制**|**分区列更新造成分区更新**|**插入值不满足分区范围**|
|-----------|-----------|-----------|-----------|-----------|-----------|-----------|
|INSERT|支持|支持|支持|支持|×|支持|
|UPDATE|×|×|支持|支持|支持|×|
|DELETE|×|×|×|×|×|×|


注：符号“×”表示错误日志表中暂不支持记录该报错信息

## 41.4 举例说明

使用包内的过程和函数之前，如果还未创建过系统包，请先调用系统过程 SP_CREATE_SYSTEM_PACKAGES 创建系统包。

```
SP_CREATE_SYSTEM_PACKAGES(1,'DBMS_ERRLOG');
```

首先，创建数据库表 t1，并使用 DBMS_ERRLOG 为 t1 创建一个错误日志表。同时插入 3 条数据。

```
create table t1 (id int primary key,name varchar(20));
begin   
  DBMS_ERRLOG.CREATE_ERROR_LOG('T1','LOG_T1');
end;
/
insert into t1 values(1, 'a');
insert into t1 values(2, 'b');
insert into t1 values(10, 'j');
```

其次，在插入数据的时候使用*\<*log errors>。为 t1 插入 20 条数据，其中有三条是违反了唯一性约束的。

```
begin
  for i in 1..20 loop
    insert into t1 values(i,i) LOG ERRORS INTO LOG_T1 ('INSERT ERROR') REJECT LIMIT UNLIMITED;
  end loop;
  commit;
end;
/
```

然后，查看日志信息表。

```
select * from LOG_T1;
```

查询结果如下：

|**行号**|**DM_ERR_NUMBER​** | **DM_ERR_MESG**|**DM_ERR_ROWID​**  | **DM_ERR_OPTYPE**|**DM_ERR_TAG$**|**ID**|**NAME**|****|****|
|----------------------------------|----------------------------------|----------------------------------|----------------------------------|----------------------------------|----------------------------------|----------------------------------|----------------------------------|
|1|-6602|违反表[T1]唯一性约束条件[CONS134218802]|AAAAAAAAAAAAAAAAAA|I|INSERT ERROR|1|1|
|2|-6602|违反表[T1]唯一性约束条件[CONS134218802]|AAAAAAAAAAAAAAAAAA|I|INSERT ERROR|2|2|
|3|-6602|违反表[T1]唯一性约束条件[CONS134218802]|AAAAAAAAAAAAAAAAAA|I|INSERT ERROR|10|10|


可见，LOG_T1 中记录了插入失败的数据以及相关信息。

```
select count(*) from t1;
```

查询结果为：20。

可见，t1 中插入了全部未出错的数据。之前的 3 条和后来批量插入的 17 条。
