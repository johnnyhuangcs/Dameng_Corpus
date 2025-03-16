

# PRO*C 程序实例

## 7.1SELECT 语句

使用 SELECT 语句，可以将从数据库中检索到的值赋给相应的目标变量。

数据库管理系统执行一个 SELECT 语句，实际上是导出查询说明对应的导出表。查询说明是 SELECT 语句中滤除 INTO 子句后的对应语句。如果导出表的基数为 0(结果集为空集)，则返回 SQLCODE 为 100；如果基数大于 1，则返回 SQLCODE 小于 0；如果基数为 1，则返回 SQLCODE 为 0，相应的结果置于选择目标清单中。

如果查询结果多于一个元组，返回一个错误信息(SQLCODE\<0)，此时应该使用游标查询。

例 在人员信息表中，查询人员编号为‘1’的姓名、联系电话。

```
EXEC SQL BEGIN DECLARE SECTION;
varchar person_name[50];
varchar person_phone[25];
varchar person_id[10];
EXEC SQL END DECLARE SECTION;
EXEC SQL SELECT name, phone
INTO :person_name, :person_phone
FROM person.person WHERE personid ='1';
......
```

## 7.2 插入、更新、删除语句

### 7.2.1 插入语句

例 设有另一表 person_1(name, phone)。将人员信息表中人员编号为‘1’的姓名、联系电话插入到 person_1 中。

```
##include <stdio.h>

EXEC SQL BEGIN DECLARE SECTION;

char username[19];

char password[19];

char servername[19];

EXEC SQL END DECLARE SECTION;

void main( )

{

printf("Input Username: ");

scanf("%s", username);

printf("Input Password: ");

scanf("%s", password);

printf(" Please input servername :");

scanf("%s",servername);

EXEC SQL LOGIN :username PASSWORD :password SERVER :servername;

EXEC SQL INSERT INTO person_1

SELECT name, phone FROM person.person

WHERE personid ='1';

EXEC SQL COMMIT WORK;

EXEC SQL LOGOUT;

}
```

### 7.2.2 更新语句

更新的方式有两种，一种是游标定位更新，另一种是搜索更新。

游标定位更新对游标所指的当前行的某些属性值进行更新。当打开一个游标时，游标指针处于游标表的第一行之前。因而应先执行 FETCH 操作，再对当前数据进行更新。

例 1 在人员信息表中，使用游标定位更新修改人员编号为‘1’的姓名和联系电话。

```
#include <stdio.h>
EXEC SQL BEGIN DECLARE SECTION;
char username[19],password[19],servername[19];
varchar old_id[10],new_id[10];
varchar old_name[50],new_name[50];
varchar old_phone[25],new_phone[25];
EXEC SQL END DECLARE SECTION;
void main( )
{
strcpy(new_id,"1");
strcpy(new_name,"李丽1");
strcpy(new_phone,"01088548562");
printf("Input Username: ");
scanf("%s",username);
printf("Input Password: ");
scanf("%s",password);
printf(" Please input servername :");
scanf("%s",servername);
EXEC SQL LOGIN :username PASSWORD :password SERVER :servername;
EXEC SQL DECLARE C1 CURSOR FOR
SELECT name, phone
FROM person.person WHERE personid='1';
EXEC SQL OPEN C1;
EXEC SQL WHENEVER NOT FOUND GOTO endexit;
for(;;)
{
EXEC SQL FETCH C1 INTO:old_name,:old_phone;
printf("The current tuple is: %s %s", old_name,
old_phone);
......
//新的数值 new_no, new_name, new_addr */
EXEC SQL UPDATE person.person
SET name =:new_name, phone =:new_phone
WHERE CURRENT OF C1;
}
endexit:
EXEC SQL CLOSE C1;
EXEC SQL COMMIT WORK;
EXEC SQL LOGOUT;
}
```

例 2 下面的例子使用搜索更新将人员编号为‘1’的元组的联系电话改为"01088548562"。

```
##include <stdio.h>

EXEC SQL BEGIN DECLARE SECTION;

char username[19],password[19],servername[19];

EXEC SQL END DECLARE SECTION;

void main( )

{

printf("Input Username:");

scanf("%s",username);

printf("Input Password:");

scanf("%s",password);

printf(" Please input servername :");

scanf("%s",servername);

EXEC SQL LOGIN :username PASSWORD :password SERVER :servername;

EXEC SQL UPDATEperson.person

SET phone = '01088548562'

WHERE personid='1';

EXEC SQL COMMIT WORK;

EXEC SQL LOGOUT;

}
```

### 7.2.3 删除语句

删除的方式有两种，一种是游标定位删除，另一种是搜索删除。

定位删除是指将游标所指的当前行删除。

例 1 游标查询人员信息表，询问是否要删除元组。

```
......

EXEC SQL DECLARE C1 CURSOR FOR

SELECT name, phone FROM person.person ;

EXEC SQL OPEN C1;

EXEC SQL WHENEVER NOT FOUND GOTO endexit;

for(;;)

{

EXEC SQL FETCH C1 INTO :person_name,:person_phone;

printf(" Tuple is %s %s ", person_name, person_phone);

printf(" delete <Yes / No > ? ");

fflush(stdin);

scanf("%c",&select);

if(select==' y' || select=='Y')

EXEC SQL DELETE FROM person.person WHERE CURRENT OF C1;

}

endexit:

EXEC SQL CLOSE C1;

EXEC SQL COMMIT WORK;

EXEC SQL LOGOUT;

......
```

搜索删除是指对符合某种条件的元组进行删除操作。

例 2 使用搜索删除将人员信息表中人员编号为‘1’的元组删除。

```
......

EXEC SQL DELETE FROM person.person WHERE personid='1';

EXEC SQL COMMIT WORK;

......
```

## 7.3 日期、时间数据类型的使用

日期、时间数据类型用于存储日期和时间信息，虽然日期和时间信息可以表示成字符串和数值数据类型，但日期和时间类型具有特殊的关联特性。下面我们通过一个例子介绍日期、时间、时间间隔类型的使用。

例

```
#include <stdio.h>
#include <stdlib.h>
EXEC SQL BEGIN DECLARE SECTION;
char username[19],password[19],servername[19];
int year1,month1,day1;
EXEC SQL END DECLARE SECTION;
void main( )
{
printf(" Please input Servername :");
scanf("%s",servername);
printf("Input Username: ");
scanf("%s", username);
printf("Input Password: ");
scanf("%s",password);
EXEC SQL LOGIN :username PASSWORD :password SERVER :servername;
//建存储日期时间的基表 
EXEC SQL CREATE TABLE DATETIME1(
COL1 DATE,
COL2 TIME(6),
COL3 TIME(6),
COL4 TIMESTAMP(6),
COL5 TIMESTAMP(6),
COL6 INTERVAL YEAR TO MONTH,
COL7 INTERVAL YEAR(4) TO MONTH,
COL8 INTERVAL SECOND(6),
COL9 INTERVAL YEAR,
COL10 INTERVAL HOUR,
COL11 INTERVAL DAY(3) TO HOUR,
COL12 INTERVAL HOUR(5) TO SECOND(6)
);
//插入日期、时间值 
EXEC SQL INSERT INTO DATETIME1
VALUES(DATE '1998-06-12',
TIME '08:09:20.123456',
TIME '09:10:21.234567',
TIMESTAMP '1999-07-13 10:11:22.345678',
TIMESTAMP '2000-08-14 11:12:23.456789',
INTERVAL '0022-11'YEAR TO MONTH,
INTERVAL '2000'YEAR,
INTERVAL '21.12345' SECOND,
INTERVAL '0055'YEAR,
INTERVAL '15'HOUR,
INTERVAL '110 22'DAY TO HOUR,
INTERVAL '19:22:10. 654321'HOUR TO SECOND
);
// 更新日期、时间值
EXEC SQL UPDATE DATETIME1
SET COL1=(INTERVAL '0010-10' YEAR TO MONTH - INTERVAL '0020' YEAR)\*2+COL1;
// 调用日期、时间函数 
EXEC SQL SELECT EXTRACT(YEAR FROM COL1),
EXTRACT(MONTH FROM COL1),
EXTRACT(DAY FROM COL1)
INTO :year1,:month1,:day1
FROM DATETIME1;
printf("\n The day is %d-%d-%d \n",year1,month1,day1);
EXEC SQL COMMIT WORK;
EXEC SQL LOGOUT;
exit(0);
}
```

## 7.4 多线程

6.7 节中介绍了 DM 的 PRO*C 对多线程应用的支持，下面的例子生成了多个线程，这些线程使用各自的上下文环境并发执行。

```
#include <stdio.h>
#include <time.h>
#ifdef WIN32
#include <Windows.h>
#include <PROCESS.H>
#include <winbase.h>
typedef HANDLE os_thread_t;
typedef LPTHREAD_START_ROUTINE os_thread_fun_t;
#else
#include <pthread.h>
#endif
#define NUM_TH 10

struct parameters
{
    sql_context ctx;
    int personid;
};
typedef struct parameters parameters;

EXEC SQL INCLUDE sqlca;
void select_person(parameters *param);

EXEC SQL BEGIN DECLARE SECTION;
    char servername[50];
    char username[50];
    char pwd[50];
EXEC SQL END DECLARE SECTION;

void
CHECKOK()
{
    if(sqlca.sqlcode!=0)
    {
    	printf("fail! [%d] %s\n",sqlca.sqlcode,sqlca.sqlerrm.sqlerrmc);
    }
}

void
main()
{
    int i;
    int rt;
    sql_context ctx[NUM_TH];
    parameters param[NUM_TH];
#ifdef WIN32
	DWORD code;
	os_thread_t thread_id[NUM_TH];
#else
	pthread_t thread_id[NUM_TH];
#endif
  
    printf("\n\n BEGIN proc027_main1 dynamic bind number \n");
    	EXEC SQL ENABLE THREADS;
    strcpy(username, "SYSDBA");
    strcpy(pwd, "*****");
    strcpy(servername, "192.168.0.89:5236");
  
    for(i=0;i<NUM_TH;i++)
    {
        EXEC SQL CONTEXT ALLOCATE :ctx[i];
        param[i].ctx=ctx[i];
        param[i].personid=i+1;
#ifdef WIN32
    thread_id[i] =
_beginthreadex(NULL,1024*1024,(os_thread_fun_t)select_person,&param[i],0,NULL);
#else
    rt = pthread_create(&thread_id[i],NULL,(void*)select_person,&param[i]);
#endif
    }
#ifdef WIN32
    while(TRUE)
    {
        for(i = 0; i < NUM_TH; i++)
        {
            rt = GetExitCodeThread(thread_id[i], &code);
            if(code == STILL_ACTIVE && rt != 0) //if there is alive thread, then sleep, and
            check again from beginning
            break;
        }
		Sleep(100);
        if(i == NUM_TH)
        	break;
	}
#else
    for(i=0;i<NUM_TH;i++)
    {
    	pthread_join(thread_id[i],NULL);
    }
#endif
	printf("\n\n END MAIN \n");
}

void
select_person(parameters *param)
{
EXEC SQL BEGIN DECLARE SECTION;
    int SQLCODE;
    char SQLSTATE[6];
    SQL_CONTEXT ctx;
    int personid;
    int personid_out;
    char name[51];
EXEC SQL END DECLARE SECTION;

    ctx=param->ctx;
    personid=param->personid;
	EXEC SQL CONTEXT USE :ctx;

/******************** BEGIN select_person 多线程并发 ********************/
        printf("\n BEGIN select_person \n");
        EXEC SQL CONNECT :username IDENTIFIED BY :pwd USING :servername;

	EXEC SQL SELECT personid,name into :personid_out,:name FROM person.person where personid=:personid;

    CHECKOK();
  
    printf("personid_out=%d,name=%s namelen=%d\n",personid_out,name,strlen(name));
  
    EXEC SQL CLOSE C1;
    EXEC SQL COMMIT RELEASE;
    EXEC SQL CONTEXT FREE :ctx;
  
/******************** END select_person  ********************/
}
```
