

# DMSQL 程序中的 SQL 语句

使用 DMSQL 程序的主要目的是对 DM 数据库进行访问，因此 DMSQL 程序中支持使用 SQL 语句进行对数据库对象的 SELECT、INSERT、UPDATE、DELETE 等 DML 操作，还可以定义和操纵游标等。DMSQL 程序支持的 SQL 语句具体包括：

  * 数据查询语句(SELECT)
  * 数据操纵语句(INSERT、DELETE、UPDATE)
  * 游标定义及操纵语句(DECLARE、OPEN、FETCH、CLOSE)
  * 事务控制语句(COMMIT、ROLLBACK、SAVEPOINT)
  * 动态 SQL 执行语句(EXECUTE IMMEDIATE)



通过 SQL 语句及上一章介绍的各种控制结构，用户可以编写复杂的 DMSQL 程序，实现复杂逻辑的数据库访问应用。

## 5.1 普通静态 SQL 语句

### 5.1.1 数据操纵

在 DMSQL 程序中，可以直接使用 INSERT、DELETE 和 UPDATE 语句对数据库中的表进行增、删、改操作。

举例说明：

```
DECLARE

	catogory CONSTANT VARCHAR(50):='天文';

BEGIN

	INSERT INTO PRODUCTION.PRODUCT_CATEGORY VALUES(catogory);

END;

/
```

上面的例子中在 INSERT 语句中直接使用了常量作为列值进行插入。在实际应用中，由于 DMSQL 程序的编程特性，更常见的使用方式是在 DML 语句中使用 DMSQL 程序中定义的变量，如下例所示。

例 2 在 DML 语句中使用 DMSQL 程序中定义的变量进行 INSERT 操作

```
CREATE OR REPLACE PROCEDURE proc_ins(category IN VARCHAR(50)) AS
BEGIN
	INSERT INTO PRODUCTION.PRODUCT_CATEGORY VALUES(category);
END;
/
CALL PROC_INS('动漫');
```

UPDATE 和 DELETE 语句的 WHERE 子句中也可以使用变量，例如：

```
DECLARE
	catogory1 VARCHAR(50);
	catogory2 VARCHAR(50);
BEGIN
	catogory1='动漫';
	catogory2='高等数学';
	DELETE PRODUCTION.PRODUCT_CATEGORY WHERE NAME=catogory1;
	UPDATE PRODUCTION.PRODUCT_CATEGORY SET NAME=catogory2 WHERE NAME='数学';
END;
/
```

### 5.1.2 数据查询

在 DMSQL 程序中可直接使用 SELECT 语句从数据库中查询数据。由于通常对数据进行查询的目的是为了进一步进行处理，而不仅仅是显示出来，所以 SELECT 语句可以采用下面的语法将查询到的数据赋给相应变量以便后面对它进行引用。

语法如下：

```
SELECT<列名>{,<列名>} INTO <变量>{,<变量>} FROM……
```

与一般 SELECT 语句不同，上面语法所示的 SELECT 语句使用 INTO 子句将查询到的数据存放到变量中。查询的列与 INTO 子句中的变量在数目、类型上要一致，否则会报错。

举例说明：

例  查询书名和出版社，存放到对应变量中并打印出来。

```
DECLARE
	p_name VARCHAR(50);
	p_publish VARCHAR(50);
BEGIN
	SELECT NAME,PUBLISHER INTO p_name,p_publish FROM PRODUCTION.PRODUCT WHERE AUTHOR LIKE '曹雪芹,高鹗';
	PRINT p_name;
	PRINT p_publish;
EXCEPTION
	WHENNO_DATA_FOUND OR TOO_MANY_ROWS THEN
		PRINT'NO_DATA_FOUND OR TOO_MANY_ROWS';
	WHEN OTHERS THEN
		PRINT 'ERROR OCCURS';
END;
/
```

SELECT…INTO 语句要求查询只能返回一条记录，执行时可能会发生两种例外情况：

  * 没有查询到满足条件的记录，系统返回预定义异常 NO_DATA_FOUND；
  * 存在多行满足条件的记录，系统返回预定义异常 TOO_MANY_ROWS，对于这样的异常必须做出相应处理，否则会影响 DMSQL 程序的正确执行。



### 5.1.3 事务控制

可以在 DMSQL 程序中直接使用 COMMIT、ROLLBACK 和 SAVEPOINT 语句进行事务控制：

  * \`COMMIT[ WORK];\` 语句提交一个事务
  * \`ROLLBACK[ WORK];\` 语句回滚一个事务
  * \`SAVEPOINT \\<保存点名\>;\` 语句在事务中设置一个保存点
  * \`ROLLBACK [WORK] TO SAVEPOINT \\<保存点名\>;\` 语句将事务回滚到指定的保存点



## 5.2 游标

5.1.2 节中介绍了 DMSQL 程序中使用 SELECT…INTO 语句将查询结果存放到变量中进行处理的方法，但这种方法只能返回一条记录，否则就会产生 TOO_MANY_ROWS 错误。为了解决这个问题，DMSQL 程序引入了游标，允许程序对多行数据进行逐条处理。

### 5.2.1 静态游标

静态游标是只读游标，它总是按照打开游标时的原样显示结果集，在编译时就能确定静态游标使用的查询。

静态游标又分为两种：隐式游标和显式游标。

#### 5.2.1.1 隐式游标

隐式游标无需用户进行定义，每当用户在 DMSQL 程序中执行一个 DML 语句（INSERT、UPDATE、DELETE、SELECT）或者 SELECT...INTO 语句时，DMSQL 程序都会自动声明一个隐式游标并管理这个游标。

隐式游标的名称统一称为“SQL”，用户可以通过隐式游标获取语句执行的一些信息。

##### 5.2.1.1.1 使用隐式游标的步骤

数据库自动执行隐式游标的相关操作。比如：打开游标、提取数据，关闭游标等。

##### 5.2.1.1.2 隐式游标属性

DMSQL 程序中的每个游标都有 %FOUND、%NOTFOUND、%ISOPEN 和 %ROWCOUNT 四个属性。兼容 SQL SERVER 模式下，SELECT 语句与 SELECT…INTO 语句的属性表现一致，否则仅 %ISOPEN 属性表现一致，以下属性描述以 SELECT…INTO 语句为准。对于隐式游标，这四个属性的意义如下：

  * %FOUND：若没有执行 DML 或查询语句，返回 NULL；若执行了 DML 或查询语句，判断语句是否影响或查询到了记录，是返回 TRUE，否则返回 FALSE；
  * %NOTFOUND：若没有执行 DML 或查询语句，返回 NULL；若执行了 DML 或查询语句，判断语句是否未能成功影响或查询到记录，是返回 TRUE，否则返回 FALSE；
  * %ISOPEN：游标是否打开。是返回 TRUE，否返回 FALSE。由于系统在语句执行完成后会自动关闭隐式游标，因此隐式游标的 %ISOPEN 属性永远为 FALSE；
  * %ROWCOUNT：DML 语句执行影响的行数，或 SELECT…INTO 语句返回的行数，非兼容 SQL SERVER 模式下 SELECT 语句返回 0。



例 将孙丽的电话号码修改为 13818882888。示例中的“SQL”为隐式游标的名称。

```
BEGIN
	UPDATE PERSON.PERSON SET PHONE=13818882888 WHERE NAME='孙丽';
	IF SQL%NOTFOUND THEN
		PRINT '此人不存在';
	ELSE
		PRINT '已修改';
	END IF;
END;
/
```

#### 5.2.1.2 显式游标

显式游标指向一个查询语句执行后的结果集区域。当需要处理返回多条记录的查询时，应显式地定义游标以处理结果集的每一行。

##### 5.2.1.2.1 使用显式游标步骤

使用显式游标一般包括四个步骤：

  1. 定义游标：在 DMSQL 程序的声明部分定义游标，声明游标及其关联的查询语句；
  2. 打开游标：执行游标关联的语句，将查询结果装入游标工作区，将游标定位到结果集的第一行之前；
  3. 拨动游标：根据应用需要将游标位置移动到结果集的合适位置；
  4. 关闭游标：游标使用完后应关闭，以释放其占有的资源。



下面对这四个步骤进行具体介绍。

■ 定义显式游标

在 DMSQL 程序的声明部分定义显式游标，其语法如下：

```
CURSOR <游标名> [FAST | NO FAST] <cursor选项>;|
<游标名> CURSOR [FAST | NO FAST] <cursor选项>;

<cursor选项> :=<cursor选项1>|<cursor选项2>|<cursor选项3>|<cursor选项4>
<cursor选项1>:= <IS|FOR> {<查询表达式>|<连接表>}
<cursor选项2>:= <IS|FOR> TABLE <表名>
<cursor选项3>:= (<参数声明> {,<参数声明>})IS <查询表达式>
<cursor选项4>:= [(<参数声明> {,<参数声明>})] RETURN <DMSQL数据类型> IS <查询表达式>
<参数声明> ::= <参数名> [IN] <参数类型> [ DEFAULT|:= <缺省值> ]
<DMSQL数据类型> ::= <普通数据类型>
                | <变量名> %TYPE
                | <表名> %ROWTYPE
                | CURSOR
                | REF <游标名>
```

语法中的“FAST”指定游标是否为快速游标。缺省为 NO FAST，为普通游标。快速游标提前返回结果集，速度上提升明显，但是存在以下的使用约束：

  * FAST 属性只在显式游标中支持；
  * 使用快速游标的 DMSQL 程序语句块中不能修改快速游标所涉及的表。这点需用户自己保证，否则可能导致结果不正确；
  * 不支持游标更新和删除；
  * 不支持 NEXT 以外的 FETCH 方向；
  * 不支持快速游标作为函数返回值；
  * MPP 环境下不支持对快速游标进行 FETCH 操作。



必须先定义一个游标，之后才能在别的语句中使用它。定义显式游标时指定游标名和与其关联的查询语句。可以指定游标的返回类型，也可以指定关联的查询语句中的 WHERE 子句使用的参数。

\<cursor 选项 > 中的 \< 查询表达式 > 可以带有 INTO 子句，但实际上会进行忽略，不会对变量进行赋值；且当解析 INTO 子句时，若变量不存在会进行报错。

举例说明：

例 如何使用不同语法定义各种显式游标。

```
DECLARE
	CURSOR c1 IS SELECT TITLE FROM RESOURCES.EMPLOYEE WHERE MANAGERID = 3;
	CURSOR c2 RETURN RESOURCES.EMPLOYEE%ROWTYPE IS SELECT * FROM
RESOURCES.EMPLOYEE;
	c3 CURSOR IS TABLE RESOURCES.EMPLOYEE;
……
```

■ 打开显式游标

语法如下：

```
OPEN <游标名>;
```

指定打开的游标必须是已定义的游标，此时系统执行这个游标所关联的查询语句，获得结果集，并将游标定位到结果集的第一行之前。

> **注意**
>
> 当再次打开一个已打开的游标时，游标会被重新初始化，游标属性数据可能会发生变化。



■ 拨动游标

将游标拨动到结果集的某个位置，获取数据。

语法如下：

```
FETCH [<fetch选项> [FROM]] <游标名> [ [BULK COLLECT] INTO <主变量名>{,<主变量名>} ] [LIMIT <rows>];
<fetch选项>::= NEXT|PRIOR|FIRST|LAST|ABSOLUTE n|RELATIVE n
```

被拨动的游标必须是已打开的游标。

fetch 选项指定将游标移动到结果集的某个位置：

  * NEXT：游标下移一行
  * PRIOR：游标前移一行
  * FIRST：游标移动到第一行
  * LAST：游标移动到最后一行
  * ABSOLUTE n：游标移动到第 n 行
  * RELATIVE n：游标移动到当前指示行后的第 n 行



FETCH 语句每次只获取一条记录，除非指定了“BULK COLLECT”。若不指定 FETCH 选项，则第一次执行 FETCH 语句时，游标下移，指向结果集的第一行，以后每执行一次 FETCH 语句，游标均顺序下移一行，使这一行成为当前行。

INTO 子句中的变量个数、类型必须与游标关联的查询语句中各 SELECT 项的个数、类型一一对应。典型的使用方式是在 LOOP 循环中使用 FETCH 语句将每一条记录数据赋给变量，并进行处理，使用 %FOUND 或 %NOTFOUND 来判断是否处理完数据并退出循环。如下例所示：

例 1 使用 %NOTFOUND 来判断是否处理完数据并退出循环

```
DECLARE 
	v_name VARCHAR(50);
    v_phone VARCHAR(50);
	c1 CURSOR FOR SELECT NAME,PHONE FROM PERSON.PERSON A,RESOURCES.EMPLOYEE B WHERE A.PERSONID=B.PERSONID;   
BEGIN
	OPEN c1;
	LOOP
	FETCH c1 INTO v_name,v_phone;
	EXIT WHEN c1%NOTFOUND; 
	PRINT v_name || v_phone; 
	END LOOP;
		CLOSE c1;
END;
/
```

使用 FETCH…BULK COLLECTINTO 可以将查询结果批量地、一次性地赋给集合变量。FETCH…BULK COLLECTINTO 和 LIMIT rows 配合使用，可以限制每次获取数据的行数。

例 2 FETCH…BULK COLLECT INTO 的使用方法

```
DECLARE
	TYPE V_rd IS RECORD(V_NAME VARCHAR(50),V_PHONE VARCHAR(50));
	TYPE V_type IS TABLE OF V_rd INDEX BY INT;
	v_info V_type;
	c1 CURSOR IS SELECT NAME,PHONE FROM PERSON.PERSON A,RESOURCES.EMPLOYEE B WHERE
A.PERSONID=B.PERSONID;
BEGIN
	OPEN c1;
	FETCH c1 BULK COLLECT INTO v_info;
	CLOSE c1;
	FOR I IN 1..v_info.COUNT LOOP
		PRINT v_info(I).V_NAME ||v_info(I).V_PHONE;
	END LOOP;
END;
/
```

BULK COLLECT 可以和 SELECT INTO、FETCH INTO、RETURNING INTO 一起使用，BULK COLLECT 之后 INTO 的变量必须是集合类型。针对 FETCH…BULK COLLECT INTO 语句，BULK COLLECT 之后 INTO 的变量不支持索引类型为 VARCHAR 的索引表。

■ 关闭游标

语法如下：

```
CLOSE <游标名>;
```

游标在使用完后应及时关闭，以释放它所占用的内存空间。当游标关闭后，不能再从游标中获取数据，否则将报错。如果需要，可以再次打开游标。

##### 5.2.1.2.2 显式游标的属性

5.2.1.1 节中介绍了隐式游标的属性，同样地，每一个显式游标也有 %FOUND、%NOTFOUND、%ISOPEN 和 %ROWCOUNT 四个属性，但这些属性的意义与隐式游标的有一些区别。

  * %FOUND：如果游标未打开，产生一个异常。否则，在第一次拨动游标之前，其值为 NULL。如果最近一次拨动游标时取到了数据，其值为 TRUE，否则为 FALSE。
  * %NOTFOUND：如果游标未打开，产生一个异常。否则，在第一次拨动游标之前，其值为 NULL。如果最近一次拨动游标时取到了数据，其值为 FALSE，否则为 TRUE。
  * %ISOPEN：游标打开时为 TRUE，否则为 FALSE。
  * %ROWCOUNT：如果游标未打开，产生一个异常。如游标已打开，在第一次拨动游标之前其值为 0，否则为最近一次拨动后已经取到的元组数。



下面的例子说明了显式游标属性的使用方法。

例 显式游标属性的使用方法。对于基表 EMPSALARY，输出表中的前 5 行数据。如果表中的数据不足 5 行，则输出表中的全部数据。

```
DECLARE
	CURSOR c1 FOR SELECT * FROM OTHER.EMPSALARY;
	my_ename CHAR(10);
	my_empno NUMERIC(4);
	my_sal NUMERIC(7,2);
BEGIN
	OPEN c1;
	LOOP
		FETCH c1 INTO my_ename, my_empno, my_sal;
		EXIT WHEN c1%NOTFOUND;  //当游标取不到数据时跳出循环 
		PRINT my_ename || ' ' || my_empno || ' ' || my_sal;
		EXIT WHEN c1%ROWCOUNT=5;  //已经输出了5行数据，跳出循环
	END LOOP;
	CLOSE c1;
END;
/
```

### 5.2.2 动态游标

与静态游标不同，动态游标在声明部分只是先声明一个游标类型的变量，并不指定其关联的查询语句，在执行部分打开游标时才指定查询语句。动态游标的使用主要在定义和打开时与显式游标不同，下面进行详细介绍。

  * **定义动态游标**



语法如下：

```
CURSOR <游标名>；
```

  * **打开动态游标**



打开动态游标的语法如下：

```
OPEN [WITH FAST]<游标名><for表达式>;
<for表达式>::=<for_item1>|<for_item2>
<for_item1>::= FOR <查询表达式>
<for_item2>::= FOR <表达式> [USING <绑定参数> {,<绑定参数>}]
```

动态游标在 OPEN 时通过 FOR 子句指定与其关联的查询语句。

WITH FAST：在打开游标时指定 FAST 属性。OPEN 语句中的 WITH FAST 必须在 DM.INI 参数 ENABLE_FAST_REFCURSOR=1 的情况下才能生效，否则只是语法支持并未生效。

例 1 使用动态游标输出员工的姓名、工号和薪水。

```
DECLARE

	my_ename CHAR(10);

	my_empno NUMERIC(4);

	my_sal NUMERIC(7,2);

	c1 CURSOR;

BEGIN

	OPEN C1 FOR SELECT * FROM OTHER.EMPSALARY;

	LOOP

		FETCH c1 INTO my_ename, my_empno, my_sal;

		EXIT WHEN c1%NOTFOUND;

		PRINT '姓名'||my_ename || '工号' || my_empno || ' 薪水' || my_sal;

	END LOOP;

	CLOSE c1;

END;

/
```

动态游标关联的查询语句还可以带有参数，参数以“?”指定，同时在打开游标语句中使用 USING 子句指定参数，且参数的个数和类型与语句中的“?”必须一一匹配。

例 2 使用关联的语句中带有参数的动态游标。

```
DECLARE

	str VARCHAR;

	CURSOR csr;

BEGIN

	OPEN csr FOR 'SELECT LOGINID FROM RESOURCES.EMPLOYEE WHERE TITLE =? OR TITLE =?'
USING '销售经理','总经理';

	LOOP

		FETCH csr INTO str;

		EXIT WHEN csr%NOTFOUND;

		PRINT str;

	END LOOP;

	CLOSE csr;

END;

/
```

### 5.2.3 游标变量

游标变量不是真正的游标对象，而是指向源游标对象的一个指针。游标变量和游标的关系就像变量与常量的关系。

游标变量继承所有源游标的属性。如果源游标已经打开，则此游标变量也已经打开，游标变量指向的位置和源游标也是完全一样的。

#### 5.2.3.1 使用游标变量的步骤

■ 定义游标变量

语法如下：

```
<游标变量名> CURSOR[= <源游标>]; |
<游标变量名> SYS_REFCURSOR [= <源游标>];
<源游标>：= <源游标名> | <游标表达式>
<游标表达式>：= CURSOR [FAST] (<查询表达式>)
```

\< 游标表达式 > 的 \< 查询表达式 > 中不允许带有 INTO 子句，否则会报错：不合理的 INTO。

■ 游标变量赋值

游标变量可以在两个阶段进行赋值。一是在定义时赋值；二是使用时在 \< 执行部分 > 对它赋值。

语法如下：

```
<游标变量名> = <源游标>;   

<源游标>：= <源游标名> | <游标表达式>
```

■ 打开游标

打开已赋值游标变量。

语法如下：

```
OPEN <游标变量名>;  
```

游标表达式会自动打开，不需要使用 OPEN 方法。

如果游标变量未赋值，那么在 \< 执行部分 > 打开游标变量的同时，必须为其动态关联一条查询语句。

打开未赋值游标变量。

语法如下：

```
OPEN [WITH FAST]<游标变量名><for表达式>;
<for表达式>::=<for_item1>|<for_item2>
<for_item1>::= FOR <查询表达式>
<for_item2>::= FOR <表达式> [USING <绑定参数> {,<绑定参数>}]
```

WITH FAST：在打开游标时指定 FAST 属性。OPEN 语句中的 WITH FAST 必须在 DM.INI 参数 ENABLE_FAST_REFCURSOR=1 的情况下才能生效，否则只是语法支持并未生效。

■ 拨动游标和关闭游标

拨动游标和关闭游标的方法和显式游标完全相同。请参考 5.2.1.2 节。

例 1  定义游标变量 c2 的同时为 c2 赋值。

```
DECLARE
	CURSOR c1 IS SELECT TITLE FROM RESOURCES.EMPLOYEE WHERE MANAGERID = 3;
	c2 CURSOR =c1;
begin
	open c2;
	close c2;
END;
```

例 2  定义游标变量 c2 时没有赋值，使用时在 BEGIN……END 的 \< 执行部分 > 中对 c2 进行赋值。

```
DECLARE
	CURSOR c1 IS SELECT TITLE FROM RESOURCES.EMPLOYEE WHERE MANAGERID = 3;
	c2 CURSOR;
BEGIN
	c2 =c1;
	open c2;
	close c2;
END;
```

例 3  定义游标变量 c1 时没有赋值，在打开 c1 时对其动态关联一条查询语句。

```
DECLARE
	c1 CURSOR;
BEGIN
	open c1 FOR SELECT TITLE FROM RESOURCES.EMPLOYEE WHERE MANAGERID = 3;
	close c1;
END;
```

例 4  使用游标表达式对 c1 进行赋值。

```
DECLARE
	c1 CURSOR;
	v_title VARCHAR(50);
BEGIN
	c1:= CURSOR (SELECT TITLE FROM RESOURCES.EMPLOYEE WHERE MANAGERID = 3);
	LOOP
	FETCH c1 INTO v_title;
	EXIT WHEN c1%NOTFOUND; 
	PRINT v_title; 
	END LOOP;
	close c1;
END;
```

例 5  打开游标的同时赋值。

```
DECLARE
	c1 CURSOR;
	v_title VARCHAR(50);
BEGIN
	OPEN c1 FOR SELECT TITLE FROM RESOURCES.EMPLOYEE WHERE MANAGERID = 3;
	LOOP
	FETCH c1 INTO v_title;
	EXIT WHEN c1%NOTFOUND; 
	PRINT v_title; 
	END LOOP;
	close c1;
END;
```

#### 5.2.3.2 游标变量的特性

与常规的游标相比，游标变量具有以下特性：

● 游标变量不局限于一个查询，可以为一个查询声明或者打开一个游标变量，然后对其结果集进行处理，之后又可以将这个游标变量为其它的查询打开；

● 可以对游标变量进行赋值；

● 可以像用一个变量一样在一个表达式中使用游标变量；

● 游标变量可以作为一个子程序的参数；

● 可以使用游标变量在 DMSQL 程序的不同子程序中传递结果集。同子程序中传递结果集。

例 1 使用游标变量在存储函数中传递结果集。C 为游标变量。

```
CREATE OR REPLACE FUNCTION F1(C OUT CURSOR) RETURN INT
AS
CNT INT := 0;
BEGIN
      	OPEN C; 
   	LOOP
       FETCH C;   
       EXIT WHEN C%NOTFOUND;  
       CNT := CNT + 1;   
   	END LOOP;   
    	CLOSE C;  
    	RETURN CNT;  
END;
```

### 5.2.4 引用游标

引用游标是一种 \<REF 类型名 > 类型的游标变量。引用游标实现了在程序间传递结果集的功能

■ 定义 REF CURSOR 类型

语法如下：

```
TYPE <REF类型名> IS REF CURSOR [RETURN <DMSQL数据类型>];
<DMSQL数据类型>请参考5.2.1.2 显式游标
```

参数说明：

\<REF 类型名 >：是游标类型的名字；

\<DMSQL 数据类型 >：是该游标类型返回的数据类型。

■ 声明引用游标变量

语法如下：

```
<引用游标变量名> <REF类型名>;
```

参数说明：

\< 引用游标变量名 >：为游标变量的名字；

\<REF 类型名 >：是上一步定义的游标类型名字。

■ 打开游标

打开引用游标，关联结果集。打开方法和游标变量完全相同，请参考 [5.2.3 游标变量](https://eco.dameng.com/document/dm/zh-cn/pm/dm8_sql-sql-statement.html#5.2.3%20%E6%B8%B8%E6%A0%87%E5%8F%98%E9%87%8F)。

■ 拨动游标和关闭游标

拨动游标和关闭游标的方法和显式游标完全相同。请参考 [5.2.1.2 显式游标](https://eco.dameng.com/document/dm/zh-cn/pm/dm8_sql-sql-statement.html#5.2.1.2%20%E6%98%BE%E5%BC%8F%E6%B8%B8%E6%A0%87)。

  * **举例说明：**



例  使用引用游标在子程序中传递结果集。

```
DECLARE
	TYPE Emptype IS REF CURSOR RETURN PERSON.PERSON%ROWTYPE;
	emp Emptype;

	PROCEDURE process_emp(emp_v IN Emptype) IS
	person PERSON.PERSON%ROWTYPE;
BEGIN
	LOOP
		FETCH emp_v INTO person;
		EXIT WHEN emp_v%NOTFOUND;
		DBMS_OUTPUT.PUT_LINE('姓名：'||person.NAME || ' 电话：' || person.PHONE);
		END LOOP;
	END;

BEGIN
	OPEN emp FOR SELECT A.* FROM PERSON.PERSON A,RESOURCES.EMPLOYEE B WHERE A.PERSONID=B.PERSONID;
	process_emp(emp);
	CLOSE emp;
END;
/
```

### 5.2.5 使用静态游标更新、删除数据

可以使用静态游标更新或删除结果集中的数据。若需要使用游标更新或删除数据，则在游标关联的查询语句中一定要使用“FOR UPDATE 选项”。FOR UPDATE 选项出现在查询语句中，用于对要修改的行上锁，以防止用户在同一行上进行修改操作。关于 FOR UPDATE 选项的详细介绍可参考《DM8_SQL 语言使用手册》。

当游标拨动到需要更新或删除的行时，就可以使用 UPDATE/DELETE 语句进行数据更新/删除。此时必须在 UPDATE/DELETE 语句结尾使用“WHERE CURRENT OF 子句”，以限定删除/更新游标当前所指的行。

语法如下：

```
< WHERE CURRENT OF子句> ::=WHERE CURRENT OF <游标名>
```

举例说明：

例 1 使用游标更新表中的数据。

```
DECLARE
	CURSOR csr is SELECT SALARY FROM RESOURCES.EMPLOYEE WHERE TITLE='销售经理' FOR UPDATE;
BEGIN
	OPEN csr;
	IF csr%ISOPEN THEN
		FETCH csr;
		UPDATE RESOURCES.EMPLOYEE SET SALARY = SALARY + 10000 WHERE CURRENT OF csr;
	ELSE
		PRINT 'CURSOR IS NOT OPENED';
	END IF;
	CLOSE csr;
END;
/
```

例 2 使用游标删除表中的数据。

```
DECLARE
	CURSOR dcsr is SELECT EMPNO FROM OTHER.EMPSALARY WHERE ENAME='KING' FOR UPDATE;
BEGIN
	OPEN dcsr;
	IF dcsr%ISOPEN THEN
		FETCH dcsr;
		DELETE FROM OTHER.EMPSALARY WHERE CURRENT OF dcsr;
	ELSE
		PRINT 'CURSOR IS NOT OPENED';
	END IF;
	CLOSE dcsr;
END;
/
```

### 5.2.6 使用游标 FOR 循环

游标通常与循环联合使用，以遍历结果集数据。实际上，DMSQL 程序还提供了一种将两者综合在一起的语句，即游标 FOR 循环语句。游标 FOR 循环自动使用 FOR 循环依次读取结果集中的数据。当 FOR 循环开始时，游标会自动打开（不需要使用 OPEN 方法）；每循环一次系统自动读取游标当前行的数据（不需要使用 FETCH）；当数据遍历完毕退出 FOR 循环时，游标被自动关闭（不需要使用 CLOSE），大大降低了应用程序的复杂度。

#### 5.2.6.1 隐式游标 FOR 循环

语法如下：

```
FOR <cursor_record> IN (<查询语句>)
LOOP
<执行部分>
END LOOP;
```

参数说明：

\<cursor_record>：一个记录类型的变量。它是 DMSQL 程序根据 SQL%ROWTYPE 属性隐式声明出来的，不需要显式声明。也不能显式声明一个与\<cursor_record>同名的记录，会导致逻辑错误。

FOR 循环不断地将行数据读入变量\<cursor_record>中，在循环中也可以存取\<cursor_record>中的字段。

举例说明：

例 使用隐式游标 FOR 循环。

```
BEGIN
	FOR v_emp IN (SELECT * FROM RESOURCES.EMPLOYEE)
	LOOP
		DBMS_OUTPUT.PUT_LINE(V_EMP.TITLE || '的工资' || V_EMP.SALARY);
	END LOOP;
END;
/
```

#### 5.2.6.2 显式游标 FOR 循环

语法如下：

```
FOR <cursor_record> IN <游标名>
LOOP
<执行部分>
END LOOP;
```

显式游标 FOR 循环与隐式游标的语法和使用方式都非常相似，只是关键字“IN”后不指定查询语句而是指定显式游标名，\<cursor_record>则为\<游标名>%ROWTYPE 类型的变量。

例 使用显式的游标 FOR 循环。

```
DECLARE
	CURSOR cur_emp IS SELECT * FROM RESOURCES.EMPLOYEE;
BEGIN
	FOR V_EMP IN CUR_EMP LOOP
		DBMS_OUTPUT.PUT_LINE(V_EMP.TITLE || '的工资' || V_EMP.SALARY);
	END LOOP;
END;
/
```

## 5.3 动态 SQL

本章前面的小节中介绍的大都是 DMSQL 程序中的静态 SQL，静态 SQL 在 DMSQL 程序进行编译时是明确的 SQL 语句，处理的数据库对象也是明确的，这些语句在编译时就可进行语法和语义分析处理。与之对应，DMSQL 程序还支持动态 SQL，动态 SQL 指在 DMSQL 程序进行编译时是不确定的 SQL，编译时对动态 SQL 不进行处理，在 DMSQL 程序运行时才动态地生成并执行这些 SQL。

在应用中，常常需要根据用户的选择（如表名、列名、排序方式等）来生成 SQL 语句并执行，这些 SQL 不能在应用开发时确定，此时就需要使用动态 SQL。另外，在 DMSQL 程序中，DDL 语句只能通过动态 SQL 执行。

使用 EXECUTE IMMEDIATE 动态地准备和执行一条 SQL 语句。

语法如下：

```
EXECUTE IMMEDIATE <SQL动态语句文本> [USING <参数> {,<参数>}];
```

动态 SQL 的执行首先分析 SQL 动态语句文本，检查是否有错误，如果有错误则不执行，并在 SQLCODE 中返回错误码；如果没发现错误则继续执行。

> **注意**
>
> 下列语句不能作动态SQL语句：CLOSE、DECLARE、FETCH、OPEN、ALTER DATABASE。



动态 SQL 中可以使用参数，并支持两种指定参数的方式：用“?”表示参数和用“:variable”表示参数。

当用“?”表示参数时，在指定参数值时可以是任意的值，但参数值个数一定要与“?”的个数相同，同时数据类型一定要匹配（能够互相转换也可以），不然会报数据类型不匹配的错误。

举例说明：

```
CREATE OR REPLACE PROCEDURE proc(cate IN INT, time IN DATE) AS

DECLARE

	str_sql varchar := 'SELECT NAME,PUBLISHER from PRODUCTION.PRODUCT WHERE
PRODUCT_SUBCATEGORYID = ? AND PUBLISHTIME> ?';

BEGIN

	EXECUTE IMMEDIATE str_sql USING cate,time;

EXCEPTION

	WHEN OTHERS THEN PRINT 'error';

END;

/

CALL proc(4, '2001-01-01');
```

当用“:variable”表示参数时，若 SQL 动态语句文本为普通语句方式，则系统将其转化为“?”进行处理。若 SQL 动态语句文本为脚本方式（语句块方式或多条用分号分隔的 SQL 语句），则保留“:variable”参数形式。此时需要注意，若参数列表中有同名参数，系统将这些同名参数视为同一个参数，只需要对应传入一个参数值即可，否则会报“参数个数不匹配.error code = -3205”的错误。

例 1 下面的 DMSQL 程序中 sql_str 采用普通语句方式，其中的两个:x 被转化为?进行处理，程序执行成功。

```
DECLARE

	sql_str VARCHAR := 'UPDATE PRODUCTION.PRODUCT SET SELLSTARTTIME=:x,
SELLENDTIME=:x WHERE NAME=''红楼梦'';';

BEGIN

	EXECUTE IMMEDIATE SQL_STR USING '2015-01-01','2018-01-01';

END;

/
```

例 2 下面的 DMSQL 程序中 sql_str 写为脚本方式，则将两个:x 视为同名参数，因此在动态执行时指定两个实际参数会报错。

```
DECLARE

	sql_str VARCHAR := 'BEGIN UPDATE PRODUCTION.PRODUCT SET SELLSTARTTIME=:x,
SELLENDTIME=:x WHERE NAME=''红楼梦'';END;';

BEGIN

	EXECUTE IMMEDIATE SQL_STR USING '2015-01-01','2018-01-01';

END;

/

[-5402] 参数个数不匹配.
```

如果 SQL 动态语句文本为 SELECT 语句，且结果集只会是一条记录，可通过 INTO 语句来操作查询到的结果集，把查询到的结果存储到变量中。

语法如下：

```
EXECUTE IMMEDIATE <SQL动态语句文本> [INTO <变量>{,<变量>}][USING <参数>{,<参数>}];
```

举例说明：

```
DECLARE
	p_name VARCHAR(50);
	p_publish VARCHAR(50);
	str_sql VARCHAR := 'SELECT NAME,PUBLISHER from PRODUCTION.PRODUCT WHERE AUTHOR =?';
BEGIN
	EXECUTE IMMEDIATE str_sql INTO p_name,p_publish USING '曹雪芹,高鹗';
	PRINT p_name;
	PRINT p_publish;
EXCEPTION
	WHEN OTHERS THEN PRINT 'error';
END;
/
```

## 5.4 返回查询结果集

在存储过程或客户端 DMSQL 程序中执行了不带 INTO 子句的查询语句时，系统将在调用结束时将该查询结果集返回给调用者。当出现多个查询语句时，只有第一个查询语句的结果集被返回，如果想查看其他结果集，可在 DIsql 中输入命令“more”查看下一个结果集。

举例说明：

```
CREATE OR REPLACE PROCEDURE PRODUCTION.proc_result(p_publisher VARCHAR(50)) AS

BEGIN

	SELECT NAME,AUTHOR FROM PRODUCTION.PRODUCT WHERE PUBLISHER=p_publisher;

	SELECT NOWPRICE FROM PRODUCTION.PRODUCT WHERE PUBLISHER=p_publisher;

END;

/
```

调用 PRODUCTION.proc_result 过程，仅返回第一条查询语句的结果集：

```
SQL> CALL PRODUCTION.proc_result('中华书局');

行号 			NAME	 AUTHOR

---------- ------ --------------

1 			红楼梦曹雪芹,高鹗

2 			水浒传施耐庵，罗贯中
```

再调用 more 命令，返回第二条查询语句的结果集：

```
SQL> more

行号 			NOWPRICE

---------- --------

1 			15.2000

2 			14.3000
```

> **注意**
>
> 存储函数中不能包含返回结果集的查询语句。



## 5.5 自治事务

通过将一个 DMSQL 语句块定义成自治事务，可以将该块中的 DML 语句和调用程序的事务环境隔离开。如此一来，该语句块就成为一个由其他事务启动的独立的事务，前一个事务被称为主事务。在自治事务块中，主事务是挂起的；等待自治事务完成后，会话自动切换回主事务。

一个定义了自治事务的语句块称为自治例程。

### 5.5.1 定义自治事务

定义自治事务，需要在 DMSQL 程序的声明部分添加如下语法的语句。

语法如下：

```
PRAGMA AUTONOMOUS_TRANSACTION;
```

自治事务的定义语句可以放在 DMSQL 程序声明部分的任何地方，但推荐放在数据结构声明之前。

作为自治事务的 DMSQL 语句块可以是下面中的一种：

  * 最顶层的（不是嵌套的）匿名 DMSQL 语句块
  * 函数和过程，或者在一个包里定义或者是一个独立的程序
  * 对象类型的方法
  * 数据库触发器
  * 嵌套子过程



举例说明：

```
CREATE TABLE test(c VARCHAR);
	INSERT INTO test VALUES('主事务');
DECLARE
	PRAGMA AUTONOMOUS_TRANSACTION;
BEGIN
	INSERT INTO test VALUES('自治事务');
	COMMIT;  //一定要提交，否则会报错。详情请参考5.5.2节
END;
/
ROLLBACK;
```

查询表 test：

```
SELECT * FROM test;

行号			C

---------- --------

1 			自治事务
```

可以看到，表 test 中有记录“自治事务”，其所在自治事务提交，不受主事务回滚影响，而主事务插入的记录“主事务”被回滚。

### 5.5.2 自治事务完整性与死锁检测

自治事务的语句块执行完成时，如果事务仍处于活动状态，则系统需要报错：检测到活动的自治事务处理，并将未提交事务回滚，所以需要在语句块结束处显式添加 COMMIT 或 ROLLBACK 语句。

另一方面，COMMIT 和 ROLLBACK 语句只是结束了活动的自治事务，但不会终止自治例程。实际上，可以在一个自治块中使用多个 COMMIT、ROLLBACK 语句。

例

```
CREATE TABLE test (c VARCHAR);

	INSERT INTO test VALUES('主事务');

DECLARE

	PRAGMA AUTONOMOUS_TRANSACTION;

BEGIN

	INSERT INTO test VALUES('自治事务1');

	ROLLBACK;

	INSERT INTO test VALUES('自治事务2');

	COMMIT;

END;

/

ROLLBACK;
```

查询表 test：

```
SELECT * FROM test;

行号 			C

---------- ---------

1 			自治事务2
```

自治事务相对主事务是完全独立的。由于执行自治事务时主事务处于挂起状态，如果自治事务需要的锁资源已经被主事务拥有则会产生死锁报错。

### 5.5.3 自治事务嵌套

自治块中可以调用其它被定义自治事务的过程，从而产生嵌套的自治事务。

例

```
CREATE TABLE TEST (C VARCHAR);

CREATE OR REPLACE PROCEDURE PROC_AUTO1

IS

PRAGMA AUTONOMOUS_TRANSACTION;

BEGIN

	DELETE TEST WHERE C='嵌套事务';

	COMMIT;

	INSERT INTO TEST VALUES('自治嵌套事务');

	COMMIT;

END;

/

CREATE OR REPLACE PROCEDURE PROC_AUTO2

IS

declare

	PRAGMA AUTONOMOUS_TRANSACTION;

BEGIN

	CALL PROC_AUTO1;

	INSERT INTO TEST VALUES('自治语句块');

	COMMIT;

END;

/

INSERT INTO TEST VALUES('嵌套事务');

COMMIT;

INSERT INTO TEST VALUES('主事务');

CALL PROC_AUTO2;

ROLLBACK;
```

查询表 test：

```
SELECT * FROM test;

行号 			C

---------- ------------

1			自治嵌套事务

2			自治语句块
```

DM 的 INI 参数 TRANSACTIONS 指定一个会话中可以并发的自治事务数量，其默认值为 75。如果实际生成自治事务数超出该参数限定值时，服务器将报错：“并发事务数超出上限”。该参数理论值上下限分别为 1000 和 1。
