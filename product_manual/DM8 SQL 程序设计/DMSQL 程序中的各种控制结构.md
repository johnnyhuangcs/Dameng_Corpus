

# DMSQL 程序中的各种控制结构

DMSQL 程序提供了丰富的控制结构，包括分支结构、循环控制结构、顺序结构等。通过控制结构，可以编写更复杂的 DMSQL 程序。

## 4.1 语句块

语句块是 DMSQL 程序的基本单元。每个语句块由关键字 DECLARE、BEGIN、EXCEPTION 和 END 划分为声明部分、执行部分和异常处理部分。其中执行部分是必须的，说明和异常处理部分可以省略。您可能已经发现，事实上存储模块的模块体就是一个语句块。语句块可以嵌套，它可以出现在任何其他语句可以出现的位置。

语法如下：

```
[DECLARE <变量说明>{,<变量说明>};]

BEGIN

<执行部分>

[<异常处理部分>]

END
```

  * **声明部分**



声明部分包含了变量和常量的数据类型和初始值。这个部分由关键字 DECLARE 开始。如果不需要声明变量或常量，那么可以忽略这一部分。游标的声明也放在这一部分。

  * **执行部分**



执行部分是语句块中的指令部分，由关键字 BEGIN 开始，以关键字 EXCEPTION 结束，如果 EXCEPTION 不存在，那么将以关键字 END 结束。所有的可执行语句都放在这一部分，其他的语句块也可以放在这一部分。分号分隔每一条语句，使用赋值操作符:=或 SELECT INTO 或 FETCH INTO 给变量赋值，执行部分的错误将在异常处理部分解决，在执行部分中可以使用另一个语句块，这种程序块被称为嵌套块。

所有的 SQL 数据操作语句都可以用于执行部分；执行部分使用的变量和常量必须首先在声明部分声明；执行部分必须至少包括一条可执行语句，NULL 是一条合法的可执行语句；事务控制语句 COMMIT 和 ROLLBACK 可以在执行部分使用。数据定义语言(Data Definition Language,简称 DDL)不能在执行部分中使用，DDL 语句与 EXECUTE IMMEDIATE 一起使用。

  * **异常处理部分**



异常处理部分是可选的，在这一部分中处理异常或错误，对异常处理的详细讨论我们在后面进行。

需要强调的一点是，一个语句块意味着一个作用域范围。也就是说，在一个语句块的声明部分定义的任何对象，其作用域就是该语句块。请看下面的例子，该例中有一个全局变量 X，同时子语句块中又定义了一个局部变量 X。

```
CREATE OR REPLACE PROCEDURE PROC_BLOCK AS
	X INT := 0;
	COUNTER INT := 0;
BEGIN
	FOR I IN 1 .. 4 LOOP
		X := X + 1000;
		COUNTER := COUNTER + 1;
		PRINT CAST(X AS CHAR(10)) || CAST(COUNTER AS CHAR(10)) || 'OUTER LOOP';

//这里是一个嵌套的子语句块的开始 */
DECLARE
	X INT := 0;  //局部变量X，与全局变量X同名
BEGIN
	FOR I IN 1 .. 4 LOOP
		X := X + 1;
		COUNTER := COUNTER + 1;
		PRINT CAST(X AS CHAR(10)) || CAST(COUNTER AS CHAR(10)) || 'INNER LOOP';
END LOOP;
END;
//子语句块结束 
	END LOOP;
END;
```

执行这个存储过程：

```
CALL PROC_BLOCK;
```

执行结果如下：

```
1000 1 OUTER LOOP
1 2 INNER LOOP
2 3 INNER LOOP
3 4 INNER LOOP
4 5 INNER LOOP
2000 6 OUTER LOOP
1 7 INNER LOOP
2 8 INNER LOOP
3 9 INNER LOOP
4 10 INNER LOOP
3000 11 OUTER LOOP
1 12 INNER LOOP
2 13 INNER LOOP
3 14 INNER LOOP
4 15 INNER LOOP
4000 16 OUTER LOOP
1 17 INNER LOOP
2 18 INNER LOOP
3 19 INNER LOOP
4 20 INNER LOOP
```

由执行结果可以看出，两个 X 的作用域是完全不同的。

## 4.2 分支结构

分支结构先执行一个判断条件，根据判断条件的执行结果执行对应的一系列语句。

### 4.2.1 IF 语句

IF 语句控制执行基于布尔条件的语句序列，以实现条件分支控制结构。

语法如下：

```
IF <条件表达式>

THEN <执行部分>;

[<ELSEIF_OR_ELSIF><条件表达式> THEN <执行部分>;{<ELSEIF_OR_ELSIF><条件表达式> THEN <执行部分>;}]

[ELSE <执行部分>;]

END IF;

<ELSEIF_OR_ELSIF> ::= ELSEIF | ELSIF
```

考虑到不同用户的编程习惯，ELSEIF 子句的起始关键字既可写作 ELSEIF，也可写作 ELSIF。

条件表达式中的因子可以是布尔类型的参数、变量，也可以是条件谓词。存储模块的控制语句中支持的条件谓词有：比较谓词、BETWEEN、IN、LIKE 和 IS NULL。

最简单的 IF 语句形如：

```
IF 条件 THEN

代码

END IF;
```

如果条件成立，则执行代码，否则什么也不执行。

如果需要在条件不成立时执行另外的代码，则应使用 IF-ELSE，形如：

```
IF 条件 THEN

代码1

ELSE

代码2

END IF;
```

此时，若条件成立则执行代码 1，条件不成立则执行代码 2。

上述语法也支持在由 BEGIN 与 END 关键字进行定界的语句块中执行，特别地，同时支持将 IF 语句作为语句块单独执行。而且形如 THEN 与 ENDIF 关键字，支持 IF 语句内部使用 BEGIN 与 END 关键字对代码块进行定界。BEGIN 与 END 关键字在使用时是作为语句或语句块的开始与结束标志出现的，类似 C 风格编码中的{}。当 IF 语句作为语句块进行单独执行时，末尾分号可以省略。

语法如下：

```
IF <条件表达式>
BEGIN <执行部分>; END
[<ELSEIF_OR_ELSIF><条件表达式> BEGIN <执行部分>; END {<ELSEIF_OR_ELSIF><条件表达式> BEGIN <执行部分>; END}]
[ELSE BEGIN <执行部分>; END]

<ELSEIF_OR_ELSIF> ::= ELSEIF | ELSIF
```

举例说明：

例 1 下面例子的子过程 proc_if 中使用 IF 语句判断销售指标完成情况，从而计算 bonus 值。

```
DECLARE

	PROCEDURE proc_if (

		sales dec,

		quota dec,

		emp_id dec

	)

	IS

		bonus dec:= 0;

	BEGIN

		IF sales > (quota + 400) THEN

			bonus := (sales - quota)/4;

		ELSE

			bonus := 100;

		END IF;

		DBMS_OUTPUT.PUT_LINE('bonus = ' || bonus);

		UPDATE RESOURCES.EMPLOYEE SET SALARY = SALARY + bonus WHERE EMPLOYEEID = emp_id;

	END proc_if ;

BEGIN

	proc_if (30100, 20000, 1);

	proc_if (15000, 10000, 2);

END;

/
```

执行结果如下：

```
bonus = 2525

bonus = 1250
```

还可以通过 ELSEIF 或 ELSIF 进行 IF 语句的嵌套，形如：

```
IF 条件1 THEN

代码1

ELSEIF 条件2 THEN

代码2

…

ELSE

代码N

END IF
```

在执行上面的 IF 语句时，首先判断条件 1，当条件 1 成立时执行代码 1，否则继续判断条件 2，条件成立则执行代码 2，否则继续判断下面的条件。如果前面的条件都不成立，则执行 ELSE 后面的代码 N。

例 2 在前面的例子中再增加一种计算 bonus 的情况：

```
DECLARE

	PROCEDURE proc_if (

		sales dec,

		quota dec,

		emp_id dec

	)

	IS

		bonus dec:= 0;

BEGIN

	IF sales > (quota + 400) THEN

		bonus := (sales - quota)/4;

	ELSE IF sales > quota THEN

		bonus := 100;

	ELSE

		bonus :=0;

		END IF;

	END IF;

	DBMS_OUTPUT.PUT_LINE('bonus = ' || bonus);

	UPDATE RESOURCES.EMPLOYEE SET SALARY = SALARY + bonus WHERE EMPLOYEEID = emp_id;

	END proc_if ;

BEGIN

	proc_if (30100, 20000, 1);

	proc_if (15000, 10000, 2);

	proc_if (9000, 10000, 3);

END;

/
```

执行结果如下：

```
bonus = 2525

bonus = 1250

bonus = 0
```

例 3 将例 2 更改为使用 BEGIN 与 END 关键字进行定界的方式计算 bonus：

```
DECLARE
	PROCEDURE proc_if (
	sales  dec,
	quota  dec,
	emp_id dec
)
IS
	bonus  dec:= 0;
BEGIN
	IF sales > (quota + 400) BEGIN
		bonus := (sales - quota)/4; END
	ELSEIF sales > quota BEGIN
		bonus := 100; END
	ELSE BEGIN
		bonus :=0; 
		END;
	DBMS_OUTPUT.PUT_LINE('bonus = ' || bonus);
	UPDATE RESOURCES.EMPLOYEE SET SALARY = SALARY + bonus     WHERE EMPLOYEEID = emp_id;
	END proc_if ;
BEGIN
	proc_if (30100, 20000, 1);
	proc_if (15000, 10000, 2);
	proc_if (9000, 10000, 3);
END;
/
```

执行结果如下：

```
bonus = 2525
bonus = 1250
bonus = 0
```

### 4.2.2 CASE 语句

CASE 语句从系列条件中进行选择，并且执行相应的语句块，主要有下面两种形式：

  * **简单形式：将一个表达式与多个值进行比较**



语法如下：

```
CASE <条件表达式>
WHEN <条件> THEN <执行部分>
{WHEN <条件> THEN <执行部分>}
[ ELSE <执行部分> ]
END [CASE];
```

语句中的每个条件可以是立即值，也可以是一个表达式。这种形式的 CASE 语句会选择第一个满足条件的对应的执行部分来执行，剩下的则不会计算，如果没有符合的条件，它会执行 ELSE 子句中的执行部分，但是如果 ELSE 子句不存在，则不会执行任何语句。

举例说明：

```
CREATE OR REPLACE PROCEDURE PROC_CASE(GRADE CHAR(10)) AS

DECLARE

	appraisal VARCHAR2(20);

BEGIN

	appraisal :=

	CASE GRADE

		WHEN NULL THEN 'IS NULL'

		WHEN 'A' THEN 'Excellent'

		WHEN 'B' THEN 'Good'

		WHEN 'C' THEN 'Fair'

		ELSE 'No such grade'

	END;

	DBMS_OUTPUT.PUT_LINE ('Grade ' ||grade|| ' is ' ||appraisal);

END;

/
```

  * **搜索形式：对多个条件进行计算，取第一个结果为真的条件**



语法如下：

```
CASE
WHEN <条件表达式> THEN <执行部分>
{ WHEN <条件表达式> THEN <执行部分>}
[ ELSE <执行部分> ]
END [CASE];
```

搜索模式的 CASE 语句依次执行各条件表达式，当遇见第一个为真的条件时，执行其对应的执行部分，在第一个为真的条件后面的所有条件都不会再执行。如果所有的条件都不为真，则执行 ELSE 子句中的执行部分，如果 ELSE 子句不存在，则不执行任何语句。

举例说明：

```
CREATE OR REPLACE PROCEDURE PROC_CASE(GRADE CHAR(10)) AS

DECLARE

	appraisal VARCHAR2(20);

BEGIN

	appraisal :=

	CASE

		WHEN grade IS NULL THEN 'IS NULL'

		WHEN grade = 'A' THEN 'Excellent'

		WHEN grade = 'B' THEN 'Good'

		WHEN grade = 'C' THEN 'Fair'

		ELSE 'No such grade'

	END;

	DBMS_OUTPUT.PUT_LINE ('Grade ' ||grade|| ' is ' ||appraisal);

END;

/
```

### 4.2.3 SWITCH 语句

DMSQL 程序支持 C 语法风格的 SWITCH 分支结构语句，关于 C 语法 DMSQL 程序介绍请看 7.1 节。

SWITCH 语句的功能与简单形式的 CASE 语句类似，用于将一个表达式与多个值进行比较，并执行相应的语句块。

语法如下：

```
SWITCH (<条件表达式>)

{

CASE <常量表达式> : <执行部分>; BREAK;

{ CASE <常量表达式> : <执行部分>; BREAK;}

[DEFAULT : <执行部分>; ]

}
```

每个 CASE 分支的执行部分后应有“BREAK”语句，否则 SWITCH 语句在执行了对应分支的执行部分后会继续执行后续分支的执行部分。

如果没有符合的 CASE 分支，会执行 DEFAULT 子句中的执行部分，如果 DEFAULT 子句不存在，则不会执行任何语句。

举例说明：

```
{

	VARCHAR appraisal='B';

	SWITCH (appraisal)

	{

		CASE NULL: PRINT 'IS NULL'; BREAK;

		CASE 'A': PRINT 'Excellent'; BREAK;

		CASE 'B': PRINT 'Good'; BREAK;

		CASE 'C': PRINT 'Fair'; BREAK;

		DEFAULT: PRINT 'No such grade';

	}

}

/
```

## 4.3 循环控制结构

DMSQL 程序支持五种类型的循环语句：LOOP 语句、WHILE 语句、FOR 语句、REPEAT 语句和 FORALL 语句。其中前四种为基本类型的循环语句：LOOP 语句循环重复执行一系列语句，直到 EXIT 语句终止循环为止；WHILE 语句循环检测一个条件表达式，当表达式的值为 TRUE 时就执行循环体的语句序列；FOR 语句对一系列的语句重复执行指定次数的循环；REPEAT 语句重复执行一系列语句直至达到条件表达式的限制要求。FORALL 语句对一条 DML 语句执行多次，当 DML 语句中使用数组或嵌套表时可进行优化处理，能大幅提升性能。

### 4.3.1 LOOP 语句

语法如下：

```
LOOP

<执行部分>;

END LOOP [标号名];
```

LOOP 语句实现对一语句系列的重复执行，是循环语句的最简单形式。LOOP 和 END LOOP 之间的执行部分将无限次地执行，必须借助 EXIT、GOTO 或 RAISE 语句来跳出循环。

举例说明：

例  在 LOOP 循环中打印参数 A 的值，并将 A 的值减 1，直到 A 小于等于 0 时跳出循环。

```
CREATE OR REPLACE PROCEDURE PROC_LOOP(a IN OUT INT) AS

BEGIN

	LOOP

		IF a<=0 THEN

			EXIT;

		END IF;

		PRINT a;

		a:=a-1;

	END LOOP;

END;

/

CALL PROC_LOOP(5);
```

### 4.3.2 WHILE 语句

语法如下：

```
WHILE <条件表达式> LOOP

<执行部分>;

END LOOP [标号名];
```

WHILE 循环语句在每次循环开始之前，先计算条件表达式，若该条件为 TRUE，执行部分被执行一次，然后控制重新回到循环顶部。若条件表达式的值为 FALSE，则结束循环。当然，也可以通过 EXIT 语句来终止循环。

举例说明：

例  使用 WHILE 语句实现上一小节中 LOOP 循环相同的功能。

```
CREATE OR REPLACE PROCEDURE PROC_WHILE(a IN OUT INT) AS

BEGIN

	WHILE a>0 LOOP

		PRINT a;

		a:=a-1;

	END LOOP;

END;

/

CALL PROC_WHILE(5);
```

### 4.3.3 FOR 语句

语法如下：

```
FOR <循环计数器> IN [REVERSE] <下限表达式> .. <上限表达式> LOOP

<执行部分>;

END LOOP [标号名];
```

循环计数器是一个标识符，它类似于一个变量，但是不能被赋值，且作用域限于 FOR 语句内部。下限表达式和上限表达式用来确定循环的范围，它们的类型必须和整型兼容。循环次数是在循环开始之前确定的，即使在循环过程中下限表达式或上限表达式的值发生了改变，也不会引起循环次数的变化。

执行 FOR 语句时，首先检查下限表达式的值是否小于上限表达式的值，如果下限数值大于上限数值，则不执行循环体。否则，将下限数值赋给循环计数器(语句中使用了 REVERSE 关键字时，则把上限数值赋给循环计数器)；然后执行循环体内的语句序列；执行完后，循环计数器值加 1(如果有 REVERSE 关键字，则减 1)；检查循环计数器的值，若仍在循环范围内，则重新继续执行循环体；如此循环，直到循环计数器的值超出循环范围。同样，也可以通过 EXIT 语句来终止循环。

举例说明：

例  使用 FOR 语句实现前面 LOOP 语句和 WHILE 语句例子相同的功能。

```
CREATE OR REPLACE PROCEDURE PROC_FOR1 (a IN OUT INT) AS

BEGIN

	FOR I IN REVERSE 1 .. a LOOP

		PRINT I;

		a:=I-1;

	END LOOP;

END;

/

CALL PROC_FOR1(5);
```

FOR 语句中的循环计数器可与当前语句块内的参数或变量同名，这时该同名的参数或变量在 FOR 语句的范围内将被屏蔽。如下例所示：

例 2 同名变量 v1 在 FOR 语句的范围内被屏蔽

```
DECLARE

	v1 DATE:=DATE '2017-03-17';

BEGIN

	FOR v1 IN 3.. 5 LOOP

		PRINT v1;

	END LOOP;

	PRINT v1;

END;

/
```

打印结果如下：

```
3

4

5

2017-03-17
```

### 4.3.4 REPEAT 语句

语法如下：

```
REPEAT

<执行部分>;

UNTIL <条件表达式>;
```

REPEAT 语句先执行执行部分，然后判断条件表达式，若为 TRUE 则控制重新回到循环顶部，若为 FALSE 则退出循环。可以看出，REPEAT 语句的执行部分至少会执行一次。

举例说明：

```
DECLARE

	a INT;

BEGIN

	a := 0;

	REPEAT

		a := a+1;

		PRINT a;

	UNTIL a>10;

END;

/
```

### 4.3.5 FORALL 语句

语法如下：

```
FORALL<循环计数器> IN <bounds_clause> [SAVE EXCEPTIONS] <forall_dml_stmt>;
<bounds_clause> ::= <下限表达式>..<上限表达式>
	| INDICES OF <集合> [BETWEEN ] <下限表达式> AND <上限表达式>
	| VALUES OF <集合>
<forall_dml_stmt> ::= <INSERT语句> 
				| <UPDATE语句> 
				| <DELETE语句> 
				|<MERGE INTO语句>
```

参数详解：

SAVE EXCEPTIONS：指定即使一些 DML 语句失败，直到 FORALL 执行结束才抛出异常。

INDICES OF \< 集合>：跳过集合中没有赋值的元素，可用于指向稀疏数组的实际下标。

VALUES OF \< 集合>：把该集合中的值作为下标。

下面的例子说明了如何使用 FORALL 语句：

```
CREATE TABLE t1_forall(C1 INT, C2 VARCHAR(50));

CREATE OR REPLACE PROCEDURE p1_forall AS

BEGIN

	FORALL I IN 1..10

		INSERT INTO t1_forall SELECT TOP 1 EMPLOYEEID, TITLE FROM RESOURCES.EMPLOYEE;

END;

/
```

FORALL 语句具有特有的隐式游标属性：%BULK_ROWCOUNT、%BULK_EXCEPTIONS，下面做出介绍，其他共有的隐式游标属性的说明可以参见[5.2.1.1.2 隐式游标属性](#5.2.1.1.2 隐式游标属性)。

  * %BULK_ROWCOUNT：FORALL 语句所执行的每个 DML 语句影响的行数。注意：如果 SQL%BULK_ROWCOUNT(i)结果为 0，那么此时 %FOUND 为 FALSE，%NOTFOUND 为 TRUE；
  * %BULK_EXCEPTIONS：若 FORALL 语句中带有 SAVE EXCEPTIONS 子句，提供 FORALL 语句抛出的每条异常信息。



DM 可对 FORALL 中的 INSERT、UPDATE 和 DELETE 语句中的数组或嵌套表引用进行优化处理。需要注意的是，优化处理会影响游标的属性值，导致其不可使用。dm.ini 中的 USE_FORALL_ATTR 参数控制是否进行优化处理：值为 0 表示可以优化，不使用游标属性；值为 1 表示不优化，使用游标属性，默认值为 0。

### 4.3.6 EXIT 语句

EXIT 语句与循环语句一起使用，用于终止其所在循环语句的执行，将控制转移到该循环语句外的下一个语句继续执行。

语法如下：

```
EXIT [<标号名>] [WHEN <条件表达式>];
```

EXIT 语句必须出现在一个循环语句中，否则将报错。

当 EXIT 后面的标号名省略时，该语句将终止直接包含它的那条循环语句；当 EXIT 后面带有标号名时，该语句用于终止标号名所标识的那条循环语句。需要注意的是，该标号名所标识的语句必须是循环语句，并且 EXIT 语句必须出现在此循环语句中。当 EXIT 语句位于多重循环中时，可以用该功能来终止其中的任何一重循环。

例 1  EXIT 后没有带标号，其所在的那层循环被终止。

```
DECLARE

	a INT;

	b INT;

BEGIN

	a := 0;

	LOOP

		FOR b in 1 .. 2 LOOP

			PRINT '内层循环' ||b;

			EXIT WHEN a > 3;

		END LOOP;

		a := a + 2;

		PRINT '---外层循环' ||a;

		EXIT WHEN a> 5;

	END LOOP;

END;

/
```

执行结果如下：

```
内层循环1

内层循环2

---外层循环2

内层循环1

内层循环2

---外层循环4

内层循环1

---外层循环6
```

例 2 EXIT 后带有标号，则其终止了指定的那层循环。

```
DECLARE

	a INT;

	b INT;

BEGIN

	a := 0;

	<<flag1>>

	LOOP

		FOR b in 1 .. 2 LOOP

			PRINT '内层循环' ||b;

		EXIT flag1 WHEN a > 3;

		END LOOP;

		a := a + 2;

		PRINT '---外层循环' ||a;

		EXIT WHEN a> 5;

	END LOOP;

END;

/
```

执行结果如下：

```
内层循环1

内层循环2

---外层循环2

内层循环1

内层循环2

---外层循环4

内层循环1
```

当 WHEN 子句省略时，EXIT 语句无条件地终止该循环语句；否则，先计算 WHEN 子句中的条件表达式，当条件表达式的值为真时，终止该循环语句。如下例所示：

例 3 当 WHEN 子句省略时，EXIT 语句无条件地终止该循环语句

```
DECLARE

	a INT;

	b INT;

BEGIN

	a := 0;

	LOOP

		FOR b in 1 .. 2 LOOP

			PRINT '内层循环' ||b;

			EXIT;

		END LOOP;

		a := a + 2;

		PRINT '---外层循环' ||a;

		EXIT;

	END LOOP;

END;

/
```

执行结果为：

```
内层循环1

---外层循环2
```

### 4.3.7 CONTINUE 语句

CONTINUE 语句的作用是退出当前循环，并且将语句控制转移到这次循环的下一次循环迭代或者是一个指定标签的循环的开始位置并继续执行。

语法如下：

```
CONTINUE [标号名] [WHEN <条件表达式>];
```

若 CONTINUE 后没有跟 WHEN 子句，则无条件立即退出当前循环，并且将语句控制转移到这次循环的下一次循环迭代或者是一个指定标号名的循环的开始位置并继续执行。如下例所示：

例 1 CONTINUE 后没有跟 WHEN 子句，将语句控制转移到指定标号 flag1 位置后继续执行。

```
DECLARE
	x INT:= 0;
BEGIN
	<<flag1>> 	//CONTINUE跳出之后，回到这里
	FOR I IN 1..4 LOOP
		DBMS_OUTPUT.PUT_LINE ('循环内部，CONTINUE之前: x = ' || TO_CHAR(x));
		x := x + 1;
		CONTINUE flag1;
		DBMS_OUTPUT.PUT_LINE ('循环内部，CONTINUE之后: x = ' || TO_CHAR(x));
	END LOOP;
	DBMS_OUTPUT.PUT_LINE (' 循环外部: x = ' || TO_CHAR(x));
END;
/
```

执行结果如下：

```
循环内部，CONTINUE之前: x = 0

循环内部，CONTINUE之前: x = 1

循环内部，CONTINUE之前: x = 2

循环内部，CONTINUE之前: x = 3

循环外部: x = 4
```

若 CONTINUE 语句中包含 WHEN 子句，则当 WHEN 子句的条件表达式为 TURE 时才退出当前循环，将语句控制转移到下一次循环迭代或者是一个指定标号名的循环的开始位置并继续执行。当每次循环到达 CONTINUE-WHEN 时，都会对 WHEN 的条件进行计算，如果条件为 FALSE，则 CONTINUE-WHEN 语句不执行任何动作，为了防止出现死循环，应将 WHEN 条件设置为一个肯定可以为 TRUE 的表达式。

例 2  CONTINUE-WHEN 语句的使用。

```
DECLARE
	x INT:= 0;
BEGIN
	// CONTINUE跳出之后，回到这里
	FOR I IN 1..4 LOOP
		DBMS_OUTPUT.PUT_LINE ('循环内部，CONTINUE之前: x = ' || TO_CHAR(x));
		x := x + 1;
		CONTINUE WHEN x > 3;
		DBMS_OUTPUT.PUT_LINE ('循环内部，CONTINUE之后: x = ' || TO_CHAR(x));
	END LOOP;
	DBMS_OUTPUT.PUT_LINE (' 循环外部: x = ' || TO_CHAR(x));
END;
/
```

执行结果如下：

```
循环内部，CONTINUE之前: x = 0

循环内部，CONTINUE之后: x = 1

循环内部，CONTINUE之前: x = 1

循环内部，CONTINUE之后: x = 2

循环内部，CONTINUE之前: x = 2

循环内部，CONTINUE之后: x = 3

循环内部，CONTINUE之前: x = 3

循环外部: x = 4
```

## 4.4 顺序结构

### 4.4.1 GOTO 语句

GOTO 语句无条件地跳转到一个标号所在的位置。

语法如下：

```
GOTO <标号名>;
```

GOTO 语句将控制权交给带有标号的语句或语句块。标号的定义在一个语句块中必须是唯一的，且必须指向一条可执行语句或语句块。

为了保证 GOTO 语句的使用不会引起程序的混乱，我们对 GOTO 语句的使用有下列限制：

  * GOTO 语句不能跳入一个 IF 语句、CASE 语句、循环语句或下层语句块中；
  * GOTO 语句不能从一个异常处理器跳回当前块，但是可以跳转到包含当前块的上层语句块。



举例说明：

例 1  GOTO 语句的正确使用。

```
DECLARE

	v_name VARCHAR2(25);

	v_empid INT := 1;

BEGIN

	<<get_name>>

	SELECT NAME INTO v_name FROM RESOURCES.EMPLOYEE A,PERSON.PERSON B WHERE
A.PERSONID=B.PERSONID AND A.EMPLOYEEID=v_empid;

	BEGIN

		DBMS_OUTPUT.PUT_LINE(v_name);

		v_empid := v_empid + 1;

		IF v_empid <=5 THEN

			GOTO get_name;

		END IF;

	END;

END;
```

例 2 非法使用 GOTO 语句，企图跳入一个 IF 语句

```
BEGIN

...

	GOTO update_row; /* 错误，企图跳入一个IF语句 */

...

	IF valid THEN

...

		<<update_row>>

		UPDATE emp SET ...

	END IF;

END;

/
```

例 3 非法使用 GOTO 语句，企图跳入一个下层语块

```
BEGIN

...

	IF status = 'OBSOLETE' THEN

		GOTO delete_part; /* 错误，企图跳入一个下层语句块 */

	END IF;

...

	BEGIN

...

		<<delete_part>>

		DELETE FROM parts WHERE ...

	END;

END;

/
```

### 4.4.2 NULL 语句

语法如下：

```
NULL;
```

NULL 语句不做任何事情，只是用于保证语法的正确性，或增加程序的可读性。

例  NULL 语句说明程序已经考虑了其他的异常，只是并不做处理。

```
CREATE OR REPLACE FUNCTION func_null (

	a INT,

	b INT

) RETURN INT

AS

BEGIN

	RETURN (a/b);

EXCEPTION

	WHEN ZERO_DIVIDE THEN

		DBMS_OUTPUT.PUT_LINE('除0错误');

	WHEN OTHERS THEN

		NULL;

END;

/
```

## 4.5 其他语句

### 4.5.1 赋值语句

语法如下：

```
<赋值对象> := <值表达式>;

或

SET <赋值对象> = <值表达式>;
```

使用赋值语句可以给各种数据类型的对象赋值。被赋值的对象可以是变量，也可以是 OUT 参数或 IN OUT 参数。表达式的数据类型必须与赋值对象的数据类型兼容。

> **注意**
>
> 如果赋值对象和值表达式是类类型，赋值采用的是对象指向逻辑，赋值对象指向值表达式的对象，而不会创建新的对象。



### 4.5.2 调用语句

存储模块可以被别的存储模块或应用程序调用。同样，在存储模块中也可以调用其他存储模块。调用存储模块时，应给存储模块提供输入参数值，并获取存储模块的输出参数值。

语法如下：

```
[CALL] [<模式名>.]<存储模块名>[@dblink_name] [(<参数>{, <参数>})];

<参数> ::= <参数值>|<参数名=参数值>
```

使用说明：

  * 如果被调用的存储模块不属于当前模式，必须在语句中指明存储模块的模式名；
  * 参数的个数和类型必须与被调用的存储模块一致；
  * 存储模块的输入参数可以是嵌入式变量，也可以是值表达式；存储模块的输出参数必须是可赋值对象，如嵌入式变量；
  * “dblink_name”表示创建的 dblink 名字，如果添加了该选项，则表示调用远程实例的存储模块；
  * 在调用过程中，服务器将以存储模块创建者的模式和权限来执行过程中的语句。



一般情况下，用户调用存储模块时通过实际参数位置关系和形式参数相对应，这种方式被称为“位置标识法”。系统还支持另一种存储模块调用方式：每个参数中包含形式参数和实际参数，这样的方法被称为“名字标识法”。对结果而言，这两种调用方式是等价的，但在具体使用时存在一些差异，如表 4.1 所示。

表4.1 位置标识法与名字标识法的使用比较

|**位置标识法**|**名字标识法**|
|-----|-----|
|依赖于实际参数的名称|清楚地说明了实际参数和形式参数间的对应关系|
|给出的实际参数必须依照形式参数的次序|指定实际参数时可不按照形式参数的顺序|
|调用比较简洁，符合大多数第三代语言的使用习惯|需要更多的编码，调用时要需要指定形式参数和实际参数|
|使用参数缺省值时必须在参数列表的末尾|无论哪个参数拥有缺省值都不会对调用产生影响|
|维护的代价在于参数的定义次序发生了变化|维护的代价在于参数名的定义发生了变化|


例 1 分别使用位置标识法和名字标识法调用存储模块。

```
CREATE OR REPLACE PROCEDURE proc_call (a INT, b IN OUT INT) AS
	v1 INT:=a;
BEGIN
	b:=0;
	FOR C IN 1 .. v1 LOOP
		b:=b+c;
	END LOOP;
	print b;
END;
/
CALL proc_call(10,0);  //以位置标识法调用
CALL proc_call(b=>0,a=>10);  //以名字标识法调用
```

例 2  演示如何通过 DBLINK 调用远程实例的存储模块。

一，假设远程数据库（实例名 dmserver2，端口号 5237）已创建 DBLINK，名为 TEST_LINK。该远程数据库上存在存储过程 dm_get_next_val。

```
CREATE OR REPLACE PROCEDUREdm_get_next_val(a IN OUT INT) AS

BEGIN

	a := a + 1;

END;

/
```

二，在本地（实例名 dmserver，端口号 5236）通过 DBLINK 名字 TEST_LINK，调用上述远程过程：

```
DECLARE

	x INT;

BEGIN

	x := 1;

	dm_get_next_val@TEST_LINK(x);

	PRINT x;

END;

/
```

### 4.5.3 RETURN 语句

语法如下：

```
RETURN [<返回值>];
```

RETURN 语句结束 DMSQL 程序的执行，将控制返回给 DMSQL 程序的调用者。如果是从存储函数返回，则同时将函数的返回值提供给调用环境。

除管道表函数外，函数的执行必须以 RETURN 语句结束。确切地说，函数中应至少有一个被执行的返回语句。由于程序中有分支结构，在编译时很难保证其每条路径都有返回语句，因此 DM 在函数执行时才对其进行检查，如果某个分支缺少 RETURN，DM 会自动为其隐式增加一条 RETURN NULL 语句。

举例如下：

```
CREATE OR REPLACE FUNCTION func_return(a INT) RETURN VARCHAR(10) AS

BEGIN

	IF (a<0) THEN

		RETURN ' a<0';

	ELSE

		PRINT '没有RUTURN';

	END IF;

END;
```

例  当参数 a 大于或等于 0 时，函数 func_return 没有以 RETURN 语句结束。

```
SELECT func_return(2);
```

执行结果如下：

```
行号 			FUNC_RETURN(2)

---------- --------------

1 				NULL
```

### 4.5.4 PRINT 语句

语法如下：

```
PRINT <表达式>;
```

PRINT 语句用于从 DMSQL 程序中向客户端输出一个字符串，语句中的表达式可以是各种数据类型，系统自动将其转换为字符类型。

PRINT 语句便于用户调试 DMSQL 程序代码。当 DMSQL 程序的行为与预期不一致时，可以在其中加入 PRINT 语句来观察各个阶段的运行情况。

之前章节的示例中有很多已经使用了 PRINT 语句，这里不再另外举例。用户也可以使用 DM 系统包方法 DBMS_OUTPUT.PUT_LINE()将信息打印到客户端。

### 4.5.5 PIPE ROW 语句

语法如下：

```
PIPE ROW ( <值表达式> );
```

管道表函数是可以返回行集合的函数，用户可以像查询数据库表一样查询它。目前 DM 管道表函数的返回值类型暂时只支持 VARRAY 类型和嵌套表类型。

PIPE ROW 语句将返回一行到管道表函数的结果行集中。如果值表达式是类类型的表达式，会复制一个对象输入到管道函数的结果集中，保证将同一个对象多次输入到管道函数的结果集中时，后文的修改不会影响前面的输入。

下面是一个关于管道表函数的例子。

```
CREATE TYPE mytype AS OBJECT (

	COL1 INT,

	COL2 VARCHAR (64)

);

/

CREATE TYPE mytypelist AS TABLE OF mytype;

/

CREATE OR REPLACE FUNCTION func_piperow RETURN mytypelist PIPELINED

IS

	v_mytype mytype;

BEGIN

	FOR I IN 1 .. 5 LOOP

		v_mytype := mytype (I, 'Row ' || I);

		PIPE ROW (v_mytype);

	END LOOP;

EXCEPTION

	WHEN OTHERS THEN NULL;

END;

/
```

查询管道表函数：

```
SELECT * FROM TABLE (FUNC_piperow);
```

查询结果如下：

```
行号 			COL1 	COL2

---------- ----------- -----

1 			1 		Row 1

2 			2 		Row 2

3 			3 		Row 3

4 			4		Row 4

5 			5 		Row 5
```

如果管道表函数中没有进行 PIPE ROW 操作，则管道表函数的返回结果为一个没有元素数据的集合，而不是 NULL，即返回的是空集而不是空。
