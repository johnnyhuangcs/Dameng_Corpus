

# DMSQL 程序的定义、调用与删除

本章主要介绍如何使用 DMSQL 程序语言来定义存储模块及客户端 DMSQL 程序。为使用户获得对 DMSQL 程序的整体印象，本节中提供了一些例子，对于例子中涉及到的各种控制语句，其详细的使用方法将在第五章进行介绍。

## 3.1 存储过程

语法如下：

```
CREATE [OR REPLACE] PROCEDURE [IF NOT EXISTS] <过程声明> <AS_OR_IS> <模块体>
<过程声明> ::= <存储过程名定义> [WITH ENCRYPTION][(<参数名><参数模式><参数类型> [<默认值表达式>]
{,<参数名><参数模式><参数类型> [<默认值表达式>] })][<调用权限子句>]
<存储过程名定义> ::=[<模式名>.]<存储过程名><AS_OR_IS>::= AS | IS
<模块体> ::= [<声明部分>]
			BEGIN
			<执行部分>
			[<异常处理部分>]
			END [存储过程名]
<声明部分> ::=[DECLARE]<声明定义>{<声明定义>}
<声明定义>::=<变量声明>
		|<异常变量声明>
		|<游标定义>
		|<子过程定义>
		|<子函数定义>;
<执行部分>::=<DMSQL程序语句序列>{;<DMSQL程序语句序列>}
<DMSQL程序语句序列> ::= [<标号说明>]<DMSQL程序语句>;
<标号说明>::=<<<标号名>>>
<DMSQL程序语句>::=<SQL语句>|<控制语句>
<异常处理部分>::=EXCEPTION<异常处理语句>{;<异常处理语句>}
```

参数说明：

  * \< 存储过程名>：指明被创建的存储过程的名字
  * \< 模式名>：指明被创建的存储过程所属模式的名字，缺省为当前模式名
  * \< 参数名>：指明存储过程参数的名称
  * \< 参数模式>：参数模式可设置为 IN、OUT 或 IN OUT（OUT IN），缺省为 IN 类型
  * \< 参数类型>：指明存储过程参数的数据类型
  * \< 声明部分>：由变量、游标和子程序等对象的声明构成，可缺省
  * \< 执行部分>：由 SQL 语句和过程控制语句构成的执行代码
  * \< 异常处理部分>：各种异常的处理程序，存储过程执行异常时调用，可缺省
  * \< 调用权限子句>：指定该过程中的 SQL 语句默认的模式



DBA 或具有 CREATE PROCEDURE 权限的用户可以使用上述语法新创建一个存储过程。OR REPLACE 选项的作用是当同名的存储过程存在时，首先将其删除，再创建新的存储过程。IF NOT EXISTS 选项的作用是当同名的存储过程存在时，忽略本次存储过程创建操作。当同时指定 OR REPLACE 和 IF NOT EXISTS 选项时，按照 OR REPLACE 选项的策略执行。

WITH ENCRYPTION 为可选项，如果指定 WITH ENCRYPTION 选项，则对存储过程名之后的语句部分进行加密，防止非法用户查看其具体内容。加密后的存储过程的定义可在 SYS.SYSTEXTS 系统表中查询。

存储过程可以带有参数，这样在调用存储过程时就需指定相应的实际参数，如果没有参数，过程名后面的圆括号和参数列表就可以省略了。关于参数使用的具体介绍见 3.4 节。

声明部分进行变量声明，对其的具体介绍见 3.5 节。

可执行部分是存储过程的核心部分，由 SQL 语句和流控制语句构成。支持的 SQL 语句包括：

  * 数据查询语句(SELECT)
  * 数据操纵语句(INSERT、DELETE、UPDATE)
  * 游标定义及操纵语句(DECLARE CURSOR、OPEN、FETCH、CLOSE)
  * 事务控制语句(COMMIT、ROLLBACK)
  * 动态 SQL 执行语句(EXECUTE IMMEDIATE)



> **注意**
>
> SQL语句必须以分号结尾，否则语法分析报错。



异常处理部分用于处理存储过程在执行过程中可能出现的错误。

下面是一个定义存储过程的简单示例：

```
CREATE OR REPLACE PROCEDURE RESOURCES.proc_1(a IN OUT INT)AS
	b INT:=10;
BEGIN 
	a:=a+b;
	PRINT a;
EXCEPTION
	WHEN OTHERS THEN NULL;
END;
/
```

该例子在模式 RESOURCES 下创建了一个名为 proc_1 的存储过程。例子中第 2 行是该存储过程的说明部分，这里声明了一个变量 b。注意在 DMSQL 程序中说明变量时，变量的类型放在变量名称之后。第 4 行和第 5 行是该程序块运行时被执行的代码段，这里将 a 与 b 的和赋给参数 a。如果发生了异常，第 6 行开始的异常处理部分就对产生的异常情况进行处理。说明部分和异常处理部分都是可选的。如果用户在模块中不需要任何局部变量或者不想处理发生的异常，则可以省略这两部分。

## 3.2 存储函数

语法如下：

```
CREATE [OR REPLACE ] FUNCTION [IF NOT EXISTS] <函数声明> <AS_OR_IS> <模块体>
<函数声明> ::= <存储函数名定义> [WITH ENCRYPTION][FOR CALCULATE][(<参数名><参数模式><参数类型> [<默认值表达式>]{,<参数名><参数模式><参数类型>[<默认值表达式>]})]RETURN <返回数据类型> [<调用选项子句>][PIPELINED]
<存储函数名定义> ::=[<模式名>.]<存储函数名>
<调用选项子句> ::= <调用选项> {<调用选项>}
<调用选项> ::= <调用权限子句> | DETERMINISTIC
<AS_OR_IS>::= AS | IS
<模块体> ::= [<声明部分>]
			BEGIN
			<执行部分>
			[<异常处理部分>]
			END [存储函数名]
<声明部分> ::=[DECLARE]<声明定义>{<声明定义>}
<声明定义>::=<变量声明>
		|<异常变量声明>
		|<游标定义>
		|<子过程定义>
		|<子函数定义>;
<执行部分>::=<DMSQL程序语句序列>{;<DMSQL程序语句序列>}
<DMSQL程序语句序列> ::= [<标号说明>]<DMSQL程序语句>;
<标号说明>::=<<<标号名>>>
<DMSQL程序语句>::=<SQL语句>|<控制语句>
<异常处理部分>::=EXCEPTION<异常处理语句>{;<异常处理语句>}
```

参数说明：

  * \< 存储函数名>：指明被创建的存储函数的名字
  * \< 模式名>：指明被创建的存储函数所属模式的名字，缺省为当前模式名
  * \< 参数名>：指明存储函数参数的名称
  * \< 参数模式>：参数模式可设置为 IN、OUT 或 IN OUT（OUT IN），缺省为 IN 类型
  * \< 参数类型>：指明存储函数参数的数据类型
  * \< 返回数据类型>：指明存储函数返回值的数据类型
  * \< 调用权限子句>：指定该过程中的 SQL 语句默认的模式
  * PIPELINED：指明函数为管道表函数



存储函数与存储过程在结构和功能上十分相似，主要的差异在于：

  * 存储过程没有返回值，调用者只能通过访问 OUT 或 IN  
OUT 参数来获得执行结果，而存储函数有返回值，它把执行结果直接返回给调用者；

  * 存储过程中可以没有返回语句，而存储函数必须通过返回语句结束；
  * 不能在存储过程的返回语句中带表达式，而存储函数必须带表达式；
  * 存储过程不能出现在一个表达式中，而存储函数可以出现在表达式中。



FOR
CALCULATE 指定存储函数为计算函数。计算函数中不支持对表进行 INSERT、DELETE、UPDATE、SELECT、上锁、设置自增列属性；对游标 DECLARE、OPEN、FETCH、CLOSE；事务的 COMMIT、ROLLBACK、SAVEPOINT、设置事务的隔离级别和读写属性；动态 SQL 的执行 EXEC、创建 INDEX、创建子过程。对于计算函数体内的函数调用必须是系统函数或者计算函数。计算函数可以被指定为表列的缺省值。

DETERMINISTIC 指定存储函数为确定性函数。在调用其的语句中，对于相同的参数返回相同的结果。如果要将一个函数作为表达式在函数索引中使用，必须指定该函数为确定性函数。当系统遇到确定性函数，它将会试图重用之前的计算结果，而不是重新计算。在确定性函数实现中，虽然没有限制不确定元素（如随机函数等）和 SQL 语句的使用，但是不推荐使用这些可能会导致结果不确定的内容。

下面是一个定义存储函数的简单示例：

例 1 定义存储函数

```
CREATE OR REPLACE FUNCTION RESOURCES.fun_1(a INT, b INT) RETURN INT AS
	s  INT;
BEGIN
	s:=a+b;
	RETURN s;
EXCEPTION
	WHEN OTHERS THEN NULL;
END;
/
```

这个例子在模式 RESOURCES 下创建一个名为 fun_1 的存储函数。第 1 行说明了该函数的返回类型为 INT 类型。第 4 行将两个参数 a、b 的和赋给了变量 s，第 5 行的 RETURN 语句则将变量 s 的值作为函数的返回值返回。

调用这个存储函数：

```
SELECT RESOURCES.fun_1(1,2);
```

查询结果为 3。

例 2 创建一个计算函数 F1，并在表 T 中使用其定义列的缺省值。

```
CREATE OR REPLACE FUNCTION F1 FOR CALCULATE
	RETURN INT
	IS
BEGIN
	RETURN 1;
END;
/
//在表T中使用
CREATE TABLE T(C1 INT, C2 INT DEFAULT F1());
或者
CREATE TABLE T(C1 INT, C2 INT DEFAULT F1);
```

## 3.3 客户端 DMSQL 程序

客户端 DMSQL 程序不需要存储，创建后立即执行，执行完毕即被释放。

客户端 DMSQL 程序的定义语法与存储过程的定义语法类似。

语法如下：

```
[<声明部分>]

BEGIN

<执行部分>

[<异常处理部分>]

END
```

> **注意**
>
> 客户端DMSQL程序的声明部分必须包含DECLARE。



读者可能已经发现，本书之前章节的很多示例都是使用的客户端 DMSQL 程序，对于不需要反复执行的脚本，使用客户端 DMSQL 程序是一个比较合适的选择。

客户端 DMSQL 程序无法被其他程序调用，但它可以调用包括存储过程和存储函数等在内的其他函数。

举例如下：

```
DECLARE

	r INT:=0;

BEGIN

	SELECT RESOURCES.fun_1(1,2)*2 INTO r;

	CALL RESOURCES.proc_1(r);

EXCEPTION

	WHEN OTHERS THEN NULL;

END;

/
```

## 3.4 参数

存储模块及模块中定义的子模块都可以带参数，用来给模块传送数据及向外界返回数据。在存储过程或存储函数中定义一个参数时，必须说明名称、参数模式和数据类型。三种可能的参数模式是：IN(缺省模式)、OUT 和 IN OUT，意义分别为：

  * IN：输入参数，用来将数据传送给模块；
  * OUT：输出参数，用来从模块返回数据到进行调用的模块；
  * IN OUT：既作为输入参数，也作为输出参数。



在存储模块中使用参数时要注意下面几点：

  * 最多能定义不超过 1024 个参数；
  * IN 参数能被赋值；



下面的例子说明了不同模式的参数的使用方法。

例 1 在客户端 DMSQL 程序中定义一个子过程 raise_salary，其三个参数分别为 IN，IN OUT 和 OUT 类型。调用 raise_salary 为工号为 emp_num 的员工加薪 bonus 元，在 raise_salary 中将加薪后的薪水值赋给 IN OUT 参数 bonus，将员工职位赋给 OUT 参数 title。

```
DECLARE
	emp_num INT := 1;
	bonus DEC(19,4) := 6000;
	title VARCHAR(50);
	PROCEDURE raise_salary (emp_id IN INT, //输入参数
							amount IN OUT DEC(19,4), //输入输出参数
							emp_title OUT VARCHAR(50) //输出参数
							)
	IS
	BEGIN
		UPDATE RESOURCES.EMPLOYEE SET SALARY = SALARY + amount WHERE EMPLOYEEID =emp_id;
		SELECT TITLE,SALARY INTO emp_title,amount FROM RESOURCES.EMPLOYEE WHERE
EMPLOYEEID = emp_id;
	END raise_salary;

BEGIN
	raise_salary(emp_num, bonus, title);
	DBMS_OUTPUT.PUT_LINE
		('工号：'||emp_num||' '||'职位：'||title||''||'加薪后薪水：'||bonus);
END;
/
```

执行这个例子，将打印如下信息：

```
工号：1 职位：总经理加薪后薪水：46000.0000
```

例 2  使用赋值符号“:=”或关键字 DEFAULT，可以为 IN 参数指定一个缺省值。如果调用时未指定参数值，系统将自动使用该参数的缺省值。例如：

```
CREATE PROCEDURE proc_def_arg(a varchar(10) default 'abc', b INT:=123) AS

BEGIN

	PRINT a;

	PRINT b;

END;

/
```

调用过程 PROC_DEF_ARG，不指定输入参数值：

```
CALL proc_def_arg;
```

系统使用缺省值作为参数值，打印结果为：

```
abc

123
```

也可以只指定第一个参数，省略后面的参数：

```
CALL proc_def_arg('我们');
```

系统对后面的参数使用缺省值，打印结果为：

```
我们

123
```

## 3.5 变量

变量的声明应在声明部分。

语法如下：

```
<变量名>{,<变量名>}[CONSTANT]<变量类型>[NOT NULL][<缺省值定义符><表达式>]

<缺省值定义符> ::= DEFAULT | ASSIGN | :=
```

声明一个变量需要给这个变量指定名字及数据类型。

变量名必须以字母或下划线、$、#符号开头，包含数字、字母、下划线以及$、#符号，长度不能超过 128 字符，并且不能与 DM 的 DMSQL 程序保留字相同，变量名与大小写是无关的。

变量的数据类型可以是基本的 SQL 数据类型，也可以是 DMSQL 程序数据类型，比如一个游标、异常等。

用赋值符号“:=”或关键字 DEFAULT、ASSIGN，可以在定义时为变量指定一个缺省值。

支持同时声明多个变量，此时仅支持同时为多个变量指定数据类型，例如“a,b,c int;”。

在 DMSQL 程序的执行部分可以对变量赋值，赋值语句有两种方式：

  * 直接赋值语句，语法如下：



```
<变量名>:=<表达式>

或

SET <变量名>=<表达式>
```

  * 通过 SQL SELECT INTO 或 FETCH INTO 给变量赋值，语法如下，具体说明见后续章节。



```
SELECT <表达式>{,<表达式>} [INTO <变量名>{,<变量名>}] FROMb <表引用>{,<表引用>} …;

或

FETCH [NEXT|PREV|FIRST|LAST|ABSOLUTE N|RELATIVE N]<游标名> [INTO<变量名>{,<变量名>}];
```

常量与变量相似，但常量的值在程序内部不能改变，常量的值在定义时赋予，它的声明方式与变量相似，但必须包含关键字 CONSTANT。

如果需要打印变量的值，则要调用 PRINT 语句或 DBMS_OUTPUT.PUT_LINE 语句，如果数据类型不一致，则系统会自动将它转换为 VARCHAR 类型输出。除了变量的声明外，变量的赋值、输出等操作都要放在 DMSQL 程序的可执行部分。

下面的例子说明了如何对变量进行定义与赋值。

```
DECLARE  //可以在这里赋值
	salary DEC(19,4);
	worked_time DEC(19,4) := 60;
	hourly_salary DEC(19,4) := 1055;
	bonus DEC(19,4) := 150;
	position VARCHAR(50);
	province VARCHAR(64);
	counter DEC(19,4) := 0;
	done BOOLEAN;
	valid_id BOOLEAN;
	emp_rec1 RESOURCES.EMPLOYEE%ROWTYPE;
	emp_rec2 RESOURCES.EMPLOYEE%ROWTYPE;
	TYPE meeting_type IS TABLE OF INT INDEX BY INT;
	meeting meeting_type;
BEGIN  //也可以在这里赋值
	salary := (worked_time * hourly_salary) + bonus;
	SELECT TITLE INTO position FROM RESOURCES.EMPLOYEE WHERE LOGINID='L3';
	province := 'ShangHai';
	province := UPPER('wuhan');
	done := (counter > 100);
	valid_id := TRUE;
	emp_rec1.employeeid := 1;
	emp_rec1.managerid := null;
	emp_rec1 := emp_rec2;
	meeting(5) := 20000 * 0.15;
	PRINT position||'来自'||province;
	PRINT ('加班工资'||salary);
END;
/
```

变量只在定义它的语句块(包括其下层的语句块)内可见，并且定义在下一层语句块中的变量可以屏蔽上一层的同名变量。当遇到一个变量名时，系统首先在当前语句块内查找变量的定义；如果没有找到，再向包含该语句块的上一层语句块中查找，如此直到最外层。举例如下：

```
DECLARE
	a INT :=5;
BEGIN
	DECLARE
		a VARCHAR(10); 	//此处定义的变量a与上一层中的变量a同名 
	BEGIN
		a:= 'ABCDEFG';
		PRINT a; 	//第一条打印语句
	END;
	PRINT a;	 //第二条打印语句 
END;
/
```

先定义了一个整型变量 a，然后又在其下层的语句块中定义了一个同名的字符型变量 a。由于在该语句块中，字符型变量 a 屏蔽了整型变量 a，所以第一条打印语句打印的是字符型变量 a 的值，而第二条打印语句打印的则是整型变量 a 的值。执行结果如下：

```
ABCDEFG

5
```

## 3.6 OR REPLACE 和 IF NOT EXISTS 选项

OR REPLACE 选项的作用是，如果系统中已经有同名的存储模块，服务器会删除原先的存储模块，再创建新的存储模块。成功使用 OR REPLACE 选项的前提条件是当前用户具有删除原存储模块的权限，如果没有删除权限，则创建失败。使用 OR REPLACE 选项重新定义存储模块后，原存储模块的授权信息全部保留。

IF NOT EXISTS 选项的作用是，如果系统中已经有同名的存储模块，则忽略本次存储模块创建操作。

若同时指定 OR REPLACE 和 IF NOT EXISTS 选项，则按照 OR REPLACE 选项的策略执行。

若均不指定 OR REPLACE 和 IF NOT EXISTS 选项，则当创建的存储模块与系统中已有的存储模块同名时，服务器会报错。

## 3.7 调用权限子句

调用权限子句用于解析存储模块中的 SQL 语句中没有指定所在模式的对象名，是否在当前模式下运行。DM8 提供两种策略：定义者权限和调用者权限，系统默认使用定义者权限。

语法如下：

```
AUTHID CURRENT_USER 	//sql语句在当前模式下执行
或
AUTHID DEFINER 		//sql语句在过程或函数所在的模式下执行
```

使用说明：

AUTHID CURRENT_USER：采用调用者权限，即 SQL 语句在当前模式下执行

AUTHID DEFINER：采用定义者权限，即 SQL 语句在过程或函数所在模式下执行

## 3.8 调用、重新编译与删除存储模块

### 3.8.1 调用存储模块

对存储过程的调用可通过 CALL 语句来完成，也可以什么也不加直接通过名字及相应的参数执行即可，两种方式没有区别。

对于存储函数，除了可以通过 CALL 语句和直接通过名字调用外，还可以通过 SELECT 语句来调用，且执行方式存在一些区别：

  * 通过 CALL 和直接使用名字调用存储函数时，不会返回函数的返回值，仅执行其中的操作；
  * 通过 SELECT 语句调用存储函数时，不仅会执行其中的操作，还会返回函数的返回值。SELECT 调用的存储函数不支持含有 OUT、IN OUT 模式的参数。



举例如下：

```
CREATE OR REPLACE FUNCTION proc(A INT) RETURN INT AS

DECLARE

	s INT;

	lv INT;

	rv INT;

BEGIN

	IF A = 1 THEN

	s = 1;

	ELSIF A = 2 THEN

	s = 1;

	ELSE

		rv = proc(A - 1);

		lv = proc(A - 2);

	s = lv + rv;

		print lv || '+' || rv || '=' || s;

	END IF;

	RETURN S;

END;

/
```

例 1 通过 CALL 来调用函数 proc：

```
CALL proc(3);
```

执行结果如下：

```
1+1=2
```

例 2 使用 SELECT 来调用函数 proc：

```
SELECT proc(3);
```

则显示结果如下：

```
行号 			PROC(3)
---------- -----------
1 				2
```

可见，使用 CALL 调用存储函数会执行其中打印操作，但不返回结果集；而如果用 SELECT 调用的话就会输出返回值 2，这个相当于是结果集。

### 3.8.2 重新编译存储模块

存储模块中常常会访问或修改一些数据库表、索引等对象，而这些对象有可能已被修改甚至删除，这意味着对应的存储模块已经失效了。

若用户想确认一个存储模块是否还有效，可以重新编译该存储模块。

语法如下：

```
ALTER PROCEDURE|FUNCTION <存储模块名定义> COMPILE [DEBUG];

<存储模块名定义> ::=[ <模式名>.]<存储模块名>
```

当指定 CASCADE 后，将级联重编译所有直接或间接引用该存储模块的对象，需要考量影响范围，建议谨慎使用。

语法中的“DEBUG”没有实际作用，仅语法支持。

> **注意**
>
> 系统存储模块不能进行重新编译。



例 下面的语句对存储过程 RESOURCES.person_account 进行重新编译。

```
ALTER PROCEDURE RESOURCES.person_account COMPILE;
```

### 3.8.3 删除存储模块

当用户需要从数据库中删除一个存储模块时，可以使用存储模块删除语句。

语法如下：

```
DROP PROCEDURE [IF EXISTS] <存储过程名定义>;

<存储过程名定义> ::= [<模式名>.]<存储过程名>

或

DROP FUNCTION [IF EXISTS] <存储函数名定义>;

<存储函数名定义> ::= [<模式名>.]<存储函数名>
```

当模式名缺省时，默认为删除当前模式下的存储模块，否则，应指明存储模块所属的模式。除了 DBA 用户外，其他用户只能删除自己模式下的存储模块。

指定 IF EXISTS 关键字后，删除不存在的存储过程或者存储函数时不会报错，否则会报错。

例  下面的语句删除之前创建的存储过程 RESOURCES.proc_1 和存储函数 RESOURCES.fun_1;

```
DROP PROCEDURE RESOURCES.proc_1;

DROP FUNCTION RESOURCES.fun_1;
```
