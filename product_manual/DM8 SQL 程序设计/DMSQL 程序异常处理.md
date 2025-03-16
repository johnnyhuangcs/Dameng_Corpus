

# DMSQL 程序异常处理

DMSQL 程序在应用运行时不可避免地会发生一些错误，这些错误常常并不是 DMSQL 程序本身的设计和编码问题，而是由于应用运行时由于一些未预计的操作或硬件异常等导致产生超出 DMSQL 程序预计处理范围的数据等错误，我们将这些错误称为异常。应该在 DMSQL 程序中对异常进行处理，否则将造成应用的异常退出。

## 6.1 异常处理的优点

异常一般是在 DMSQL 程序执行发生错误时由服务器抛出，也可以在 DMSQL 块中由程序员在一定的条件下显式抛出。无论是哪种形式的异常，DMSQL 程序的执行都会被中止，程序控制转至 DMSQL 程序的异常处理部分。程序员可以在异常处理部分编写一段程序对异常进行处理，以避免 DMSQL 程序的异常退出。

异常被处理结束后，异常所处 DMSQL 块的执行便告结束。所以一旦发生异常，则在所处 DMSQL 块的可执行部分中，从发生异常的地方开始，后续的代码将不再执行。如果没有对异常进行任何处理，异常将被传递到调用者，由调用者统一处理。

## 6.2 预定义异常

为方便用户编程，DM 提供了一些预定义的异常，这些异常与常见的 DM 错误相对应，如表 6.1 所示。

表6.1 DM预定义异常

|**异常名**|**错误号**|**错误描述**|
|----|----|----|
|INVALID_CURSOR|-4535|无效的游标操作|
|ZERO_DIVIDE|-6103|除 0 错误|
|DUP_VAL_ON_INDEX|-6602|违反唯一性约束|
|TOO_MANY_ROWS|-7046|SELECT INTO 中包含多行数据|
|NO_DATA_FOUND|-7065|数据未找到|


此外，还有一个特殊的异常名 OTHERS，它处理所有没有明确列出的异常。OTHERS 对应的异常处理语句必须放在其他异常处理语句之后。

下面的例子中异常处理部分对指定的预定义异常进行处理。

```
DECLARE

	v_name VARCHAR(50);

	v_phone VARCHAR(50);

BEGIN

	SELECT NAME,PHONE INTO V_NAME,V_PHONE FROM PERSON.PERSON A,RESOURCES.EMPLOYEE B
WHERE A.PERSONID=B.PERSONID AND B.TITLE='销售代表';

	PRINT v_name||' '||v_phone;

EXCEPTION

	WHEN TOO_MANY_ROWS THEN

		PRINT 'SELECT INTO中包含多行数据';

END;

/
```

执行结果如下：

```
SELECT INTO中包含多行数据
```

## 6.3 用户自定义异常

除了 DM 预定义的异常外，用户还可以在 DMSQL 程序中自定义异常。程序员可以把一些特定的状态定义为异常，在一定的条件下抛出，然后利用 DMSQL 程序的异常机制进行处理。

DMSQL 程序支持两种自定义异常的方法。

  * **使用 EXCEPTION FOR 将异常变量与错误号绑定**



语法如下：

```
<异常变量名> EXCEPTION [FOR <错误号> [, <错误描述>]];
```

在 DMSQL 程序的声明部分使用该语法声明一个异常变量。其中，FOR 子句用来为异常变量绑定错误号(SQLCODE 值)及错误描述。\< 错误号>必须是-20000 到-30000 间的负数值，\<错误描述>则为字符串类型。如果未显式指定错误号，则系统在运行中在-10001 到-15000 区间中顺序为其绑定错误值。

举例如下：

```
CREATE OR REPLACE PROCEDURE proc_exception (v_personid INT)

IS

	v_name VARCHAR(50);

	v_email VARCHAR(50);

	e1 EXCEPTION FOR -20001, 'EMAIL为空';

BEGIN

	SELECT NAME,EMAIL INTO v_name,v_email FROM PERSON.PERSON WHERE
PERSONID=v_personid;

	IF v_email='' THEN

		RAISE e1;

	ELSE

		PRINT v_name || v_email;

	END IF;

EXCEPTION

	WHEN E1 THEN

		PRINT v_name ||' '||SQLCODE||' '||SQLERRM;

END;

/

CALL proc_exception(2);
```

执行结果如下：

```
王刚 -20001 EMAIL为空
```

  * **使用 EXCEPTION_INIT 将一个特定的错误号与程序中所声明的异常标示符关联起来**



语法如下：

```
<异常变量名> EXCEPTION;

PRAGMA EXCEPTION_INIT(<异常变量名>, <错误号>);
```

在 DMSQL 程序的声明部分使用这个语法先声明一个异常变量，再使用 EXCEPTION_INIT 将一个特定的错误号与这个异常变量关联起来。这样可以通过名字引用任意的 DM 服务器内部异常（DM 服务器内部异常可参考《DM8 程序员手册》附录 1），并且可以通过名字为异常编写一适当的异常处理。如果希望使用 RAISE 语句抛出一个用户自定义异常，则与异常关联的错误号必须是-20000 到-30000 之间的负数值。

举例说明：

例 1 使用 EXCEPTION_INIT 将 DM 服务器的特定错误“-2206：缺少参数值”与异常变量 e1 关连起来。

```
CREATE OR REPLACE PROCEDURE proc_exception2(v_personid INT)
IS
	TYPE Rec_type IS RECORD(V_NAME VARCHAR(50),V_EMAIL VARCHAR(50));
	v_col Rec_type;
	e1 EXCEPTION;
	PRAGMA EXCEPTION_INIT(e1, -2206);
BEGIN
	SELECT NAME,EMAIL INTO v_col FROM PERSON.PERSON WHERE PERSONID=v_personid;
	IF v_col.V_EMAIL=''THEN
		RAISE e1;
	ELSE
		PRINT v_col.V_NAME || v_col.V_EMAIL;
	END IF;
EXCEPTION
	WHEN e1 THEN
		PRINT v_col.V_NAME ||' '||SQLCODE||':' ||SQLERRM(SQLCODE);
END;
/
CALL proc_exception2(2);
```

执行结果如下：

```
王刚 -2206:[-2206]:无效的参数值
```

例 2 使用 EXCEPTION_INIT 定义用户自定义异常，并使用 RAISE 语句抛出该异常。

```
CREATE OR REPLACE PROCEDURE proc_exception3 (v_personid INT)

IS

	TYPE Rec_type IS RECORD (V_NAME VARCHAR(50),V_EMAIL VARCHAR(50));

	v_col Rec_type;

	e2 EXCEPTION;

	PRAGMA EXCEPTION_INIT(e2, -25000);

BEGIN

	SELECT NAME,EMAIL INTO v_col FROM PERSON.PERSON WHERE PERSONID=v_personid;

	IF v_col.V_EMAIL='' THEN

		RAISE e2;

	ELSE

		PRINT v_col.V_NAME || v_col.V_EMAIL;

	END IF;

EXCEPTION

	WHEN e2 THEN

		PRINT v_col.V_NAME ||'的邮箱为空';

END;

/

CALL proc_exception3(2);
```

执行结果如下：

```
王刚的邮箱为空
```

## 6.4 异常的抛出

DMSQL 程序运行时如果发生错误，系统会自动抛出一个异常。

此外，程序员还可以使用 RAISE 主动抛出一个异常。例如，当程序运行并不违反数据库规则，但是不满足应用的业务逻辑时，可以主动抛出一个异常并进行处理。

使用 RAISE 抛出异常分为有异常名和无异常名两种情况。

  * **有异常名**



语法如下：

```
RAISE <异常名>
```

可以使用 RAISE 语句抛出一个系统预定义异常或用户自定义异常。

6.3 节中已经有使用 RAISE 语句的示例，这里不再举例。

  * **无异常名**



如果没有在声明部分定义异常变量，也可以在执行部分使用 DM 提供的系统过程直接抛出自定义异常。

语法如下：

```
RAISE_APPLICATION_ERROR (
ERR_CODE IN INT,
ERR_MSG IN VARCHAR(2000)
);
```

参数说明：

ERR_CODE：错误码，取值范围为-20000~-30000；

ERR_MSG：用户自定义的错误信息，长度不能超过 2000 字节。

举例说明：

```
CREATE OR REPLACE PROCEDURE proc_exception4 (v_personid INT)

IS

	TYPE Rec_type IS RECORD (V_NAME VARCHAR(50),V_EMAIL VARCHAR(50));

	v_col Rec_type;

BEGIN

	SELECT NAME,EMAIL INTO v_col FROM PERSON.PERSON WHERE PERSONID=v_personid;

	IF v_col.V_EMAIL='' THEN

		RAISE_APPLICATION_ERROR(-20001, '邮箱为空');

	ELSE

		PRINT v_col.V_NAME || v_col.V_EMAIL;

	END IF;

EXCEPTION

	WHEN OTHERS THEN

		PRINT SQLCODE ||' '||v_col.V_NAME ||':' ||SQLERRM;

END;

/

CALL proc_exception4(2);
```

执行结果如下：

```
-20001 王刚:邮箱为空
```

## 6.5 内置函数 SQLCODE 和 SQLERRM

DMSQL 程序提供了内置函数 SQLCODE 和 SQLERRM，程序员可以在异常处理部分通过这两个函数获取异常对应的错误码和描述信息。SQLCODE 返回错误码，为一个负数。SQLERRM 返回异常的描述信息，为字符串类型。

例

```
DECLARE

	e1 EXCEPTION FOR -20001, 'EMAIL为空';

BEGIN

	RAISE e1;

EXCEPTION

	WHEN e1 THEN

		PRINT SQLCODE ||' '|| SQLERRM;

END;

/
```

执行结果如下：

```
-20001 EMAIL为空
```

若异常为 DM 服务器错误，则 SQLERRM 返回该错误的描述信息，否则 SQLERRM 的返回值遵循以下规则：

  * 如果错误码在-15000 至-19999 间，返回'User-Defined Exception'；
  * 如果错误码在-20000 至-30000 之间，则当手动抛出异常时，返回该错误的描述信息；否则，返回'DM-\< 错误码绝对值 >'；
  * 如果错误码大于 0 或小于-65535，返回'-\<错误码绝对值>: non-DM exception'；
  * 否则，返回'DM-\<错误码绝对值>: Message \<错误码绝对值> not found;'。



如果程序员想查询某个 DM 服务器错误码对应的错误描述信息，也可以在执行部分或异常处理部分使用错误码参数调用 SQLERRM 函数。

语法如下：

```
VARCHARSQLERRM(ERROR_NUMBER INT(4));
```

这种 SQLERRM 调用方式的返回值与上面所述的规则存在以下区别：

  1. 如果错误码为 DM 服务器错误码，则指定参数调用 SQLERRM 时返回'[-\< 错误码绝对值 >]:描述信息'，而不指定参数调用 SQLERRM 时返回'描述信息' ；
  2. 如果错误码在-20000 至-30000 之间，则指定参数调用 SQLERRM 时始终返回'DM-\< 错误码绝对值 >'。



举例如下：

```
BEGIN
	PRINT 'SQLERRM(-6815): ' || SQLERRM(-6815);
END;
```

执行结果如下：

```
SQLERRM(-6815): [-6815]:指定的对象数据库中不存在
```

## 6.6 异常处理部分

DMSQL 程序的异常处理部分对执行过程中抛出的异常进行处理，可以处理一个或多个异常。

语法如下：

```
EXCEPTION {<异常处理语句>;}

<异常处理语句> ::= WHEN <异常处理器>

<异常处理器> ::= <异常名>{ OR <异常名>} THEN <执行部分>;
```

EXCEPTION 关键字表示异常处理部分的开始。如果在语句块的执行中出现异常，执行就被传递给语句块的异常处理部分。而如果在本语句块的异常处理部分没有相应的异常处理器对它进行处理，系统就会中止此语句块的执行，并将此异常传递到该语句块的上一层语句块或其调用者，这样一直到最外层。如果始终没有找到相应的异常处理器，则中止本次调用语句的处理，并向用户报告错误。

异常处理部分是可选的。但是如果出现 EXCEPTION 关键字时，必须至少有一个异常处理器。异常处理器可以按任意次序排列，只有 OTHERS 异常处理器必须在最后，它处理所有没有明确列出的异常。此外，同一个语句块内的异常处理器不允许处理重复的异常。

一个异常处理器可以同时对多个异常进行统一处理，在 WHEN 子句中用 OR 分隔多个异常名。

例 用 WHEN…OR 子句分隔多个异常，同时对多个异常进行统一处理

```
CREATE OR REPLACE PROCEDURE proc_exception5 (score INT) AS

	e1 EXCEPTION FOR -20001, '补考';

	e2 EXCEPTION FOR -20002, '不能补考';

BEGIN

	IF score BETWEEN 90 AND 100 THEN

		PRINT '考试通过';

	ELSEIF score BETWEEN 80 AND 90 THEN

		RAISE e1;

	ELSE

		RAISE e2;

	END IF;

EXCEPTION

	WHEN e1 OR e2 THEN --同时处理多个异常

		PRINT '考试不通过';

END;

/

CALL proc_exception5(50);
```

执行结果如下:

```
考试不通过
```

下层语句块中出现的异常，如果在该语句块中没有对应的异常处理器，可以被上层语句块或其调用者的异常处理器处理。

例 下层语句块没有异常处理器，出现异常被上层语句块的异常处理器处理。

```
DECLARE

	e1 EXCEPTION;

BEGIN

	PRINT '下层块之前';

	BEGIN

		RAISE e1;

	END;

	PRINT '下层块之后';

EXCEPTION

	WHEN e1 THEN

		PRINT '外层块处理下层块抛出的异常';

END;

/
```

执行结果如下：

```
下层块之前

外层块处理下层块抛出的异常
```

要注意异常变量的有效范围。

例 下面的例子中在上层语句块中引用下层语句块定义的异常变量，DMSQL 程序无法运行通过，会报错。

```
BEGIN
	PRINT '下层块之前';
	DECLARE
		e1 EXCEPTION;  //异常变量定义在下层语句块中
	BEGIN
		RAISE e1;
	END;
	PRINT '下层块之后';
EXCEPTION
	WHEN e1 THEN  //报错。因为异常变量e1在此范围内没有定义 
		PRINT '外层块处理下层块抛出的异常';
END;
/
```

执行结果如下：

```
[-2207]:无法解析的成员访问表达式[E1.CODE]
```

有时候，在异常处理器的执行中又可能出现新的异常。这时，系统将新出现的异常作为当前需要处理的异常，并向上层传递。例如：

```
DECLARE
	e1 EXCEPTION for -20000, 'EC_01';
	e2 EXCEPTION for -30000, 'EC_02';
BEGIN
	BEGIN
		RAISE e1;  //下层语句块抛出异常E1
	EXCEPTION
		WHEN e1 THEN
			RAISE e2;  //在e1的处理过程中，抛出e2。但是下层块并没有处理e2
	END;
EXCEPTION
	WHEN e1 THEN  //异常变量e1的异常处理器
		PRINT 'EXCEPTION e1 CATCHED';
	WHEN e2 THEN  //异常变量e2的异常处理器
		PRINT 'EXCEPTION e2 CATCHED';
END;
/
```

执行结果如下：

```
EXCEPTION e2 CATCHED
```
