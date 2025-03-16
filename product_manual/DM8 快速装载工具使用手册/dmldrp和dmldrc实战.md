

# dmldrp和dmldrc实战

dmldrp 和 dmldrc 需搭配使用才能完成快速装载。具体分为两步：一是先启动 dmldrp；二是使用 dmldrc 执行导入功能。

例 使用 dmldrp 和 dmldrc 工具将数据快速装载进 test 表。

1.数据准备。

  1. 建表 test。



```
DROP TABLE TEST;
CREATE TABLE TEST(C1 INT,C2 INT,C3 DATE);
```

  2. 编辑数据文件 test.txt，存放路径为/opt/data/test.txt，文件内容如下：



```
1 1|2015-11-06
2 2|2015-11-05
3 3|2015-11_04
```

  3. 编辑控制文件 test.ctl，存放路径为/opt/data/test.ctl，内容如下：



```
LOAD DATA
INFILE '/opt/data/test.txt'
INTO TABLE test
FIELDS '|'
(
C1 TERMINATED BY ' ',
C2,
C3 DATE FORMAT 'yyyy-mm-dd'
)
```

2.使用 dmldrp 和 dmldrc 完成快速装载。

  1. 启动 dmldrp 服务器。



```
./dmldrp port=9898
```

  2. 使用 dmldrc 进行数据载入。



```
./dmldrc userid=SYSDBA/*****@localhost:5236 localhost:9898 control=\'/opt/data/test.ctl\' MODE=\'IN\'
```
