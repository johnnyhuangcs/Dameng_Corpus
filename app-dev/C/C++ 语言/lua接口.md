

# lua接口

## 一、环境准备

### 1.1 达梦数据库安装

可访问达梦云适配中心[下载试用](https://eco.dameng.com/download/?_blank)，下载 DM8 数据库试用版并安装，安装步骤请参考[数据库环境准备](#%E6%95%B0%E6%8D%AE%E5%BA%93%E7%8E%AF%E5%A2%83%E5%87%86%E5%A4%87)。

### 1.2 注册 ODBC 数据源

请参考[通过 ODBC 连接数据库](https://eco.dameng.com/document/dm/zh-cn/start/C_C++_development.html#%E9%80%9A%E8%BF%87%20ODBC%20%E8%BF%9E%E6%8E%A5%E6%95%B0%E6%8D%AE%E5%BA%93)。

## 1.3 下载 Lua 源码

点击 [luasql](https://github.com/lunarmodules/luasql) 下载源码。

## 二、配置 LuaSQL

### 2.1 解压 LuaSQL

```
unzip luasql-master.zip
```

### 2.2 修改配置文件

修改 odbc 配置参数。

```
cd luasql-master
vi config

DRIVER_LIBS_odbc ?= -L/usr/local/lib -lodbc   //odbc动态链接库
DRIVER_INCS_odbc ?= -DUNIXODBC -I/usr/local/include  //c头文件目录
```

![图片 1.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240930150341O4ZEOE6ABAH5WJ7AKL)

### 2.3 生成 C 程序库

```
 make odbc install
```

![图片 2.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240930150430B41X1PJ1GONEHW4U0K)

## 三、测试用例

### 3.1 测试连接

创建 test.lua 文件，测试连接的代码如下：

```
local luasql =  require "luasql.odbc"
env = luasql.odbc()
con = env:connect ("DM8","SYSDBA","*****")
conn =assert(con)
if conn then
print("连接成功")
con:close()
else
print("连接失败")
end
env:close()
```

执行命令 `lua test.lua`，结果如下：

![图片 3.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2024093015055003CFMDK3H9XIHI53FZ)

### 3.2 测试插入

创建 test2.lua 文件，插入数据的代码如下：

```
local luasql =  require "luasql.odbc"
env = luasql.odbc()
con = env:connect ("DM8","SYSDBA","*****")
conn =assert(con)

con:execute("DROP TABLE IF EXISTS SYSDBA.TEST01;")

local create_table_sql = [[
create table "SYSDBA"."TEST01"
(
	"ID" INTEGER,
	"NAME" VARCHAR(100)
);
]]

local res, err = conn:execute(create_table_sql)
if res then
    print("Table created successfully!")
else
    print("Failed to create table: " .. err)
end

local insert_sql = [[
INSERT INTO "SYSDBA"."TEST01" (ID, NAME) VALUES (1,'Alice');
INSERT INTO "SYSDBA"."TEST01" (ID, NAME) VALUES (2,'Bob');
]]

local res, err = conn:execute(insert_sql)
if res then
    print("Data inserted successfully!")
else
    print("Failed to insert data: " .. err)
end

local update_sql = "UPDATE SYSDBA.TEST01 SET ID = 31 WHERE NAME = 'Alice';"
local res, err = conn:execute(update_sql)
if res then
    print("Data updated successfully!")
else
    print("Failed to update data: " .. err)
end


local delete_sql = "DELETE FROM SYSDBA.TEST01 WHERE NAME = 'Bob';"
local res, err = conn:execute(delete_sql)
if res then
    print("Data deleted successfully!")
else
    print("Failed to delete data: " .. err)
end

cur = con:execute ("SELECT * FROM SYSDBA.TEST01;")
row = cur:fetch ({}, "a")
local result_set = {}

while row do
  table.insert(result_set, row)
  row = cur:fetch(row, "a")
end
cur:close()
con:close()
env:close()
for i, row in ipairs(result_set) do
    for k, v in pairs(row) do
        print(k, v)
    end
    print("-----")
end
```

执行命令 `lua test.lua`，结果如下：

![图片 4.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240930150714QS8NT5G75V2BB1BHQA)

查看数据：

![图片 5.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202409301507434WON4FKB66MMYCP82F)
