

# 基于 C、JAVA 语法的 DMSQL 程序

在程序员们的印象中，过程化 SQL 语言是较复杂的，因为它有自己的语法规则，用户通常都不会去记那么多复杂的语法，只是在用的时候查看手册，所以当需要编写一个逻辑较复杂的 DMSQL 程序可能会碰到一些困难。

DM8 实现了用 C、JAVA 语言语法作为 DMSQL 程序的可选语法，这就为那些了解 C、JAVA 语言的人提供了很大的方便性，无需查看手册就可以很自如的完成一个语句块，对 SQL 程序员而言，这个功能无疑是他们梦寐以求的。

## 7.1 C 语法 DMSQL 程序

编写 C 语言语法的 DMSQL 程序，定义语句块时不需要用 BEGIN 及 END 把语句包含起来，而是直接用大括号括住即可。

下面给出两个 C 语法的 DMSQL 程序的例子来更具体地说明。

例 1

```
{

	int i = 0;

	VARCHAR v_name;

	VARCHAR v_email;

	for(i = 1; i < =7; i++)

	{

		SELECT NAME,EMAIL INTO v_name,v_email FROM PERSON.PERSON WHERE PERSONID=i;

		PRINT CONCAT(i,v_name); //使用数据库函数concat

		PRINT v_email;

	}

}

/
```

例 2

```
{

	try

	{

		SELECT 1/0;

	}

	catch(EXCEPTION EX)

	{

		THROW NEW EXCEPTION(-20002,'TEST');

	}

}

/
```

使用 C 语法的 DMSQL 程序时，可以很自由地调用一些系统内部函数（如上面例子中的 concat()）、存储函数、过程等等。可以定义像 C##中的一些数据类型，如 STRING 类型，还可以定义 C 语言中的基本数据类型，如上面例子中的 int，另外还支持全部的 SQL 类型，DM 内部定义的类类型包括 EXCEPTION 类、数组类型、游标类型等。

在 C 语法 DMSQL 程序中使用游标时需要在 OPEN、FETCH 及 CLOSE 后使用“CURSOR”关键字，下面是一个使用 C 语法 DMSQL 程序操作游标的例子。

例 使用 C 语法 DMSQL 程序操作游标。

```
{
	VARCHAR v_name;
	VARCHAR v_phone;
	int i=0;
	CURSOR csr IS SELECT NAME,PHONE FROM PERSON.PERSON A,RESOURCES.EMPLOYEE B WHERE A.PERSONID=B.PERSONID;
	string str;
	{
		OPEN CURSOR csr;
		if (csr%ISOPEN)
		{
			PRINT 'CURSOR IS ALREADY FIRST';
			while(i<=csr%ROWCOUNT)
			{
				FETCH CURSOR csr INTO v_name,v_phone;
				PRINT csr%ROWCOUNT;   //从游标上取数据后，csr%ROWCOUNT属性加1
				PRINT CONCAT(v_name,v_phone);
				i++;
			}
			CLOSE CURSOR csr;
		}
		else
			PRINT 'CURSOR IS NOT OPENED';
		OPEN CURSOR csr;  //再次打开，则会初始化各个属性 
		if (csr%ISOPEN)
		{
			PRINT 'CURSOR IS OPENED AGAIN';
			PRINT csr%ROWCOUNT;
		}
		else
			PRINT 'CURSOR IS NOT OPENED';
	}
}
/
```

## 7.2 JAVA 语法 DMSQL 程序

当使用 JAVA 语法编写 DMSQL 程序时，可以按 JAVA 语言风格定义一个类，之后在 DMSQL 程序中就可以以 JAVA 语言风格创建这个类对象并调用类的属性和方法。

举例说明：

例 1 一个使用 JAVA 语法的 DMSQL 程序示例。

```
create or replace java public class Class1 {

	int b = 5;

	Class1 (int a)

	{

		this.b = a;

	}

	int testFun(int a)

	{

		b = a;

		return b;

	}

}

/

DECLARE

	c1 class1;

	c2 class1;

BEGIN

	c1 = new class1( );

	PRINT c1.b;

	PRINT c1.testFun(9);

	c2 = new class1(8);

	PRINT c2.testFun(c2.b);

END;

/
```

在 JAVA 语法 DMSQL 程序中使用游标时需要在 OPEN、FETCH 及 CLOSE 后使用“CURSOR”关键字。

例 2 演示如何使用 JAVA 语法的 DMSQL 程序操纵游标。

```
create or replace java public class cls_java

{

	VARCHAR v_name;

	VARCHAR v_phone;

	int I=0;

	int fun1()

	{

		CURSOR csr FOR SELECT NAME,PHONE FROM PERSON.PERSON A,RESOURCES.EMPLOYEE B WHERE
A.PERSONID=B.PERSONID;

		OPEN CURSOR csr;

		while(i<=csr%ROWCOUNT)

		{

			FETCH CURSOR csr INTO v_name,v_phone;

			PRINT CONCAT(v_name,v_phone);

			i++;

		}

	CLOSE CURSOR csr;

	}

}

/

DECLARE

	c1 cls_java;

BEGIN

	c1 = new cls_java();

	c1.fun1();

END;

/
```
