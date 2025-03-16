

# SQL 语法

本章节主要介绍达梦数据库 SQL 语法常见问题，为用户提供 SQL 语法常见问题的分析和解决思路。除此之外，用户还可前往[达梦技术社区](https://eco.dameng.com/community/question/)参与更多问题讨论。

## 目录

  * [between and 怎么使用](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#between%20and%20%E6%80%8E%E4%B9%88%E4%BD%BF%E7%94%A8)
  * [如何设置成主键后，生成默认自增](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E5%A6%82%E4%BD%95%E8%AE%BE%E7%BD%AE%E6%88%90%E4%B8%BB%E9%94%AE%E5%90%8E%EF%BC%8C%E7%94%9F%E6%88%90%E9%BB%98%E8%AE%A4%E8%87%AA%E5%A2%9E)
  * [小写调用存储过程报错](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E5%B0%8F%E5%86%99%E8%B0%83%E7%94%A8%E5%AD%98%E5%82%A8%E8%BF%87%E7%A8%8B%E6%8A%A5%E9%94%99)
  * [DM 中按姓氏笔画排序用什么函数](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#DM%20%E4%B8%AD%E6%8C%89%E5%A7%93%E6%B0%8F%E7%AC%94%E7%94%BB%E6%8E%92%E5%BA%8F%E7%94%A8%E4%BB%80%E4%B9%88%E5%87%BD%E6%95%B0)
  * [有类似 Oracle 的 REDO 日志的文件吗，如何语句查询](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E6%9C%89%E7%B1%BB%E4%BC%BC%20Oracle%20%E7%9A%84%20REDO%20%E6%97%A5%E5%BF%97%E7%9A%84%E6%96%87%E4%BB%B6%E5%90%97%EF%BC%8C%E5%A6%82%E4%BD%95%E8%AF%AD%E5%8F%A5%E6%9F%A5%E8%AF%A2)
  * [字符串截断、超长文本截取](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E5%AD%97%E7%AC%A6%E4%B8%B2%E6%88%AA%E6%96%AD%E3%80%81%E8%B6%85%E9%95%BF%E6%96%87%E6%9C%AC%E6%88%AA%E5%8F%96)
  * [to_date(2019/12/31,​'YYYY-MM-DD​')报：无效的日期格式](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#to_date\(2019/12/31,'YYYY-MM-DD'\)%E6%8A%A5%EF%BC%9A%E6%97%A0%E6%95%88%E7%9A%84%E6%97%A5%E6%9C%9F%E6%A0%BC%E5%BC%8F)
  * [数据链路在哪里创建](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E6%95%B0%E6%8D%AE%E9%93%BE%E8%B7%AF%E5%9C%A8%E5%93%AA%E9%87%8C%E5%88%9B%E5%BB%BA)
  * [DM 有没有类似 pl\sql 的对比表结构，比对表数据差异的工具，生成更新脚本，更新到正式环境](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#DM%20%E6%9C%89%E6%B2%A1%E6%9C%89%E7%B1%BB%E4%BC%BC%20pl\\sql%20%E7%9A%84%E5%AF%B9%E6%AF%94%E8%A1%A8%E7%BB%93%E6%9E%84%EF%BC%8C%E6%AF%94%E5%AF%B9%E8%A1%A8%E6%95%B0%E6%8D%AE%E5%B7%AE%E5%BC%82%E7%9A%84%E5%B7%A5%E5%85%B7%EF%BC%8C%E7%94%9F%E6%88%90%E6%9B%B4%E6%96%B0%E8%84%9A%E6%9C%AC%EF%BC%8C%E6%9B%B4%E6%96%B0%E5%88%B0%E6%AD%A3%E5%BC%8F%E7%8E%AF%E5%A2%83)
  * [DM 数据库如何获取表的列注释？在代码中如何修改列属性的注释](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#DM%20%E6%95%B0%E6%8D%AE%E5%BA%93%E5%A6%82%E4%BD%95%E8%8E%B7%E5%8F%96%E8%A1%A8%E7%9A%84%E5%88%97%E6%B3%A8%E9%87%8A%EF%BC%9F%E5%9C%A8%E4%BB%A3%E7%A0%81%E4%B8%AD%E5%A6%82%E4%BD%95%E4%BF%AE%E6%94%B9%E5%88%97%E5%B1%9E%E6%80%A7%E7%9A%84%E6%B3%A8%E9%87%8A)
  * [DM 数据库 VARCHAR2 的长度最大是多少](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#DM%20%E6%95%B0%E6%8D%AE%E5%BA%93%20VARCHAR2%20%E7%9A%84%E9%95%BF%E5%BA%A6%E6%9C%80%E5%A4%A7%E6%98%AF%E5%A4%9A%E5%B0%91)
  * [DM 支持的字段名最大长度](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#DM%20%E6%94%AF%E6%8C%81%E7%9A%84%E5%AD%97%E6%AE%B5%E5%90%8D%E6%9C%80%E5%A4%A7%E9%95%BF%E5%BA%A6)
  * [DM 数据库有类似于 Oracle 的 sql 的视图吗](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#DM%20%E6%95%B0%E6%8D%AE%E5%BA%93%E6%9C%89%E7%B1%BB%E4%BC%BC%E4%BA%8E%20Oracle%20%E7%9A%84%20v$sql%20%E7%9A%84%E8%A7%86%E5%9B%BE%E5%90%97)
  * [[不能修改或删除聚集索引的列] 或 [试图删除聚集主键]、[表 xx 中不能同时包含聚集 KEY 和大字段]](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#\[%E4%B8%8D%E8%83%BD%E4%BF%AE%E6%94%B9%E6%88%96%E5%88%A0%E9%99%A4%E8%81%9A%E9%9B%86%E7%B4%A2%E5%BC%95%E7%9A%84%E5%88%97\]%20%E6%88%96%20\[%E8%AF%95%E5%9B%BE%E5%88%A0%E9%99%A4%E8%81%9A%E9%9B%86%E4%B8%BB%E9%94%AE\]%E3%80%81\[%E8%A1%A8%20xx%20%E4%B8%AD%E4%B8%8D%E8%83%BD%E5%90%8C%E6%97%B6%E5%8C%85%E5%90%AB%E8%81%9A%E9%9B%86%20KEY%20%E5%92%8C%E5%A4%A7%E5%AD%97%E6%AE%B5\])
  * [DM 数据库如何获取系统时间](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#DM%20%E6%95%B0%E6%8D%AE%E5%BA%93%E5%A6%82%E4%BD%95%E8%8E%B7%E5%8F%96%E7%B3%BB%E7%BB%9F%E6%97%B6%E9%97%B4)
  * [DM 是否可以并发创建索引](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#DM%20%E6%98%AF%E5%90%A6%E5%8F%AF%E4%BB%A5%E5%B9%B6%E5%8F%91%E5%88%9B%E5%BB%BA%E7%B4%A2%E5%BC%95)
  * [SQL 执行计划绑定变量和非绑定变量的不一致](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#SQL%20%E6%89%A7%E8%A1%8C%E8%AE%A1%E5%88%92%E7%BB%91%E5%AE%9A%E5%8F%98%E9%87%8F%E5%92%8C%E9%9D%9E%E7%BB%91%E5%AE%9A%E5%8F%98%E9%87%8F%E7%9A%84%E4%B8%8D%E4%B8%80%E8%87%B4)
  * [有过滤条件，查询时不走索引](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E6%9C%89%E8%BF%87%E6%BB%A4%E6%9D%A1%E4%BB%B6%EF%BC%8C%E6%9F%A5%E8%AF%A2%E6%97%B6%E4%B8%8D%E8%B5%B0%E7%B4%A2%E5%BC%95)
  * [如何修改用户默认表空间](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E5%A6%82%E4%BD%95%E4%BF%AE%E6%94%B9%E7%94%A8%E6%88%B7%E9%BB%98%E8%AE%A4%E8%A1%A8%E7%A9%BA%E9%97%B4)
  * [DM 支持 Oracle 的闪回查询么](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#DM%20%E6%94%AF%E6%8C%81%20Oracle%20%E7%9A%84%E9%97%AA%E5%9B%9E%E6%9F%A5%E8%AF%A2%E4%B9%88)
  * [null 值结果排序前后问题](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#null%20%E5%80%BC%E7%BB%93%E6%9E%9C%E6%8E%92%E5%BA%8F%E5%89%8D%E5%90%8E%E9%97%AE%E9%A2%98)
  * [Insert values 超过最大参数个数 (2048)](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#Insert%20values%20%E8%B6%85%E8%BF%87%E6%9C%80%E5%A4%A7%E5%8F%82%E6%95%B0%E4%B8%AA%E6%95%B0%20\(2048\))
  * [类似 Oracle 正则表达式 regexp_substr 函数](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E7%B1%BB%E4%BC%BC%20Oracle%20%E6%AD%A3%E5%88%99%E8%A1%A8%E8%BE%BE%E5%BC%8F%20regexp_substr%20%E5%87%BD%E6%95%B0)
  * [对象权限的授权](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E5%AF%B9%E8%B1%A1%E6%9D%83%E9%99%90%E7%9A%84%E6%8E%88%E6%9D%83)
  * [USING LONG ROW（启用超长记录）选项的作用](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#USING%20LONG%20ROW%EF%BC%88%E5%90%AF%E7%94%A8%E8%B6%85%E9%95%BF%E8%AE%B0%E5%BD%95%EF%BC%89%E9%80%89%E9%A1%B9%E7%9A%84%E4%BD%9C%E7%94%A8)
  * [关于 DM 数据库的数据类型](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E5%85%B3%E4%BA%8E%20DM%20%E6%95%B0%E6%8D%AE%E5%BA%93%E7%9A%84%E6%95%B0%E6%8D%AE%E7%B1%BB%E5%9E%8B)
  * [insert 后如何获取自增 ID](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#insert%20%E5%90%8E%E5%A6%82%E4%BD%95%E8%8E%B7%E5%8F%96%E8%87%AA%E5%A2%9E%20ID)
  * [DM 和其他数据库的兼容性配置](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#DM%20%E5%92%8C%E5%85%B6%E4%BB%96%E6%95%B0%E6%8D%AE%E5%BA%93%E7%9A%84%E5%85%BC%E5%AE%B9%E6%80%A7%E9%85%8D%E7%BD%AE)
  * [Oracle 和 DM 数据类型对比表](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#Oracle%20%E5%92%8C%20DM%20%E6%95%B0%E6%8D%AE%E7%B1%BB%E5%9E%8B%E5%AF%B9%E6%AF%94%E8%A1%A8)
  * [如何加 comment 备注](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E5%A6%82%E4%BD%95%E5%8A%A0%20comment%20%E5%A4%87%E6%B3%A8)
  * [字段名和关键字冲突](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E5%AD%97%E6%AE%B5%E5%90%8D%E5%92%8C%E5%85%B3%E9%94%AE%E5%AD%97%E5%86%B2%E7%AA%81)
  * [时间类型的默认值可以是当前时间](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E6%97%B6%E9%97%B4%E7%B1%BB%E5%9E%8B%E7%9A%84%E9%BB%98%E8%AE%A4%E5%80%BC%E5%8F%AF%E4%BB%A5%E6%98%AF%E5%BD%93%E5%89%8D%E6%97%B6%E9%97%B4)
  * [含中文字符的 SQL 语句执行报错](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E5%90%AB%E4%B8%AD%E6%96%87%E5%AD%97%E7%AC%A6%E7%9A%84%20SQL%20%E8%AF%AD%E5%8F%A5%E6%89%A7%E8%A1%8C%E6%8A%A5%E9%94%99)
  * [Manager 中 SQL 自动补全](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#Manager%20%E4%B8%AD%20SQL%20%E8%87%AA%E5%8A%A8%E8%A1%A5%E5%85%A8)
  * [如何使用打印信息](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E5%A6%82%E4%BD%95%E4%BD%BF%E7%94%A8%E6%89%93%E5%8D%B0%E4%BF%A1%E6%81%AF)
  * [PB 调用存储过程](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#PB%20%E8%B0%83%E7%94%A8%E5%AD%98%E5%82%A8%E8%BF%87%E7%A8%8B)
  * [shell 执行 DM 语句](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#shell%20%E6%89%A7%E8%A1%8C%20DM%20%E8%AF%AD%E5%8F%A5)
  * [update 和 order by 如何一起用](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#update%20%E5%92%8C%20order%20by%20%E5%A6%82%E4%BD%95%E4%B8%80%E8%B5%B7%E7%94%A8)
  * [SQL 语句中不带上模式名](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E5%86%99%20SQL%20%E5%A6%82%E4%BD%95%E6%89%8D%E8%83%BD%E4%B8%8D%E5%B8%A6%E4%B8%8A%E6%A8%A1%E5%BC%8F%E5%90%8D)
  * [分段连接、拼接字符串函数](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E5%88%86%E6%AE%B5%E8%BF%9E%E6%8E%A5%E3%80%81%E6%8B%BC%E6%8E%A5%E5%AD%97%E7%AC%A6%E4%B8%B2%E5%87%BD%E6%95%B0)
  * [DISQL 语句不结束怎么处理](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#DISQL%20%E8%AF%AD%E5%8F%A5%E4%B8%8D%E7%BB%93%E6%9D%9F%E6%80%8E%E4%B9%88%E5%A4%84%E7%90%86)
  * [获取 GUID 的函数](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E8%8E%B7%E5%8F%96%20GUID%20%E7%9A%84%E5%87%BD%E6%95%B0)
  * [需要 transform 定义 DDL 语句](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E9%9C%80%E8%A6%81%20transform%20%E5%AE%9A%E4%B9%89%20DDL%20%E8%AF%AD%E5%8F%A5)
  * [如何查询 SQL 计划中各操作符的执行时间](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E5%A6%82%E4%BD%95%E6%9F%A5%E8%AF%A2%20SQL%20%E8%AE%A1%E5%88%92%E4%B8%AD%E5%90%84%E6%93%8D%E4%BD%9C%E7%AC%A6%E7%9A%84%E6%89%A7%E8%A1%8C%E6%97%B6%E9%97%B4)
  * [执行 SQL 报错：试图在 blob 或者 clob 列上排序或比较](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E6%89%A7%E8%A1%8C%20SQL%20%E6%8A%A5%E9%94%99%EF%BC%9A%E8%AF%95%E5%9B%BE%E5%9C%A8%20blob%20%E6%88%96%E8%80%85%20clob%20%E5%88%97%E4%B8%8A%E6%8E%92%E5%BA%8F%E6%88%96%E6%AF%94%E8%BE%83)
  * [如何查询所有自增列](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E5%A6%82%E4%BD%95%E6%9F%A5%E8%AF%A2%E6%89%80%E6%9C%89%E8%87%AA%E5%A2%9E%E5%88%97)
  * [如何输出格式为 yyyy 年 mm 月 dd 日 HH 时 MM 分 SS 秒 的日期数据](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E5%A6%82%E4%BD%95%E8%BE%93%E5%87%BA%E6%A0%BC%E5%BC%8F%E4%B8%BA%20yyyy%20%E5%B9%B4%20mm%20%E6%9C%88%20dd%20%E6%97%A5%20HH%20%E6%97%B6%20MM%20%E5%88%86%20SS%20%E7%A7%92%20%E7%9A%84%E6%97%A5%E6%9C%9F%E6%95%B0%E6%8D%AE)
  * [达梦嵌套模糊查询如何编写](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E8%BE%BE%E6%A2%A6%E5%B5%8C%E5%A5%97%E6%A8%A1%E7%B3%8A%E6%9F%A5%E8%AF%A2%E5%A6%82%E4%BD%95%E7%BC%96%E5%86%99)
  * [达梦数据库如何兼容 MySQL 的语法](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E8%BE%BE%E6%A2%A6%E6%95%B0%E6%8D%AE%E5%BA%93%E5%A6%82%E4%BD%95%E5%85%BC%E5%AE%B9%20MySQL%20%E7%9A%84%E8%AF%AD%E6%B3%95)
  * [是否有类似 MySQL 的 ShA()等加密函数](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E6%98%AF%E5%90%A6%E6%9C%89%E7%B1%BB%E4%BC%BC%20MySQL%20%E7%9A%84%20ShA\(\)%E7%AD%89%E5%8A%A0%E5%AF%86%E5%87%BD%E6%95%B0)
  * [有类似 mysql 的 LAST_INSERT_ID() 的函数吗](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E6%9C%89%E7%B1%BB%E4%BC%BC%20mysql%20%E7%9A%84%20LAST_INSERT_ID\(\)%20%E7%9A%84%E5%87%BD%E6%95%B0%E5%90%97)
  * [如何写复制表的 SQL](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E5%A6%82%E4%BD%95%E5%86%99%E5%A4%8D%E5%88%B6%E8%A1%A8%E7%9A%84%20SQL)
  * [如何用命令清理归档日志](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E5%A6%82%E4%BD%95%E7%94%A8%E5%91%BD%E4%BB%A4%E6%B8%85%E7%90%86%E5%BD%92%E6%A1%A3%E6%97%A5%E5%BF%97)
  * [获取表的主键的视图](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E8%8E%B7%E5%8F%96%E8%A1%A8%E7%9A%84%E4%B8%BB%E9%94%AE%E7%9A%84%E8%A7%86%E5%9B%BE)
  * [在 Linux 环境下，AWR 报告如何取出来](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E5%9C%A8%20Linux%20%E7%8E%AF%E5%A2%83%E4%B8%8B%EF%BC%8CAWR%20%E6%8A%A5%E5%91%8A%E5%A6%82%E4%BD%95%E5%8F%96%E5%87%BA%E6%9D%A5)
  * [达梦数据库的“函数”、“存储过程”的使用参考文档](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E8%BE%BE%E6%A2%A6%E6%95%B0%E6%8D%AE%E5%BA%93%E7%9A%84%E2%80%9C%E5%87%BD%E6%95%B0%E2%80%9D%E3%80%81%E2%80%9C%E5%AD%98%E5%82%A8%E8%BF%87%E7%A8%8B%E2%80%9D%E7%9A%84%E4%BD%BF%E7%94%A8%E5%8F%82%E8%80%83%E6%96%87%E6%A1%A3)
  * [表的 id 字段没有重复值，但是添加主键就提示违反唯一性约束](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E8%A1%A8%E7%9A%84%20id%20%E5%AD%97%E6%AE%B5%E6%B2%A1%E6%9C%89%E9%87%8D%E5%A4%8D%E5%80%BC%EF%BC%8C%E4%BD%86%E6%98%AF%E6%B7%BB%E5%8A%A0%E4%B8%BB%E9%94%AE%E5%B0%B1%E6%8F%90%E7%A4%BA%E8%BF%9D%E5%8F%8D%E5%94%AF%E4%B8%80%E6%80%A7%E7%BA%A6%E6%9D%9F)
  * [表创建完成后怎么修改字段顺序？](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E8%A1%A8%E5%88%9B%E5%BB%BA%E5%AE%8C%E6%88%90%E5%90%8E%E6%80%8E%E4%B9%88%E4%BF%AE%E6%94%B9%E5%AD%97%E6%AE%B5%E9%A1%BA%E5%BA%8F%EF%BC%9F)
  * [是否支持 IF 函数](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E6%98%AF%E5%90%A6%E6%94%AF%E6%8C%81%20IF%20%E5%87%BD%E6%95%B0)
  * [NVARCHAR2 和 VARCHAR2、VARCHAR 的区别](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#NVARCHAR2%20%E5%92%8C%C2%A0VARCHAR2%E3%80%81VARCHAR%C2%A0%E7%9A%84%E5%8C%BA%E5%88%AB)
  * [达梦不支持布尔类型，请问可以使用什么替代？](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E8%BE%BE%E6%A2%A6%E4%B8%8D%E6%94%AF%E6%8C%81%E5%B8%83%E5%B0%94%E7%B1%BB%E5%9E%8B%EF%BC%8C%E8%AF%B7%E9%97%AE%E5%8F%AF%E4%BB%A5%E4%BD%BF%E7%94%A8%E4%BB%80%E4%B9%88%E6%9B%BF%E4%BB%A3%EF%BC%9F)
  * [达梦可以给自增列赋值么？](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E8%BE%BE%E6%A2%A6%E5%8F%AF%E4%BB%A5%E7%BB%99%E8%87%AA%E5%A2%9E%E5%88%97%E8%B5%8B%E5%80%BC%E4%B9%88%EF%BC%9F)
  * [达梦如何判断表中的字段是否为自增列？如何用脚本查询？](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E8%BE%BE%E6%A2%A6%E5%A6%82%E4%BD%95%E5%88%A4%E6%96%AD%E8%A1%A8%E4%B8%AD%E7%9A%84%E5%AD%97%E6%AE%B5%E6%98%AF%E5%90%A6%E4%B8%BA%E8%87%AA%E5%A2%9E%E5%88%97%EF%BC%9F%E5%A6%82%E4%BD%95%E7%94%A8%E8%84%9A%E6%9C%AC%E6%9F%A5%E8%AF%A2%EF%BC%9F)
  * [达梦数据库中整数相除如何让结果为小数？如 select TRUNCATE((1/5),4) from dual](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E8%BE%BE%E6%A2%A6%E6%95%B0%E6%8D%AE%E5%BA%93%E4%B8%AD%E6%95%B4%E6%95%B0%E7%9B%B8%E9%99%A4%E5%A6%82%E4%BD%95%E8%AE%A9%E7%BB%93%E6%9E%9C%E4%B8%BA%E5%B0%8F%E6%95%B0%EF%BC%9F%E5%A6%82%20select%20TRUNCATE\(\(1/5\),4\)%20from%20dual)
  * [达梦有类似 MySQL 的 conv() 进制转换的的函数么?](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E8%BE%BE%E6%A2%A6%E6%9C%89%E7%B1%BB%E4%BC%BC%20MySQL%20%E7%9A%84%20conv\(\)%20%E8%BF%9B%E5%88%B6%E8%BD%AC%E6%8D%A2%E7%9A%84%E7%9A%84%E5%87%BD%E6%95%B0%E4%B9%88?)
  * [达梦如何用脚本导出某个模式下全部表定义](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E8%BE%BE%E6%A2%A6%E5%A6%82%E4%BD%95%E7%94%A8%E8%84%9A%E6%9C%AC%E5%AF%BC%E5%87%BA%E6%9F%90%E4%B8%AA%E6%A8%A1%E5%BC%8F%E4%B8%8B%E5%85%A8%E9%83%A8%E8%A1%A8%E5%AE%9A%E4%B9%89)
  * [子查询中存在相同列名，进行查询时，报有歧义的列名](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E5%AD%90%E6%9F%A5%E8%AF%A2%E4%B8%AD%E5%AD%98%E5%9C%A8%E7%9B%B8%E5%90%8C%E5%88%97%E5%90%8D%EF%BC%8C%E8%BF%9B%E8%A1%8C%E6%9F%A5%E8%AF%A2%E6%97%B6%EF%BC%8C%E6%8A%A5%E6%9C%89%E6%AD%A7%E4%B9%89%E7%9A%84%E5%88%97%E5%90%8D)
  * [收集列的统计信息时是否会收集索引统计信息](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E6%94%B6%E9%9B%86%E5%88%97%E7%9A%84%E7%BB%9F%E8%AE%A1%E4%BF%A1%E6%81%AF%E6%97%B6%E6%98%AF%E5%90%A6%E4%BC%9A%E6%94%B6%E9%9B%86%E7%B4%A2%E5%BC%95%E7%BB%9F%E8%AE%A1%E4%BF%A1%E6%81%AF)
  * [在客户端做 TEXT 数据类型查询时，无法直接看到文本内容](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E5%9C%A8%E5%AE%A2%E6%88%B7%E7%AB%AF%E5%81%9A%20TEXT%20%E6%95%B0%E6%8D%AE%E7%B1%BB%E5%9E%8B%E6%9F%A5%E8%AF%A2%E6%97%B6%EF%BC%8C%E6%97%A0%E6%B3%95%E7%9B%B4%E6%8E%A5%E7%9C%8B%E5%88%B0%E6%96%87%E6%9C%AC%E5%86%85%E5%AE%B9)
  * [Mysql 的 replace into 语法，用达梦怎么实现](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#Mysql%20%E7%9A%84%20replace%20into%20%E8%AF%AD%E6%B3%95%EF%BC%8C%E7%94%A8%E8%BE%BE%E6%A2%A6%E6%80%8E%E4%B9%88%E5%AE%9E%E7%8E%B0)
  * [xml 解析库未加载](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#xml%20%E8%A7%A3%E6%9E%90%E5%BA%93%E6%9C%AA%E5%8A%A0%E8%BD%BD)
  * [SQL 语法错误：无效的表或视图名[dual]](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#SQL%20%E8%AF%AD%E6%B3%95%E9%94%99%E8%AF%AF%EF%BC%9A%E6%97%A0%E6%95%88%E7%9A%84%E8%A1%A8%E6%88%96%E8%A7%86%E5%9B%BE%E5%90%8D\[dual\])
  * [在 disql 中，语句用 ​";​" 无法结束](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E5%9C%A8%20disql%20%E4%B8%AD%EF%BC%8C%E8%AF%AD%E5%8F%A5%E7%94%A8%20%22;%22%20%E6%97%A0%E6%B3%95%E7%BB%93%E6%9D%9F)
  * [IP 函数 inet_aton()和 inet_ntoa()怎么转换成达梦语法？](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#IP%20%E5%87%BD%E6%95%B0%20inet_aton\(\)%E5%92%8C%20inet_ntoa\(\)%E6%80%8E%E4%B9%88%E8%BD%AC%E6%8D%A2%E6%88%90%E8%BE%BE%E6%A2%A6%E8%AF%AD%E6%B3%95%EF%BC%9F)
  * [如何利用数据库取得一个唯一值编号？](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E5%A6%82%E4%BD%95%E5%88%A9%E7%94%A8%E6%95%B0%E6%8D%AE%E5%BA%93%E5%8F%96%E5%BE%97%E4%B8%80%E4%B8%AA%E5%94%AF%E4%B8%80%E5%80%BC%E7%BC%96%E5%8F%B7%EF%BC%9F)
  * [达梦整数或者整除的显示，如何保持与 Oracle 相同？](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E8%BE%BE%E6%A2%A6%E6%95%B4%E6%95%B0%E6%88%96%E8%80%85%E6%95%B4%E9%99%A4%E7%9A%84%E6%98%BE%E7%A4%BA%EF%BC%8C%E5%A6%82%E4%BD%95%E4%BF%9D%E6%8C%81%E4%B8%8E%20Oracle%20%E7%9B%B8%E5%90%8C%EF%BC%9F)
  * [blob 字段用 length 取长度报错：参数不兼容](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#blob%20%E5%AD%97%E6%AE%B5%E7%94%A8%20length%20%E5%8F%96%E9%95%BF%E5%BA%A6%E6%8A%A5%E9%94%99%EF%BC%9A%E5%8F%82%E6%95%B0%E4%B8%8D%E5%85%BC%E5%AE%B9)
  * [PIVOT 行列转换函数报错](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#PIVOT%20%E8%A1%8C%E5%88%97%E8%BD%AC%E6%8D%A2%E5%87%BD%E6%95%B0%E6%8A%A5%E9%94%99)
  * [字符串太长，导致 LISTAGG 函数报“字符串截断”如何处理？](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E5%AD%97%E7%AC%A6%E4%B8%B2%E5%A4%AA%E9%95%BF%EF%BC%8C%E5%AF%BC%E8%87%B4%20LISTAGG%20%E5%87%BD%E6%95%B0%E6%8A%A5%E2%80%9C%E5%AD%97%E7%AC%A6%E4%B8%B2%E6%88%AA%E6%96%AD%E2%80%9D%E5%A6%82%E4%BD%95%E5%A4%84%E7%90%86%EF%BC%9F)
  * [查询语句中如何按照自定义的顺序进行排序](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E6%9F%A5%E8%AF%A2%E8%AF%AD%E5%8F%A5%E4%B8%AD%E5%A6%82%E4%BD%95%E6%8C%89%E7%85%A7%E8%87%AA%E5%AE%9A%E4%B9%89%E7%9A%84%E9%A1%BA%E5%BA%8F%E8%BF%9B%E8%A1%8C%E6%8E%92%E5%BA%8F)
  * [sql 快捷命令：授予用户对某一模式的只读权限，并查询](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#sql%20%E5%BF%AB%E6%8D%B7%E5%91%BD%E4%BB%A4%EF%BC%9A%E6%8E%88%E4%BA%88%E7%94%A8%E6%88%B7%E5%AF%B9%E6%9F%90%E4%B8%80%E6%A8%A1%E5%BC%8F%E7%9A%84%E5%8F%AA%E8%AF%BB%E6%9D%83%E9%99%90%EF%BC%8C%E5%B9%B6%E6%9F%A5%E8%AF%A2)
  * [使用 UTL_FILE 包报错：utf.file.open 无访问权限](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E4%BD%BF%E7%94%A8%20UTL_FILE%20%E5%8C%85%E6%8A%A5%E9%94%99%EF%BC%9Autf.file.open%20%E6%97%A0%E8%AE%BF%E9%97%AE%E6%9D%83%E9%99%90)
  * [如何查询一个表涉及到的所有触发器](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E5%A6%82%E4%BD%95%E6%9F%A5%E8%AF%A2%E4%B8%80%E4%B8%AA%E8%A1%A8%E6%B6%89%E5%8F%8A%E5%88%B0%E7%9A%84%E6%89%80%E6%9C%89%E8%A7%A6%E5%8F%91%E5%99%A8)
  * [dm 到 oracle 的 dblink 环境中，在 dm 上调用 oracle 包中的存储过程提示：获取对象失败](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#dm%20%E5%88%B0%20oracle%20%E7%9A%84%20dblink%20%E7%8E%AF%E5%A2%83%E4%B8%AD%EF%BC%8C%E5%9C%A8%20dm%20%E4%B8%8A%E8%B0%83%E7%94%A8%20oracle%20%E5%8C%85%E4%B8%AD%E7%9A%84%E5%AD%98%E5%82%A8%E8%BF%87%E7%A8%8B%E6%8F%90%E7%A4%BA%EF%BC%9A%E8%8E%B7%E5%8F%96%E5%AF%B9%E8%B1%A1%E5%A4%B1%E8%B4%A5)
  * [当虚拟列的基列中有空数据时，查询虚拟列或者把虚拟列作为查询条件会报错:非法的参数数据](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E5%BD%93%E8%99%9A%E6%8B%9F%E5%88%97%E7%9A%84%E5%9F%BA%E5%88%97%E4%B8%AD%E6%9C%89%E7%A9%BA%E6%95%B0%E6%8D%AE%E6%97%B6%EF%BC%8C%E6%9F%A5%E8%AF%A2%E8%99%9A%E6%8B%9F%E5%88%97%E6%88%96%E8%80%85%E6%8A%8A%E8%99%9A%E6%8B%9F%E5%88%97%E4%BD%9C%E4%B8%BA%E6%9F%A5%E8%AF%A2%E6%9D%A1%E4%BB%B6%E4%BC%9A%E6%8A%A5%E9%94%99:%E9%9D%9E%E6%B3%95%E7%9A%84%E5%8F%82%E6%95%B0%E6%95%B0%E6%8D%AE)
  * [如何利用 SQL 语句查看数据库用户限制登录和允许登录的 IP 地址](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E5%A6%82%E4%BD%95%E5%88%A9%E7%94%A8%20SQL%20%E8%AF%AD%E5%8F%A5%E6%9F%A5%E7%9C%8B%E6%95%B0%E6%8D%AE%E5%BA%93%E7%94%A8%E6%88%B7%E9%99%90%E5%88%B6%E7%99%BB%E5%BD%95%E5%92%8C%E5%85%81%E8%AE%B8%E7%99%BB%E5%BD%95%E7%9A%84%20IP%20%E5%9C%B0%E5%9D%80)
  * [如何批量禁用/启用用户下的所有约束信息](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E5%A6%82%E4%BD%95%E6%89%B9%E9%87%8F%E7%A6%81%E7%94%A8/%E5%90%AF%E7%94%A8%E7%94%A8%E6%88%B7%E4%B8%8B%E7%9A%84%E6%89%80%E6%9C%89%E7%BA%A6%E6%9D%9F%E4%BF%A1%E6%81%AF)
  * [插入的数据带 ​& 字符，显示请输入 XXX 的值，该如何解决](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E6%8F%92%E5%85%A5%E7%9A%84%E6%95%B0%E6%8D%AE%E5%B8%A6%20&%20%E5%AD%97%E7%AC%A6%EF%BC%8C%E6%98%BE%E7%A4%BA%E8%AF%B7%E8%BE%93%E5%85%A5%20XXX%20%E7%9A%84%E5%80%BC%EF%BC%8C%E8%AF%A5%E5%A6%82%E4%BD%95%E8%A7%A3%E5%86%B3)
  * [TRANSLATE 剔除字符串中的字符后结果为空](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#TRANSLATE%20%E5%89%94%E9%99%A4%E5%AD%97%E7%AC%A6%E4%B8%B2%E4%B8%AD%E7%9A%84%E5%AD%97%E7%AC%A6%E5%90%8E%E7%BB%93%E6%9E%9C%E4%B8%BA%E7%A9%BA)
  * [执行查询语句时报错：字符串转换出错](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E6%89%A7%E8%A1%8C%E6%9F%A5%E8%AF%A2%E8%AF%AD%E5%8F%A5%E6%97%B6%E6%8A%A5%E9%94%99%EF%BC%9A%E5%AD%97%E7%AC%A6%E4%B8%B2%E8%BD%AC%E6%8D%A2%E5%87%BA%E9%94%99)
  * [达梦的聚集索引不支持大字段操作，将聚集索引变成非聚集索引应如何操作](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E8%BE%BE%E6%A2%A6%E7%9A%84%E8%81%9A%E9%9B%86%E7%B4%A2%E5%BC%95%E4%B8%8D%E6%94%AF%E6%8C%81%E5%A4%A7%E5%AD%97%E6%AE%B5%E6%93%8D%E4%BD%9C%EF%BC%8C%E5%B0%86%E8%81%9A%E9%9B%86%E7%B4%A2%E5%BC%95%E5%8F%98%E6%88%90%E9%9D%9E%E8%81%9A%E9%9B%86%E7%B4%A2%E5%BC%95%E5%BA%94%E5%A6%82%E4%BD%95%E6%93%8D%E4%BD%9C)
  * [DM 中默认除数和被除数都是整数，最后的结果也被当作整数处理](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#DM%20%E4%B8%AD%E9%BB%98%E8%AE%A4%E9%99%A4%E6%95%B0%E5%92%8C%E8%A2%AB%E9%99%A4%E6%95%B0%E9%83%BD%E6%98%AF%E6%95%B4%E6%95%B0%EF%BC%8C%E6%9C%80%E5%90%8E%E7%9A%84%E7%BB%93%E6%9E%9C%E4%B9%9F%E8%A2%AB%E5%BD%93%E4%BD%9C%E6%95%B4%E6%95%B0%E5%A4%84%E7%90%86)
  * [LIKE 语句 CLOB 字段模糊查询报错：字符串截断](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#LIKE%20%E8%AF%AD%E5%8F%A5%20CLOB%20%E5%AD%97%E6%AE%B5%E6%A8%A1%E7%B3%8A%E6%9F%A5%E8%AF%A2%E6%8A%A5%E9%94%99%EF%BC%9A%E5%AD%97%E7%AC%A6%E4%B8%B2%E6%88%AA%E6%96%AD)
  * [在一条 select 查询中无法同时执行两个 distinct()函数](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E5%9C%A8%E4%B8%80%E6%9D%A1%20select%20%E6%9F%A5%E8%AF%A2%E4%B8%AD%E6%97%A0%E6%B3%95%E5%90%8C%E6%97%B6%E6%89%A7%E8%A1%8C%E4%B8%A4%E4%B8%AA%20distinct\(\)%E5%87%BD%E6%95%B0)
  * [插入数据时报错：违反引用约束[CONSxxxxx]](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E6%8F%92%E5%85%A5%E6%95%B0%E6%8D%AE%E6%97%B6%E6%8A%A5%E9%94%99%EF%BC%9A%E8%BF%9D%E5%8F%8D%E5%BC%95%E7%94%A8%E7%BA%A6%E6%9D%9F\[CONSxxxxx\])
  * [达梦有很多内置函数，比如 sp_set_para_value, sf_set，有没有一张表或者视图可以查询出来](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E8%BE%BE%E6%A2%A6%E6%9C%89%E5%BE%88%E5%A4%9A%E5%86%85%E7%BD%AE%E5%87%BD%E6%95%B0%EF%BC%8C%E6%AF%94%E5%A6%82%20sp_set_para_value,%20sf_set%EF%BC%8C%E6%9C%89%E6%B2%A1%E6%9C%89%E4%B8%80%E5%BC%A0%E8%A1%A8%E6%88%96%E8%80%85%E8%A7%86%E5%9B%BE%E5%8F%AF%E4%BB%A5%E6%9F%A5%E8%AF%A2%E5%87%BA%E6%9D%A5)
  * [对于 resource 角色的用户跨用户访问其他模式对象，向外键所在表插入修改数据报错：没有引用表[T1]上的权限](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E5%AF%B9%E4%BA%8E%20resource%20%E8%A7%92%E8%89%B2%E7%9A%84%E7%94%A8%E6%88%B7%E8%B7%A8%E7%94%A8%E6%88%B7%E8%AE%BF%E9%97%AE%E5%85%B6%E4%BB%96%E6%A8%A1%E5%BC%8F%E5%AF%B9%E8%B1%A1%EF%BC%8C%E5%90%91%E5%A4%96%E9%94%AE%E6%89%80%E5%9C%A8%E8%A1%A8%E6%8F%92%E5%85%A5%E4%BF%AE%E6%94%B9%E6%95%B0%E6%8D%AE%E6%8A%A5%E9%94%99%EF%BC%9A%E6%B2%A1%E6%9C%89%E5%BC%95%E7%94%A8%E8%A1%A8\[T1\]%E4%B8%8A%E7%9A%84%E6%9D%83%E9%99%90)
  * [Oracle XML 函数在 DM 中相同功能函数替代案例说明](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#Oracle%20XML%20%E5%87%BD%E6%95%B0%E5%9C%A8%20DM%20%E4%B8%AD%E7%9B%B8%E5%90%8C%E5%8A%9F%E8%83%BD%E5%87%BD%E6%95%B0%E6%9B%BF%E4%BB%A3%E6%A1%88%E4%BE%8B%E8%AF%B4%E6%98%8E)
  * [子查询语句执行报错，提示单行子查询返回多行](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E5%AD%90%E6%9F%A5%E8%AF%A2%E8%AF%AD%E5%8F%A5%E6%89%A7%E8%A1%8C%E6%8A%A5%E9%94%99%EF%BC%8C%E6%8F%90%E7%A4%BA%E5%8D%95%E8%A1%8C%E5%AD%90%E6%9F%A5%E8%AF%A2%E8%BF%94%E5%9B%9E%E5%A4%9A%E8%A1%8C)
  * [插入数据到视图，报错：提示违反视图[t1_v]CHECK 约束](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E6%8F%92%E5%85%A5%E6%95%B0%E6%8D%AE%E5%88%B0%E8%A7%86%E5%9B%BE%EF%BC%8C%E6%8A%A5%E9%94%99%EF%BC%9A%E6%8F%90%E7%A4%BA%E8%BF%9D%E5%8F%8D%E8%A7%86%E5%9B%BE\[t1_v\]CHECK%20%E7%BA%A6%E6%9D%9F)
  * [BLOB 如何转换成可视化字符串](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#BLOB%20%E5%A6%82%E4%BD%95%E8%BD%AC%E6%8D%A2%E6%88%90%E5%8F%AF%E8%A7%86%E5%8C%96%E5%AD%97%E7%AC%A6%E4%B8%B2)
  * [多字段组合唯一性约束限制](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E5%A4%9A%E5%AD%97%E6%AE%B5%E7%BB%84%E5%90%88%E5%94%AF%E4%B8%80%E6%80%A7%E7%BA%A6%E6%9D%9F%E9%99%90%E5%88%B6)
  * [如何查询出 varchar 字段中包含的非数字数据](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E5%A6%82%E4%BD%95%E6%9F%A5%E8%AF%A2%E5%87%BA%20varchar%20%E5%AD%97%E6%AE%B5%E4%B8%AD%E5%8C%85%E5%90%AB%E7%9A%84%E9%9D%9E%E6%95%B0%E5%AD%97%E6%95%B0%E6%8D%AE)
  * [如何设置部分用户禁止删除某张数据表](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E5%A6%82%E4%BD%95%E8%AE%BE%E7%BD%AE%E9%83%A8%E5%88%86%E7%94%A8%E6%88%B7%E7%A6%81%E6%AD%A2%E5%88%A0%E9%99%A4%E6%9F%90%E5%BC%A0%E6%95%B0%E6%8D%AE%E8%A1%A8)
  * [使用 to_date 函数进行数据类型转换过程中出现报错：非法的时间日期类型数据](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E4%BD%BF%E7%94%A8%20to_date%20%E5%87%BD%E6%95%B0%E8%BF%9B%E8%A1%8C%E6%95%B0%E6%8D%AE%E7%B1%BB%E5%9E%8B%E8%BD%AC%E6%8D%A2%E8%BF%87%E7%A8%8B%E4%B8%AD%E5%87%BA%E7%8E%B0%E6%8A%A5%E9%94%99%EF%BC%9A%E9%9D%9E%E6%B3%95%E7%9A%84%E6%97%B6%E9%97%B4%E6%97%A5%E6%9C%9F%E7%B1%BB%E5%9E%8B%E6%95%B0%E6%8D%AE)
  * [MySQL 迁移到 DM 的存储过程运算结果与原库不一致](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#MySQL%20%E8%BF%81%E7%A7%BB%E5%88%B0%20DM%20%E7%9A%84%E5%AD%98%E5%82%A8%E8%BF%87%E7%A8%8B%E8%BF%90%E7%AE%97%E7%BB%93%E6%9E%9C%E4%B8%8E%E5%8E%9F%E5%BA%93%E4%B8%8D%E4%B8%80%E8%87%B4)
  * [创建索引后，在管理工具导航栏索引下看不到索引信息](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E5%88%9B%E5%BB%BA%E7%B4%A2%E5%BC%95%E5%90%8E%EF%BC%8C%E5%9C%A8%E7%AE%A1%E7%90%86%E5%B7%A5%E5%85%B7%E5%AF%BC%E8%88%AA%E6%A0%8F%E7%B4%A2%E5%BC%95%E4%B8%8B%E7%9C%8B%E4%B8%8D%E5%88%B0%E7%B4%A2%E5%BC%95%E4%BF%A1%E6%81%AF)
  * [count 查询某张表显示为空，但是 select * from 查询某张表是有数据的](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#count%20%E6%9F%A5%E8%AF%A2%E6%9F%90%E5%BC%A0%E8%A1%A8%E6%98%BE%E7%A4%BA%E4%B8%BA%E7%A9%BA%EF%BC%8C%E4%BD%86%E6%98%AF%20select%20*%20from%20%E6%9F%A5%E8%AF%A2%E6%9F%90%E5%BC%A0%E8%A1%A8%E6%98%AF%E6%9C%89%E6%95%B0%E6%8D%AE%E7%9A%84)
  * [查询数据时怎么样可以不带模式名](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E6%9F%A5%E8%AF%A2%E6%95%B0%E6%8D%AE%E6%97%B6%E6%80%8E%E4%B9%88%E6%A0%B7%E5%8F%AF%E4%BB%A5%E4%B8%8D%E5%B8%A6%E6%A8%A1%E5%BC%8F%E5%90%8D)
  * [数据库报错 select 包含过多表](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E6%95%B0%E6%8D%AE%E5%BA%93%E6%8A%A5%E9%94%99%20select%20%E5%8C%85%E5%90%AB%E8%BF%87%E5%A4%9A%E8%A1%A8)
  * [如何通过 SQL_BINDDATA_HISTORY 视图，查看历史 SQL 绑定变量具体的值](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E5%A6%82%E4%BD%95%E9%80%9A%E8%BF%87%20V$SQL_BINDDATA_HISTORY%20%E8%A7%86%E5%9B%BE%EF%BC%8C%E6%9F%A5%E7%9C%8B%E5%8E%86%E5%8F%B2%20SQL%20%E7%BB%91%E5%AE%9A%E5%8F%98%E9%87%8F%E5%85%B7%E4%BD%93%E7%9A%84%E5%80%BC)
  * [如何通过 SQL 获取指定表的定义](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E5%A6%82%E4%BD%95%E9%80%9A%E8%BF%87%20SQL%20%E8%8E%B7%E5%8F%96%E6%8C%87%E5%AE%9A%E8%A1%A8%E7%9A%84%E5%AE%9A%E4%B9%89)
  * [嵌套层次太深](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E5%B5%8C%E5%A5%97%E5%B1%82%E6%AC%A1%E5%A4%AA%E6%B7%B1)
  * [为什么有时候简单的 insert 语句会阻塞查询事务。](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E4%B8%BA%E4%BB%80%E4%B9%88%E6%9C%89%E6%97%B6%E5%80%99%E7%AE%80%E5%8D%95%E7%9A%84%20insert%20%E8%AF%AD%E5%8F%A5%E4%BC%9A%E9%98%BB%E5%A1%9E%E6%9F%A5%E8%AF%A2%E4%BA%8B%E5%8A%A1%E3%80%82)
  * [表数量与实际的表数量不一致](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E8%A1%A8%E6%95%B0%E9%87%8F%E4%B8%8E%E5%AE%9E%E9%99%85%E7%9A%84%E8%A1%A8%E6%95%B0%E9%87%8F%E4%B8%8D%E4%B8%80%E8%87%B4)
  * [如何通过地图上两个点的经纬度计算距离](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E5%A6%82%E4%BD%95%E9%80%9A%E8%BF%87%E5%9C%B0%E5%9B%BE%E4%B8%8A%E4%B8%A4%E4%B8%AA%E7%82%B9%E7%9A%84%E7%BB%8F%E7%BA%AC%E5%BA%A6%E8%AE%A1%E7%AE%97%E8%B7%9D%E7%A6%BB)
  * [dblink 访问远程库中的大字段表数据时报错：字符串截断](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#dblink%20%E8%AE%BF%E9%97%AE%E8%BF%9C%E7%A8%8B%E5%BA%93%E4%B8%AD%E7%9A%84%E5%A4%A7%E5%AD%97%E6%AE%B5%E8%A1%A8%E6%95%B0%E6%8D%AE%E6%97%B6%E6%8A%A5%E9%94%99%EF%BC%9A%E5%AD%97%E7%AC%A6%E4%B8%B2%E6%88%AA%E6%96%AD)
  * [如何不退出 disql 使用代理功能](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E5%A6%82%E4%BD%95%E4%B8%8D%E9%80%80%E5%87%BA%20disql%20%E4%BD%BF%E7%94%A8%E4%BB%A3%E7%90%86%E5%8A%9F%E8%83%BD)
  * [如何通过 sql 查询表的分布类型、分布列、以及创建时间](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E5%A6%82%E4%BD%95%E9%80%9A%E8%BF%87%20sql%20%E6%9F%A5%E8%AF%A2%E8%A1%A8%E7%9A%84%E5%88%86%E5%B8%83%E7%B1%BB%E5%9E%8B%E3%80%81%E5%88%86%E5%B8%83%E5%88%97%E3%80%81%E4%BB%A5%E5%8F%8A%E5%88%9B%E5%BB%BA%E6%97%B6%E9%97%B4)
  * [达梦等价实现 MySQL 的列转行拼接功能](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E8%BE%BE%E6%A2%A6%E7%AD%89%E4%BB%B7%E5%AE%9E%E7%8E%B0%20MySQL%20%E7%9A%84%E5%88%97%E8%BD%AC%E8%A1%8C%E6%8B%BC%E6%8E%A5%E5%8A%9F%E8%83%BD)
  * [如何并行创建分区表上的索引](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E5%A6%82%E4%BD%95%E5%B9%B6%E8%A1%8C%E5%88%9B%E5%BB%BA%E5%88%86%E5%8C%BA%E8%A1%A8%E4%B8%8A%E7%9A%84%E7%B4%A2%E5%BC%95)
  * [达梦是否支持 Oracle 中的 userenv() 函数？](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E8%BE%BE%E6%A2%A6%E6%98%AF%E5%90%A6%E6%94%AF%E6%8C%81%20Oracle%20%E4%B8%AD%E7%9A%84%20userenv\(\)%20%E5%87%BD%E6%95%B0%EF%BC%9F)
  * [对象视图存在，但查询报错：​"指定的对象数据库中不存在​"](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E5%AF%B9%E8%B1%A1%E8%A7%86%E5%9B%BE%E5%AD%98%E5%9C%A8%EF%BC%8C%E4%BD%86%E6%9F%A5%E8%AF%A2%E6%8A%A5%E9%94%99%EF%BC%9A%22%E6%8C%87%E5%AE%9A%E7%9A%84%E5%AF%B9%E8%B1%A1%E6%95%B0%E6%8D%AE%E5%BA%93%E4%B8%AD%E4%B8%8D%E5%AD%98%E5%9C%A8%22)
  * [当前用户无法调用其他用户创建的存储函数/存储过程/包](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E5%BD%93%E5%89%8D%E7%94%A8%E6%88%B7%E6%97%A0%E6%B3%95%E8%B0%83%E7%94%A8%E5%85%B6%E4%BB%96%E7%94%A8%E6%88%B7%E5%88%9B%E5%BB%BA%E7%9A%84%E5%AD%98%E5%82%A8%E5%87%BD%E6%95%B0/%E5%AD%98%E5%82%A8%E8%BF%87%E7%A8%8B/%E5%8C%85)
  * [disql 如何同时给多条 sql 赋予变量值？](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#disql%20%E5%A6%82%E4%BD%95%E5%90%8C%E6%97%B6%E7%BB%99%E5%A4%9A%E6%9D%A1%20sql%20%E8%B5%8B%E4%BA%88%E5%8F%98%E9%87%8F%E5%80%BC%EF%BC%9F)
  * [如何查询列存储分区表的单列使用大小](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E5%A6%82%E4%BD%95%E6%9F%A5%E8%AF%A2%E5%88%97%E5%AD%98%E5%82%A8%E5%88%86%E5%8C%BA%E8%A1%A8%E7%9A%84%E5%8D%95%E5%88%97%E4%BD%BF%E7%94%A8%E5%A4%A7%E5%B0%8F)
  * [如何查询列存储分区表的物理占用空间大小](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E5%A6%82%E4%BD%95%E6%9F%A5%E8%AF%A2%E5%88%97%E5%AD%98%E5%82%A8%E5%88%86%E5%8C%BA%E8%A1%A8%E7%9A%84%E7%89%A9%E7%90%86%E5%8D%A0%E7%94%A8%E7%A9%BA%E9%97%B4%E5%A4%A7%E5%B0%8F)
  * [达梦数据库有没有用于分割字符串的函数？](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E8%BE%BE%E6%A2%A6%E6%95%B0%E6%8D%AE%E5%BA%93%E6%9C%89%E6%B2%A1%E6%9C%89%E7%94%A8%E4%BA%8E%E5%88%86%E5%89%B2%E5%AD%97%E7%AC%A6%E4%B8%B2%E7%9A%84%E5%87%BD%E6%95%B0%EF%BC%9F)
  * [如何从动态 SQL 中返回新记录 ROWID 的值](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E5%A6%82%E4%BD%95%E4%BB%8E%E5%8A%A8%E6%80%81%20SQL%20%E4%B8%AD%E8%BF%94%E5%9B%9E%E6%96%B0%E8%AE%B0%E5%BD%95%20ROWID%20%E7%9A%84%E5%80%BC)
  * [使用 length（）查询 blob 列字节长度时报错：“参数不兼容”](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E4%BD%BF%E7%94%A8%20length%EF%BC%88%EF%BC%89%E6%9F%A5%E8%AF%A2%20blob%20%E5%88%97%E5%AD%97%E8%8A%82%E9%95%BF%E5%BA%A6%E6%97%B6%E6%8A%A5%E9%94%99%EF%BC%9A%E2%80%9C%E5%8F%82%E6%95%B0%E4%B8%8D%E5%85%BC%E5%AE%B9%E2%80%9D)
  * [如何通过 sql 语句更改单列 blob 图片](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E5%A6%82%E4%BD%95%E9%80%9A%E8%BF%87%20sql%20%E8%AF%AD%E5%8F%A5%E6%9B%B4%E6%94%B9%E5%8D%95%E5%88%97%20blob%20%E5%9B%BE%E7%89%87)
  * [表数据分组拆分合并，同时列转多行](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E8%A1%A8%E6%95%B0%E6%8D%AE%E5%88%86%E7%BB%84%E6%8B%86%E5%88%86%E5%90%88%E5%B9%B6%EF%BC%8C%E5%90%8C%E6%97%B6%E5%88%97%E8%BD%AC%E5%A4%9A%E8%A1%8C)
  * [通过数据库将中文转换为拼音](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E9%80%9A%E8%BF%87%E6%95%B0%E6%8D%AE%E5%BA%93%E5%B0%86%E4%B8%AD%E6%96%87%E8%BD%AC%E6%8D%A2%E4%B8%BA%E6%8B%BC%E9%9F%B3)
  * [如何通过数据库生成未来一段时段的年月数据库](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E5%A6%82%E4%BD%95%E9%80%9A%E8%BF%87%E6%95%B0%E6%8D%AE%E5%BA%93%E7%94%9F%E6%88%90%E6%9C%AA%E6%9D%A5%E4%B8%80%E6%AE%B5%E6%97%B6%E6%AE%B5%E7%9A%84%E5%B9%B4%E6%9C%88%E6%95%B0%E6%8D%AE%E5%BA%93)
  * [使用 sql 复制表结构无法将主键和约束复制到新表](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E4%BD%BF%E7%94%A8%20sql%20%E5%A4%8D%E5%88%B6%E8%A1%A8%E7%BB%93%E6%9E%84%E6%97%A0%E6%B3%95%E5%B0%86%E4%B8%BB%E9%94%AE%E5%92%8C%E7%BA%A6%E6%9D%9F%E5%A4%8D%E5%88%B6%E5%88%B0%E6%96%B0%E8%A1%A8)
  * [如何将 16 进制数据转换为 10 进制或者将 10 进制数据转换为 16 进制](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E5%A6%82%E4%BD%95%E5%B0%86%2016%20%E8%BF%9B%E5%88%B6%E6%95%B0%E6%8D%AE%E8%BD%AC%E6%8D%A2%E4%B8%BA%2010%20%E8%BF%9B%E5%88%B6%E6%88%96%E8%80%85%E5%B0%86%2010%20%E8%BF%9B%E5%88%B6%E6%95%B0%E6%8D%AE%E8%BD%AC%E6%8D%A2%E4%B8%BA%2016%20%E8%BF%9B%E5%88%B6)
  * [空间数据如何调用 ST_WKT 将几何类型数据通过文本方式表示出来](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E7%A9%BA%E9%97%B4%E6%95%B0%E6%8D%AE%E5%A6%82%E4%BD%95%E8%B0%83%E7%94%A8%20ST_WKT%20%E5%B0%86%E5%87%A0%E4%BD%95%E7%B1%BB%E5%9E%8B%E6%95%B0%E6%8D%AE%E9%80%9A%E8%BF%87%E6%96%87%E6%9C%AC%E6%96%B9%E5%BC%8F%E8%A1%A8%E7%A4%BA%E5%87%BA%E6%9D%A5)
  * [使用达梦管理工具创建自增列表后，如何获取上一次 insert 语句的 id](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E4%BD%BF%E7%94%A8%E8%BE%BE%E6%A2%A6%E7%AE%A1%E7%90%86%E5%B7%A5%E5%85%B7%E5%88%9B%E5%BB%BA%E8%87%AA%E5%A2%9E%E5%88%97%E8%A1%A8%E5%90%8E%EF%BC%8C%E5%A6%82%E4%BD%95%E8%8E%B7%E5%8F%96%E4%B8%8A%E4%B8%80%E6%AC%A1%20insert%20%E8%AF%AD%E5%8F%A5%E7%9A%84%20id)
  * [管道表函数调用方法](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E7%AE%A1%E9%81%93%E8%A1%A8%E5%87%BD%E6%95%B0%E8%B0%83%E7%94%A8%E6%96%B9%E6%B3%95)
  * [SQL 无法查询到对应数据](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#SQL%20%E6%97%A0%E6%B3%95%E6%9F%A5%E8%AF%A2%E5%88%B0%E5%AF%B9%E5%BA%94%E6%95%B0%E6%8D%AE)
  * [存储过程报错：“数据类型不匹配”](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E5%AD%98%E5%82%A8%E8%BF%87%E7%A8%8B%E6%8A%A5%E9%94%99%EF%BC%9A%E2%80%9C%E6%95%B0%E6%8D%AE%E7%B1%BB%E5%9E%8B%E4%B8%8D%E5%8C%B9%E9%85%8D%E2%80%9D)
  * [达梦 like 模糊匹配可以走索引吗？](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E8%BE%BE%E6%A2%A6%20like%20%E6%A8%A1%E7%B3%8A%E5%8C%B9%E9%85%8D%E5%8F%AF%E4%BB%A5%E8%B5%B0%E7%B4%A2%E5%BC%95%E5%90%97%EF%BC%9F)
  * [在达梦数据库中如何替代 sql server 中 for xml path 功能的函数？](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E5%9C%A8%E8%BE%BE%E6%A2%A6%E6%95%B0%E6%8D%AE%E5%BA%93%E4%B8%AD%E5%A6%82%E4%BD%95%E6%9B%BF%E4%BB%A3%20sql%20server%20%E4%B8%AD%20for%20xml%20path%20%E5%8A%9F%E8%83%BD%E7%9A%84%E5%87%BD%E6%95%B0%EF%BC%9F)
  * [含有全文索引的表执行 SQL 查询报错：“-6803: 非法的参数数据“](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E5%90%AB%E6%9C%89%E5%85%A8%E6%96%87%E7%B4%A2%E5%BC%95%E7%9A%84%E8%A1%A8%E6%89%A7%E8%A1%8C%20SQL%20%E6%9F%A5%E8%AF%A2%E6%8A%A5%E9%94%99%EF%BC%9A%E2%80%9C-6803:%20%E9%9D%9E%E6%B3%95%E7%9A%84%E5%8F%82%E6%95%B0%E6%95%B0%E6%8D%AE%E2%80%9C)
  * [如何通过表名查询表上对应索引定义](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E5%A6%82%E4%BD%95%E9%80%9A%E8%BF%87%E8%A1%A8%E5%90%8D%E6%9F%A5%E8%AF%A2%E8%A1%A8%E4%B8%8A%E5%AF%B9%E5%BA%94%E7%B4%A2%E5%BC%95%E5%AE%9A%E4%B9%89)
  * [如何通过语句统计库中的普通索引和全局索引](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E5%A6%82%E4%BD%95%E9%80%9A%E8%BF%87%E8%AF%AD%E5%8F%A5%E7%BB%9F%E8%AE%A1%E5%BA%93%E4%B8%AD%E7%9A%84%E6%99%AE%E9%80%9A%E7%B4%A2%E5%BC%95%E5%92%8C%E5%85%A8%E5%B1%80%E7%B4%A2%E5%BC%95)
  * [DM 可以类似 mysql 中 json 数据能够通过虚拟列实现添加索引吗](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#DM%20%E5%8F%AF%E4%BB%A5%E7%B1%BB%E4%BC%BC%20mysql%20%E4%B8%AD%20json%20%E6%95%B0%E6%8D%AE%E8%83%BD%E5%A4%9F%E9%80%9A%E8%BF%87%E8%99%9A%E6%8B%9F%E5%88%97%E5%AE%9E%E7%8E%B0%E6%B7%BB%E5%8A%A0%E7%B4%A2%E5%BC%95%E5%90%97)
  * [时间范围分区表，插入数据扩展分区报错](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E6%97%B6%E9%97%B4%E8%8C%83%E5%9B%B4%E5%88%86%E5%8C%BA%E8%A1%A8%EF%BC%8C%E6%8F%92%E5%85%A5%E6%95%B0%E6%8D%AE%E6%89%A9%E5%B1%95%E5%88%86%E5%8C%BA%E6%8A%A5%E9%94%99)
  * [DM8 如何创建并使用空间数据类型](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#DM8%20%E5%A6%82%E4%BD%95%E5%88%9B%E5%BB%BA%E5%B9%B6%E4%BD%BF%E7%94%A8%E7%A9%BA%E9%97%B4%E6%95%B0%E6%8D%AE%E7%B1%BB%E5%9E%8B)
  * [在达梦数据库中如何创建对象数组类型的 TYPE](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E5%9C%A8%E8%BE%BE%E6%A2%A6%E6%95%B0%E6%8D%AE%E5%BA%93%E4%B8%AD%E5%A6%82%E4%BD%95%E5%88%9B%E5%BB%BA%E5%AF%B9%E8%B1%A1%E6%95%B0%E7%BB%84%E7%B1%BB%E5%9E%8B%E7%9A%84%20TYPE)
  * [主键为自增列，插入 0 后无法通过主键查询到数据](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E4%B8%BB%E9%94%AE%E4%B8%BA%E8%87%AA%E5%A2%9E%E5%88%97%EF%BC%8C%E6%8F%92%E5%85%A5%200%20%E5%90%8E%E6%97%A0%E6%B3%95%E9%80%9A%E8%BF%87%E4%B8%BB%E9%94%AE%E6%9F%A5%E8%AF%A2%E5%88%B0%E6%95%B0%E6%8D%AE)
  * [如何像 MySQL 一样查询所有表的表数据行数？如何统计表占用空间大小？](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E5%A6%82%E4%BD%95%E5%83%8F%20MySQL%20%E4%B8%80%E6%A0%B7%E6%9F%A5%E8%AF%A2%E6%89%80%E6%9C%89%E8%A1%A8%E7%9A%84%E8%A1%A8%E6%95%B0%E6%8D%AE%E8%A1%8C%E6%95%B0%EF%BC%9F%E5%A6%82%E4%BD%95%E7%BB%9F%E8%AE%A1%E8%A1%A8%E5%8D%A0%E7%94%A8%E7%A9%BA%E9%97%B4%E5%A4%A7%E5%B0%8F%EF%BC%9F)
  * [使用 for update 语法报错：此查询表达式不允许 FOR UPDATE](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E4%BD%BF%E7%94%A8%20for%20update%20%E8%AF%AD%E6%B3%95%E6%8A%A5%E9%94%99%EF%BC%9A%E6%AD%A4%E6%9F%A5%E8%AF%A2%E8%A1%A8%E8%BE%BE%E5%BC%8F%E4%B8%8D%E5%85%81%E8%AE%B8%20FOR%20UPDATE)
  * [如何去除 char 类型数据后的空格](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E5%A6%82%E4%BD%95%E5%8E%BB%E9%99%A4%20char%20%E7%B1%BB%E5%9E%8B%E6%95%B0%E6%8D%AE%E5%90%8E%E7%9A%84%E7%A9%BA%E6%A0%BC)
  * [使用 dblink 批量插入远程表时报错：“超出保存点上限”](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E4%BD%BF%E7%94%A8%20dblink%20%E6%89%B9%E9%87%8F%E6%8F%92%E5%85%A5%E8%BF%9C%E7%A8%8B%E8%A1%A8%E6%97%B6%E6%8A%A5%E9%94%99%EF%BC%9A%E2%80%9C%E8%B6%85%E5%87%BA%E4%BF%9D%E5%AD%98%E7%82%B9%E4%B8%8A%E9%99%90%E2%80%9D)
  * [如何给表添加虚拟列](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E5%A6%82%E4%BD%95%E7%BB%99%E8%A1%A8%E6%B7%BB%E5%8A%A0%E8%99%9A%E6%8B%9F%E5%88%97)
  * [删除 varray 集合指定下标元素报错“-7088 非法的对象方法序号”](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E5%88%A0%E9%99%A4%20varray%20%E9%9B%86%E5%90%88%E6%8C%87%E5%AE%9A%E4%B8%8B%E6%A0%87%E5%85%83%E7%B4%A0%E6%8A%A5%E9%94%99%E2%80%9C-7088%20%E9%9D%9E%E6%B3%95%E7%9A%84%E5%AF%B9%E8%B1%A1%E6%96%B9%E6%B3%95%E5%BA%8F%E5%8F%B7%E2%80%9D)
  * [如何使用 extract 函数将两个 timestamp 字段相减](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E5%A6%82%E4%BD%95%E4%BD%BF%E7%94%A8%20extract%20%E5%87%BD%E6%95%B0%E5%B0%86%E4%B8%A4%E4%B8%AA%20timestamp%20%E5%AD%97%E6%AE%B5%E7%9B%B8%E5%87%8F)
  * [执行 sql 报错“ -7141: 方法所属对象未初始化”](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E6%89%A7%E8%A1%8C%20sql%20%E6%8A%A5%E9%94%99%E2%80%9C%20-7141:%20%E6%96%B9%E6%B3%95%E6%89%80%E5%B1%9E%E5%AF%B9%E8%B1%A1%E6%9C%AA%E5%88%9D%E5%A7%8B%E5%8C%96%E2%80%9D)
  * [使用 decode 函数报错：“-6111: 字符串转换出错”](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E4%BD%BF%E7%94%A8%20decode%20%E5%87%BD%E6%95%B0%E6%8A%A5%E9%94%99%EF%BC%9A%E2%80%9C-6111:%20%E5%AD%97%E7%AC%A6%E4%B8%B2%E8%BD%AC%E6%8D%A2%E5%87%BA%E9%94%99%E2%80%9D)
  * [使用 SQL 语句进行列排序时汉字未按拼音首字母进行排序](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E4%BD%BF%E7%94%A8%20SQL%20%E8%AF%AD%E5%8F%A5%E8%BF%9B%E8%A1%8C%E5%88%97%E6%8E%92%E5%BA%8F%E6%97%B6%E6%B1%89%E5%AD%97%E6%9C%AA%E6%8C%89%E6%8B%BC%E9%9F%B3%E9%A6%96%E5%AD%97%E6%AF%8D%E8%BF%9B%E8%A1%8C%E6%8E%92%E5%BA%8F)
  * [间隔分区表使用方法](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E9%97%B4%E9%9A%94%E5%88%86%E5%8C%BA%E8%A1%A8%E4%BD%BF%E7%94%A8%E6%96%B9%E6%B3%95)
  * [to_date(​'12-DEC-2024​',​'DD-MON-YYYY​') 执行报错](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#to_date\('12-DEC-2024','DD-MON-YYYY'\)%20%E6%89%A7%E8%A1%8C%E6%8A%A5%E9%94%99)
  * [power 函数计算大数值时有误差](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#power%20%E5%87%BD%E6%95%B0%E8%AE%A1%E7%AE%97%E5%A4%A7%E6%95%B0%E5%80%BC%E6%97%B6%E6%9C%89%E8%AF%AF%E5%B7%AE)
  * [-6105:数据类型不匹配](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#-6105:%E6%95%B0%E6%8D%AE%E7%B1%BB%E5%9E%8B%E4%B8%8D%E5%8C%B9%E9%85%8D)
  * [如何通过 SQL 快速获取表、注释、索引定义](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E5%A6%82%E4%BD%95%E9%80%9A%E8%BF%87%20SQL%20%E5%BF%AB%E9%80%9F%E8%8E%B7%E5%8F%96%E8%A1%A8%E3%80%81%E6%B3%A8%E9%87%8A%E3%80%81%E7%B4%A2%E5%BC%95%E5%AE%9A%E4%B9%89)
  * [DBLINK 查询报错：“DBLINK 大字段缓存空间不足”](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#DBLINK%20%E6%9F%A5%E8%AF%A2%E6%8A%A5%E9%94%99%EF%BC%9A%E2%80%9CDBLINK%20%E5%A4%A7%E5%AD%97%E6%AE%B5%E7%BC%93%E5%AD%98%E7%A9%BA%E9%97%B4%E4%B8%8D%E8%B6%B3%E2%80%9D)
  * [存储过程中通过 scope_identity 获取不到自增列的最大值](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E5%AD%98%E5%82%A8%E8%BF%87%E7%A8%8B%E4%B8%AD%E9%80%9A%E8%BF%87%20scope_identity%20%E8%8E%B7%E5%8F%96%E4%B8%8D%E5%88%B0%E8%87%AA%E5%A2%9E%E5%88%97%E7%9A%84%E6%9C%80%E5%A4%A7%E5%80%BC)
  * [修改表列精度报错：“Cannot modify col with context index”](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sql-gramm.html#%E4%BF%AE%E6%94%B9%E8%A1%A8%E5%88%97%E7%B2%BE%E5%BA%A6%E6%8A%A5%E9%94%99%EF%BC%9A%E2%80%9CCannot%20modify%20col%20with%20context%20index%E2%80%9D)



## 正文

## between and 怎么使用

【问题描述】

达梦数据库中 between......and...... 如何使用，and 前后范围是否包含。

【问题解决】

在 between......and....... 的条件查询中，查询范围既包括 and 前的条件又包括 and 后的条件，如下：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231016163052ZDC1B9BHP1MLNWRO0P)

如果需要了解更多 SQL 使用方法及详细说明可参考数据安装目录下 doc 目录中的《DM8_SQL 语言使用手册》。

## 如何设置成主键后，生成默认自增

DM 数据库也可以实现这种方式，只是和 MySQL 的操作方式不一样而已。举例如下：

```
CREATE TABLE "TAB_12"
(
"ID" INT IDENTITY (1, 1) NOT NULL,
"NAME" VARCHAR(10),
NOT CLUSTER PRIMARY KEY("ID")) ;
```

![查询结果](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-002.png)

![查询结果](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-003.png)

## 小写调用存储过程报错

这个是数据库大小写敏感的问题。数据库在初始化的时候选择是否大小写敏感，默认是大小写敏感。这时执行的 SQL 在到解释器那层解析时，会自动转换为大写名称。举例如下：

```
create or REPLACE PROCEDURE p_test as
V_BACKUP_DATE varchar;
begin
V_BACKUP_DATE := TO_CHAR(SYSDATE,'YYYY_MM_DD_HH24_MI_SS');
print V_BACKUP_DATE;
end;
```

![执行结果日志](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-004.png)

而以下语句将报错：

```
create or REPLACE PROCEDURE "p_test1" as
V_BACKUP_DATE varchar;
begin
V_BACKUP_DATE := TO_CHAR(SYSDATE,'YYYY_MM_DD_HH24_MI_SS');
print V_BACKUP_DATE;
end;
```

![执行出错日志](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-005.png)

因为执行 call ``p_test1`()` 时，数据库解释器自动换行成 CALL ``P_TEST1`()`，而对象 `P_TEST1` 在数据库中是不存在的，数据库中存的是 `p_test1` 这个。可以加上 `“”` 再执行，执行结果如下图所示：

![执行成功日志](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-006.png)

## DM 中按姓氏笔画排序用什么函数

可以使用 `NLSSORT 函数` 实现此功能，NLSSORT 支持以拼音、笔画、部首排序，示例如下：

```
拼音：SELECT * FROM PEOPLE ORDER BY NLSSORT(name,'NLS_SORT = SCHINESE_PINYIN_M');
笔划：SELECT * FROM PEOPLE ORDER BY NLSSORT(name,'NLS_SORT = SCHINESE_STROKE_M');
部首：SELECT * FROM PEOPLE ORDER BY NLSSORT(name,'NLS_SORT = SCHINESE_RADICAL_M');
```

## 有类似 Oracle 的 REDO 日志的文件吗，如何语句查询

DM 有 REDO 日志，默认位置在数据库文件夹内，名称为：数据库名 0X.log。

如 (DAMENG01.log DAMENG02.log) 可以通过如下语句查看 REDO 日志信息。

```
select * from v$rlogfile;
```

关于 REDO 日志的介绍请参考《DM 系统管理手册》第二章-2.4 节内容（手册位于数据库安装路径 `/dmdbms/doc` 文件夹下），节选如下：

  * 重做日志，又叫 REDO 日志，指在 DM 数据库中添加、删除、修改对象，或者改变数据，DM 数据库都会按照特定的格式，将这些操作执行的结果写入到当前的重做日志文件中。重做日志文件以 log 为扩展名。每个 DM 数据库实例必须至少有 2 个重做日志文件，默认两个日志文件为 \`DAMENG01.log、DAMENG02.log\`，这两个文件循环使用。
  * 重做日志文件主要用于数据库的备份与恢复。理想情况下，数据库系统不会用到重做日志文件中的信息。但是如果出现意外情况，例如电源故障、系统故障、介质故障，或者数据库实例进程被强制终止等，数据库缓冲区中的数据页来不及写入数据文件。这样，在重启 DM 实例时，通过重做日志文件中的信息，就可以将数据库的状态恢复到发生意外时的状态。
  * 重做日志文件对于数据库是至关重要的。它们用于存储数据库的事务日志，以便系统在出现系统故障和介质故障时能够进行故障恢复。在 DM 数据库运行过程中，任何修改数据库的操作都会产生重做日志，例如，当一条元组插入到一个表中的时候，插入的结果写入了重做日志，当删除一条元组时，删除该元组的操作也记录在日志内，这样，当系统出现故障时，通过分析日志可以知道在故障发生前系统做了哪些动作，并可以重做这些动作使系统恢复到故障之前的状态。



## 字符串截断、超长文本截取

  * 执行 INSERT 时报错



一般此类问题是由于目标表存在一个（或多个）字段长度不够，导致插入失败。重点关注 `CHAR、VARCHAR` 字段的长度，可以创建一个具备足够长度的测试表，将报错数据插入测试表中，通过 `select max(length(“目标字段”)) from “测试表”`，获得插入数据实际的长度信息，从而修改目标表数据或者处理包含超长字段的记录。

另外，注意中文字符的长度问题：数据库初始化参数 `LENGTH_IN_CHAR=0` 时，unicode 编码下一个中文占据 3 个字节 `（char(3)）`；GBK 编码下一个中文占据 2 个字节 `（char(2)）`；当数据库初始化参数 `LENGTH_IN_CHAR=1` 时，`char(1)` 即可存储一个字符。

![Varchar 类型描述](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-007.jpg)

  * 执行查询时报此错误



此类问题常见于分组查询中使用了 `wm_concat()` 之类函数拼接字符串，由于分组数据记录过多导致拼接函数返回值超长，解决方法一般是改写 SQL 查询条件降低分组记录数或者对 SQL 语句进行修改（如改为超长切分）。

此外，clob 字段进行模糊查询时的字符串截断：`Case (clob_column_name as varchar) like`。

  * 数据迁移时报错



一般从 Oracle 迁移到 DM 的时候，出现字符串截断的一般都是字段中含有中文，出现这种问题是因为 DM 初始化的时候选择的字符集是 `Unicode（即 utf-8）`，该字符集的国际标准是一个汉字占 3 个字节，而 Oracle 中默认情况下一个汉字占 2 个字节，此时迁移的时候就会报。

遇到该错误有 3 种解决方法。

  1. 在初始化数据库的时候，字符集选择【gb18030】。
  2. 在初始化数据库的时候，选择【VARCHAR 类型以字符为单位】。
  3. 因为前面 2 种都需要重新初始化数据库，第三种不需要重新初始化数据库即可解决，即在选择迁移方式时，选择字符长度映射关系为 2，如下图所示：



![选择迁移方式-设置字符长度](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-008.jpg)

  4. 也可使用数据库自带的包解决 clob 字段模糊查询的问题。



例如：

```
---执行以下语句报错，其中a1字段为clob类型
select * from test where a1 like '%会议%';

---尝试使用to_char进行转换后模糊查询，报错test类型数据过长
select * from test where to_char(a1) like '%会议%';

---通过数据库自带的包 dbms_lob.instr 处理，解决该问题
select * from test where dbms_lob.instr(a1,'会议',1,1)>0;
```

## to_date(2019/12/31,'YYYY-MM-DD')报：无效的日期格式

【场景复现】：

![执行结果](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-009.jpg)

【解决方式】：

![结果集](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-010.jpg)

【报错原因】：

合法的 DATE 格式为年月日、月日年和日月年三种，各部分之间可以有间隔 `(".","-","/")` 或者没有间隔符；合法的 TIME 格式为：时分和时分秒，间隔符为":"。

详见《DM_SQL 语言手册》第八章-函数 8.3 日期时间函数中第 37 条。（手册位于数据库安装路径 `/dmdbms/doc` 文件夹下）

举例如下：

```
SELECT TO_DATE('2003-06-19 08:40:36','YYYY-MM-DD HH:MI:SS');
```

查询结果如下：

![结果集](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-011.jpg)

## 数据链路在哪里创建

数据链路也就是外部链接，外部链接对象 (LINK) 是 DM 中的一种特殊的数据库实体对象，它记录了远程数据库的连接和路径信息，用于建立与远程数据的联系。用户可以通过外部链接对远程数据库的表进行查询和增删改操作，以及本地调用远程的存储过程。

例如：DM-DM 外部链接的创建

  * 两台服务器，其中一个为目的主机 A，另一个为源端机器 B；分别在这两台服务器上修改 dm.ini 中参数 \`MAL_UTHR_FLAG = 0\`， \`MAL_INI=1\`（开启 MAL 系统），且配置 dmmal.ini （保证源端目的端的 dmmal.ini 文件相同）如下：



```
[MAL_INST1]
MAL_INST_NAME = DMSERVER  #实例名，和 dm.ini 中的 INSTANCE_NAME 一致
MAL_HOST   = 192.168.10.2  #实例所在 IP 地址
MAL_PORT   = 5536  #MAL系统监听 TCP 连接的端口，不是数据库端口号
```

```
[MAL_INST2]
MAL_INST_NAME = DMSERVER1
MAL_HOST   = 192.168.10.3
MAL_PORT   = 5537
```

  * 分别重启 A，B 两台机器上的 DM 服务。
  * 在主机 A 上建表 test：



```
CREATE TABLE TEST(C1 INT,C2 VARCHAR(20));
```

在 B 上建立到 A 的外部链接 LINK01：

```
CREATE PUBLIC LINK LINK01 CONNECT WITH SYSDBA IDENTIFIED BY SYSDBA USING '192.168.10.2/5536';
```

  * 在 B 上使用链接进行插入，更新，删除等操作，说明外部链接创建成功。如下图所示：



![执行结果](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-012.jpg)

> **注意**
>
> 异构外部链接创建步骤请参考《DM_SQL 语言使用手册》第 16 章-外部链接。（手册位于**数据库安装路径** \`/dmdbms/doc\` 文件夹下）



## DM 有没有类似 pl\sql 的对比表结构，比对表数据差异的工具，生成更新脚本，更新到正式环境

DM 数据库目前没有比对工具，可以自己写一个 SQL 进行比对或手动比对。示例如下：

  * 执行如下语句查看表结构信息。



```
SELECT * FROM DBA_TAB_COLUMNS;
```

![结果集](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-013.jpg)

  * 执行如下语句将不同数据库中表结构信息导入至新创建表中，方便进行对比。



```
CREATE TABLE TABLE_2 AS SELECT * FROM DBA_TAB_COLUMNS;
CREATE TABLE TXT_1 AS SELECT * FROM DBA_TAB_COLUMNS;
```

表 TABLE_2 是 DM 数据库实例（端口 5236）中的表结构信息，表 TXT_1 是 DM 数据库实例（端口 5237）中的表结构信息，根据不同实例中的表结构进行比对是否存在不一致的表。

![执行结果](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-014.jpg)

![执行结果](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-015.jpg)

  * 将 TXT_1（端口 5237）表导入到 DM 数据库（端口 5236）中，进行左连接查询，比对是否存在表结构差异。



```
SELECT DISTINCT A.OWNER,A.TABLE_NAME
FROM SYSDBA.TABLE_2 A LEFT JOIN SYSDBA.TXT_1 B
ON A.OWNER|| A.TABLE_NAME = B.OWNER|| B.TABLE_NAME AND B.COLUMN_NAME = A.COLUMN_NAME AND
B.COLUMN_ID=A.COLUMN_ID
WHERE A.OWNER='SYSDBA' AND B.OWNER IS NULL
ORDER BY A.TABLE_NAME;
```

![结果集](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-016.jpg)

## DM 数据库如何获取表的列注释？在代码中如何修改列属性的注释

DM 数据库查询表的列注释语句如下：

```
select * from SYSCOLUMNCOMMENTS;
```

![执行结果](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-017.jpg)

如题所示：用户可以自行条件筛选得到想要的结果集。

列属性的注释：

```
select COMMENTS from user_col_comments where TABLE_NAME='TEST' AND COLUMN_NAME='ID';
```

修改列属性的注释：

```
comment on column "SYSDBA"."TEST"."ID" is 'ttttt';
```

## DM 数据库 VARCHAR2 的长度最大是多少

  * DM 数据库的 VARCHAR2 长度如果指定了 USINGLONG ROW 存储选项，则插入 VARCHAR 数据类型的长度不受数据库页面大小限制。VARCHAR 类型在表达式计算中的长度上限不受页面大小限制，为 32767；



关于 VARCHAR2 长度详细简介详见《DM_SQL 语言使用手册》-1.41 章节。（手册位于数据库安装路径 `/dmdbms/doc` 文件夹下）

  * VARCHAR2 数据类型指定变长字符串，用法类似 CHAR 数据类型，可以指定一个不超过 8188 的正整数作为字符长度，在 DM 数据库中，VARCHAR2 数据类型的实际最大长度由数据库页面大小决定，在建库时指定，之后无法修改。具体最大长度算法如下表所示：



|**数据页面大小**|**实际最大长度**|
|------|------|
|4K|1900|
|8K|3900|
|16K|8000|
|32K|8188|


## DM 支持的字段名最大长度

正规标识符的最大长度是 128 个英文字符或 64 个汉字。

  * 测试：64 个汉字长度字段。



```
CREATE TABLE "SYSDBA"."TABLE_1" --64个汉字字段
(
"一二三四五六七八九十一二三四五六七八九十一二三四五六七八九十一二三四五六七八九十一二三四五六七八九十一二三四五六七八九十一二三四" CHAR(10)) STORAGE(ON "MAIN", CLUSTERBTR);

测试结果：创建成功

CREATE TABLE "SYSDBA"."TABLE_1" --65个汉字字段
(
"一二三四五六七八九十一二三四五六七八九十一二三四五六七八九十一二三四五六七八九十一二三四五六七八九十一二三四五六七八九十一二三四五" CHAR(10))STORAGE(ON "MAIN", CLUSTERBTR
);  

测试结果：失败
```

测试结果：最大长度是 64 个汉字字符。

![执行结果](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-018.jpg)

  * 测试：128 个英文字符长度字段。



![执行结果](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-019.jpg)

测试成功。

  * 测试：129 个英文字符长度字段。



![执行结果](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-020.jpg)

测试结果：最大长度是 128 个英文字符。

## DM 数据库有类似于 Oracle 的 v$sql 的视图吗

DM 数据库没有提供完全一样的系统视图，提供了 `V$SQL_HISTORY` 视图来达到类似的功能。目前 DM 数据库可以兼容 100 多个 Oracle 的系统视图。在《DM 系统管理员手册》中有更多介绍（手册位于数据库安装路径 `/dmdbms/doc` 文件夹下）。

## [不能修改或删除聚集索引的列] 或 [试图删除聚集主键]、[表 xx 中不能同时包含聚集 KEY 和大字段]

  * 情况一



表上某一列上创建有聚集索引，但是该列不是主键列。举例如下：

```
CREATE TABLE TEST_C (A INT, B VARCHAR,
CONSTRAINT C1 CLUSTER KEY (A));
```

或：

```
CREATE TABLE TEST_C1(A INT,B VARCHAR);
CREATE CLUSTER INDEX IDX1 ON TEST_C1(A);
```

这种情况下，在列 A 上有一个聚集索引，如下图所示：

![TEST_C1](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-021.png)

此时若想直接修改列 A 的字段类型或删除列 A，会报错：`不能修改或删除聚集索引的列`。

若想对列 A 进行修改，需要先删除掉 A 上的索引，再做修改。

![执行结果](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-022.png)

  * 情况二



表上某一列为聚集主键，举例如下：

```
CREATE TABLE TEST_PK(A INT PRIMARY KEY,B VARCHAR);
```

![主键](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-023.png)

此时修改或删除列 A，会报错：`不能修改或删除聚集索引的列`；删除表上的主键约束会报错：`试图删除聚集主键`。

在这种情况下，若想对列 A 进行修改，可以重建表，或者重新在别的列上建立一个聚集索引，然后再删掉，此时主键上的索引变成非聚集了，就可以修改了。

要想在一开始建表的时候就指定非聚集主键，有两种方法：

  1. **用语句显式指定；**
  2. **修改 dm.ini 参数 PK_WITH_CLUSTER 为 0，默认创建主键时为非聚集型；**



语句显式指定如下：

```
CREATE TABLE TEST_PK2(A INT,B VARCHAR,
CONSTRAINT C1 NOT CLUSTER PRIMARY KEY (A));
```

![索引](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-024.png)

此时可以对列 A 进行修改与删除，如下图所示：

![执行结果](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-025.png)

![执行结果](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-026.png)

  * 修改 dm.ini 参数的方式



由于 `PK_WITH_CLUSTER` 默认取值为 1，即仅指定 PRIMARY KEY 关键字时默认创建为聚集主键，修改为 0 后默认创建非聚集主键。如下所示：

```
SELECT * FROM V$DM_INI WHERE PARA_NAME = 'PK_WITH_CLUSTER';
```

查询该参数的值可以使用 `SP_SET_PARA_VALUE(1,'PK_WITH_CLUSTER',0)` 语句来将该参数值修改为 0；使用 `SP_SET_PARA_VALUE(1,'PK_WITH_CLUSTER',1)` 语句将该参数值改回 1。

另外，报错 `表 xx 中不能同时包含聚集 KEY 和大字段` 也可以通过将表重建为非聚集型主键的方式来解决。

## DM 数据库如何获取系统时间

DM 数据库提供对应的系统函数获取系统时间，如下所示：

```
--返回值为：DATE
select current_date();
--返回值为：TIMESTAMP(0)
select sysdate();
--返回值为：DATETIME(6) WITH TIME ZONE
select current_timestamp();
```

## DM 是否可以并发创建索引

DM 无法并发创建索引，如下所示：

```
drop table table1;  
create  table table1 (c1 int,c2 VARCHAR2(10),c3 number);
insert  into table1 select level,level,level from dual CONNECT by level <10000;
```

按 Oracle 语法并行创建索引，如下所示：

```
create  index ind_c2 on SYSDBA.TABLE1(c2) parallel 2;
```

报错，如下所示：

```
create  index ind_c2 on SYSDBA.TABLE1(c2) parallel 2;
执行失败(语句1)  [P]  第 1 行, 第 41 列 [parallel] 附近出现错误 [-2007]:

语法分析出错
```

  * DM 并行查询使用方法



修改并行查询相关的 dm.ini 参数，如下所示：

```
select  * from v$dm_ini where para_name
in('MAX_PARALLEL_DEGREE','PARALLEL_POLICY','PARALLEL_THRD_NUM')
```

只有 PARALLEL_POLICY 不为 0 时并行才可以使用（取值：0，1，2），MAX_PARALLEL_DEGREE 指定并行度（取值：1-128）；PARALLEL_THRD_NUM 指定并行线程数（取值：1-1024）；

当 `PARALLEL_POLICY=1` 时（自动并行模式），修改 PARALLEL_POLICY 的值为 1，需要重启数据库生效：

```
sp_set_para_value(2,'PARALLEL_POLICY',1);
```

修改 MAX_PARALLEL_DEGREE 为 10，即时生效：

```
sp_set_para_value(1,'MAX_PARALLEL_DEGREE',10);
```

此时查询默认开启并行，且并行度为 10，当 MAX_PARALLEL_DEGREE 值为 1 时，无并行：

```
sp_set_para_value(1,'MAX_PARALLEL_DEGREE',1);
```

但是此时指定 hint 的 parallel 仍可以开启并行，且指定 hint 优先级高于 MAX_PARALLEL_DEGREE 参数。

当 `PARALLEL_POLICY=2` 时（手动并行模式），修改 PARALLEL_POLICY 的值为 2，需要重启数据库生效：

```
sp_set_para_value(2,'PARALLEL_POLICY',2);
```

此时 MAX_PARALLEL_DEGREE 参数无效，必须指定 hint 的 parallel 才开启并行。如下所示：

```
create  table t_objects as select * from dba_objects;

select  * from t_objects;

执行计划：

1    #NSET2: [0, 1768, 566]
2      #PRJT2: [0, 1768, 566]; exp_num(16), is_atom(FALSE)
3        #CSCN2: [0, 1768, 566]; INDEX33555710(T_OBJECTS)

select  /*+ PARALLEL(2)*/* from t_objects;

执行计划：

1    #NSET2: [0, 1768, 566]
2      #LOCAL COLLECT: [0, 1768, 566]; op_id(1) n_grp_by (0) n_cols(0)  n_keys(0) for_sync(FALSE)
3        #PRJT2: [0, 1768, 566]; exp_num(16), is_atom(FALSE)
4          #CSCN2: [0, 1768, 566]; INDEX33555710(T_OBJECTS)
```

## SQL 执行计划绑定变量和非绑定变量的不一致

  * 检查是否赋值有数据类型转换，导致不走索引。举例说明如下：



```
create  table table1 (c1 int,c2  VARCHAR2(10),c3 number);
insert into table1 select  level,level,level from dual CONNECT by level <10000;
create index ind_c2 on  SYSDBA.TABLE1(c2);
```

```
select * from SYSDBA.TABLE1 where  TABLE1.C2=?

执行计划：（走 IND_C2 索引）

1  #NSET2: [0, 249,  90]
2   #PRJT2: [0, 249,  90]; exp_num(4), is_atom(FALSE)
3    #BLKUP2:  [0, 249, 90]; IND_C2(TABLE1)
4       #SSEK2: [0, 249, 90]; scan_type(ASC), IND_C2(TABLE1),scan_range[exp_param(no:0),exp_param(no:0)]
```

```
select * from SYSDBA.TABLE1 where  TABLE1.C2=5

执行计划：（有数据类型转换，不走 IND_C2 索引）

1  #NSET2: [1, 499,  90]
2   #PRJT2: [1, 499,  90]; exp_num(4), is_atom(FALSE)
3    #SLCT2: [1,  499, 90]; exp_cast(TABLE1.C2) = 5
4     #CSCN2:  [1, 9999, 90]; INDEX33555695(TABLE1)
```

```
select * from SYSDBA.TABLE1 where TABLE1.C2='5'

执行计划：（无数据类型转换，走 IND_C2 索引）

1  #NSET2: [0, 249,  90]
2   #PRJT2: [0, 249,  90]; exp_num(4), is_atom(FALSE)
3    #BLKUP2:  [0, 249, 90]; IND_C2(TABLE1)  
4     #SSEK2:  [0, 249, 90]; scan_type(ASC), IND_C2(TABLE1), scan_range['5','5']
```

  * 估算值不同，使用的连接方式、连接顺序会有差异，影响最终的执行计划。



## 有过滤条件，查询时不走索引

需注意数据分布和统计信息，很多时候，统计信息不全会导致优化器计算执行代价的时候算错，误认为走索引会比全表慢，造成执行计划不对。

  * 统计信息的收集方法



某用户下的所有索引：

```
DBMS_STATS.GATHER_SCHEMA_STATS('SSCKF',100,TRUE,'FOR ALL INDEXED SIZE AUTO');
```

某用户下所有字段(包括索引)：

```
DBMS_STATS.GATHER_SCHEMA_STATS('SSCKF',100,TRUE,'FOR ALL COLUMNS SIZE AUTO');
```

某表下的所有字段：

```
DBMS_STATS.GATHER_TABLE_STATS('SSCKF','FO_ACCOUNTBILL',null,100,TRUE,'FOR ALL COLUMNS SIZE AUTO');
```

收集某一列的统计信息：

```
sp_col_stat_init('JXWOA','OA2_RECEIVEDOC','RD_STATE');

sp_col_stat_init('JXWOA','OA2_SENDDOC','SD_STATE');
```

例如有 10 万条测试数据，然后执行查询语句 `SELECT * FROM T WHERE C='A'`；但这 10 万条数据全是一样的，是不可能走索引的。

修改测试数据的脚本，让 C 这个字段的值平均分布在 1~1000 上，做完后收集统计信息，就走索引了。

## 如何修改用户默认表空间

创建用户 TEST 并指定默认表空间为 TBS_TEST，如下所示：

```
create tablespace TBS_TEST datafile  'TBS_TEST.dbf' size 50;

create user TEST identified by “123456789” default tablespace  TBS_TEST;
```

修改用户 TEST 的默认表空间为 TBS_TEST1：

```
create tablespace TBS_TEST1 datafile  'TBS_TEST1.dbf' size 50;

alter user TEST default tablespace  TBS_TEST1;
```

## DM 支持 Oracle 的闪回查询么

闪回开启参数，在 dm.ini 文件中。（生产环境最好不要开闪回功能，慎用）

![参数介绍](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-027.jpg)

管理工具开启闪回功能如下：

![执行结果](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-028.jpg)

管理工具界面闪回按钮如下：

![闪回按钮](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-029.jpg)

![闪回条件](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-030.png)

时刻：指定闪回查询时刻。

事务 ID 号：指定事务 ID 号。

要使用闪回功能实例要开启归档：

  * 在线开启



修改数据库为 Mount 状态，如下所示：

```
SQL ALTER DATABASE MOUNT;
```

配置本地归档，如下所示：

```
SQL ALTER DATABASE ADD ARCHIVELOG 'DEST = /archive, TYPE = local, FILE_SIZE = 1024, SPACE_LIMIT = 0';
//DEST=归档路径  FILE_SIZE：单个归档文件大小。SPACE_LIMIT：预留归档总大小，0 表示没有限制。
```

开启归档模式，如下所示：

```
SQL ALTER DATABASE ARCHIVELOG;
```

修改数据库为 OPEN 状态，如下所示：

```
SQL ALTER DATABASE OPEN;
```

## null 值结果排序前后问题

对可为空的 timestamp 列排序，都是 null 在最前面

创建测试表并初始化数据，如下所示：

```
CREATE TABLE "SYSDBA"."a"
("COLUMN_1" TIMESTAMP(6)) STORAGE(ON "MAIN", CLUSTERBTR);

insert "SYSDBA"."a" values (''),(SYSTIMESTAMP),(SYSTIMESTAMP);
commit;
insert "SYSDBA"."a" values (''),(SYSTIMESTAMP),(SYSTIMESTAMP);
commit;
insert "SYSDBA"."a" values (''),(SYSTIMESTAMP),(SYSTIMESTAMP);
commit;
```

![执行结果](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-031.jpg)

![执行结果](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-032.jpg)

测试结果：可为空的 timestamp 列排序，无论 asc，desc，都是 null 在最前面，解决方法如下：

  * 修改 dm.ini 中 ORDER_BY_NULLS_FLAG 的值为 1；  
这个值默认是 0，意义是  
Whether to place NULL values to the end of the result set when in ascending
order  
修改以后，重启数据库。查询时 order by asc 就发现 null 值在最后了，但是 DESC 降序时该参数无效。

  * 修改 SQL 写法



```
 order by NVL(column , '0') 当column列为null时则指定为0。
 order by column asc nulls first : null值始终放在最前面。
 order bu column desc nulls last : null值始终放在最后面。
```

## Insert values 超过最大参数个数 (2048)

```
insert into xx(id, name ....) values (?,?,?), (?,?,?), (?,?,?)
报错：超过最大参数个数(2048)

insert into FZQ(COLUMN_1) values (1),(2)....(2048);
```

![执行结果](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-033.png)

尝试插入 2049 个报错复现：

```
insert into FZQ(COLUMN_1) values (1),(2)....(4100);
```

![执行结果](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-034.png)

结论： 最大参数不止 2048。

  * insert all INTO 语法改写



批量插入数据 insert all。

```
INTO  A(field_1,field_2) VALUES (value_1,value_2)
INTO  A(field_1,field_2) VALUES (value_3,value_4)
INTO  A(field_1,field_2) VALUES (value_5,value_6)  select  1 from dual;
```

Demo 如下所示：

```
INSERT ALL
INTO FZQ(COLUMN_1) VALUES (1)
INTO FZQ(COLUMN_1) VALUES (2)  .......
INTO FZQ(COLUMN_1) VALUES (4100)
select 1 from dual
```

![执行结果](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-035.png)

## 类似 Oracle 正则表达式 regexp_substr 函数

  * regexp_like 样例测试



REGEXP_LIKE
(
source_string,
pattern
[, match_parameter]
)

```
--创建表并初始化数据
--创建表
create table fzq (  
   id varchar(4),  
   value varchar(10)
);
--插入数据
insert into fzq values ('1','1234560');
insert into fzq values ('2','1234560');
insert into fzq values ('3','1b3b560');
insert into fzq values ('4','abc');
insert into fzq values ('5','abcde');
insert into fzq values ('6','ADREasx');
insert into fzq values ('7','123 45');
insert into fzq values ('8','adc de');
insert into fzq values ('9','adc,.de');
insert into fzq values ('10','1B');
insert into fzq values ('10','abcbvbnb');
insert into fzq values ('11','11114560');
insert into fzq values ('11','11124560');
--提交
commit;
```

```
select * from fzq where regexp_like(value,'1[0-9]{4}60');
```

![查询结果](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-036.jpg)

  * REGEXP_SUBSTR 测试样例



REGEXP_SUBSTR
(
source_string,
pattern
[,position  [,occurrence  [,match_parameter]]]
)

```
SELECT REGEXP_SUBSTR('17,20,23','[^,]+',1,1,'i') AS STR FROM DUAL;
```

![查询结果](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-037.jpg)

  * REGEXP_INSTR 样例测试



REGEXP_INSTR
(
source_string,
pattern
[,start_position
[,occurrence
[,return_option
[,match_parameter]]]]
)

```
SELECT REGEXP_INSTR ('hello itmyhome', 'e')FROM dual;
```

![查询结果](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-038.png)

  * REGEXP_REPLACE 样例测试



REGEXP_REPLACE
(
source_string,
pattern
[,replace_string]
[,position]
[,occurtence]
[,match_parameter]
)

```
select regexp_replace('asdlfj','(.)','\1 ') from dual;
```

![查询结果](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-039.png)

## 对象权限的授权

```
--对象权限的授予

grant select on  ""SYS"".""V$SQL_NODE_NAME"" to ""用户"";
grant select on  ""SYS"".""V$CIPHERS"" to ""用户"";
grant select on  ""SYS"".""V$DATAFILE"" to ""用户"";
```

## USING LONG ROW（启用超长记录）选项的作用

在建表时，字符类型的字段和整条数据的存储长度是受初始化参数 PAGE_SIZE（页大小）限制的，具体关系如下图所示：

![参数信息](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-040.jpg)

实际插入表中的列长度受到记录长度的约束，每条记录总长度不能大于页面大小的一半。例如我们页大小初始化是 32 KB，某表中有 3 个 VARCHAR 类型的字段，那么每个字段最大长度不能操作 8188 字节，且每条记录不能超过页大小（32 KB）的一半，也就是不能超过 16 KB，如果记录超过 16 KB 时，会报“错误号:-2665 错误消息:[P] 记录超长”。

![报错信息](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-041.jpg)

当指定了 USINGLONG ROW 存储选项，则每条记录的长度不受数据库页面大小限制。USINGLONG ROW 选项用法如下：

```
create table "text_to_varchar1"
(
"id" CHAR(8188),
"textcol1" VARCHAR(8188),
"textcol3" VARCHAR(8188),
"textcol4" VARCHAR(8188),
"textcol5" VARCHAR(8188),
"textcol2" VARCHAR(8188)
) STORAGE (USING LONG ROW);
```

## 关于 DM 数据库的数据类型

DM 数据的数据类型请参考如下两个手册的内容（手册位于数据库安装路径 `/dmdbms/doc` 文件夹下。）：

  * 《DM_SQL 语言使用手册》-DM_SQL 所支持的数据类型章节；其中手册中“长度最大为 2G-1 字节”的“-”表示减号。
  * 《DM_SQL 程序设计》-DM_SQL 程序数据类型与操作符章节。



## insert 后如何获取自增 ID

```
IDENT_CURRENT
定义：
int
IDENT_CURRENT (
 fulltablename varchar(8187)
)
功能说明：
获取自增列当前值
参数说明：
fulltablename：表全名；格式为“模式名.表名”
返回值：
自增列当前值
```

举例说明：

```
SELECT IDENT_CURRENT('PRODUCTION.PRODUCT');
```

若为序列作为自增列，则使用 select 模式名.序列名.CURRVAL 获取当前值；select 模式名.序列名.NEXTVAL 获取下一值。

## DM 和其他数据库的兼容性配置

DM 数据库初始化 dm.ini 文档，是否兼容其他数据库模式，如下所示：

```
0：不兼容
1：兼容 SQL92 标准
2：部分兼容 Oracle
3：部分兼容 MS SQL SERVER
4：部分兼容 MySQL
5：兼容 DM6
6：部分兼容 TERADATA
```

## Oracle 和 DM 数据类型对比表

|**Oracle**|****|**DM**|****|****|
|------|------|------|------|------|
|类型名|说明|类型名|说明|备注|
|CHAR (n)|定长字符串，最大长度 2000，长度不足 n 时，自动填充空格|CHAR (n)、CHARACTER (n)|定长字符串，最大长度 8000（页大小为 16 KB 或以上时），长度不足 n 时，自动填充空格|DM 允许的串最大长度可通过设置页大小参数分别调整为 1900，3900，8000，16200 之一。|
|VARCHAR (n)  VARCHAR2 (n)|可变长字符串，最大长度 4000|VARCHAR (n)、  VARCHAR2 (n)|可变长字符串，最大长度 8000（页大小为 16 KB 或以上时）||
|LONG|可变长文本，最大长度 134217727，表中只能有一个 LONG 列|TEXT、LONGVARCHAR|长文本，最大长度 2G-1，表中可有多个 TEXT/LONGVARCHAR 列|可用于存储较长的非格式化文本，文本较小时可考虑用 Oracle 的 VARCHAR2 或 DM 的 VARCHAR|
|NUMBER|可变长数值列，最大数值长度为 38 个数字，NUMBER 是 Oracle 特有的非标准数据类型|BIT、BOOLEAN|BIT 类型用于存储整数数据 1、0 或 NULL。BOOLEAN 类型的字值可以取常量 TRUE 和 FALSE，或者数值 1 和 0|这些数据类型都不带精度描述。DM 的 BIT 类型与 SQL Server 2000 的 BIT 数据类型相似。|
|BYTE、  TINYINT|1 字节存储的整数，精度 3，标度 0|BIT、BOOLEAN 都可以用来支持 ODBC JDBC 的布尔数据类型，MONEY 类型便于存储货币数据。|||
|SMALLINT|2 字节存储的整数，精度 5，标度 0||||
|INT、  INTEGER|4 字节存储的整数，精度 10，标度 0||||
|BIGINT|8 字节存储的长整数，精度 19，标度 0||||
|DEC、  DECIMAL、  NUMERIC、  NUMBER|定点数，精度 16，标度 0||||
|MONEY|定点数，精度 19，标度 4||||
|NUMBER (m,n)|可变长数值列，最大数值长度为 38 个数字|DECIMAL (m,n)、  NUMBER (m,n)、NUMERIC (m,n)||这些数据类型都带精度描述。|
|FLOAT|浮点数，精度 126|FLOAT、DOUBLE、DOUBLE PRECISION|浮点数，最大二进制精度 53，十进制精度 15||
|REAL|实数类型，NUMBER (63)，精度更高|REAL|浮点数，二进制精度 24，十进制精度 7||
|(LONG) RAW|可变长二进制数据  最大长度 134217727，表中只能有一个 LONG RAW 列|BINARY (n)|定长二进制数据，缺省长度 1，最大长度同 CHAR，长度不足 n 时，自动填充 0||
|VARBINARY (n)|可变长二进制数据，缺省长度 1，最大长度同 CHAR||||
|IMAGE、LONGVARBINARY|可变长的长二进制数据，最大长度 2G-1，表中可有多个 IMAGE/ LONGVARBINARY 列|可用于存储多媒体图像、WORD 文档等，长度较小时可考虑用 DM 的 VARBINARY|||
|BLOB (n)|可变长二进制大对象，最大长度 4G-1|BLOB (n)|可变长二进制大对象，最大长度 100G-1||
|CLOB (n)|可变长二进制大对象，最大长度 4G-1|CLOB (n)|可变长字符串大对象，最大长度 100G-1||
|DATE|固定长度（7 字节）的日期型，时间也作为一部分存储其中。Oracle 的 DATE 是其特有的非标准数据类型。|DATETIME|日期型，包括年、月、日、时分秒信息。数据格式：datetime'2013-11-06 12:23:22'|时间间隔类型 Oracle、DM 均支持（略）|
|TIME|时间型，包括时、分、秒信息。格式：time ’09:10:21’||||
|TIMESTAMP|时间戳型，包括年月日时分秒信息。例如：timestamp ‘1999-07-13 10:11:22’||||
|BFILE|存放在数据库外的二进制数据，最大长度 4 GB|BFILE|存储在操作系统中的二进制文件，文件存储在操作系统而非数据库中，仅能进行只读访问，最大长度 2G-1。||
|NCHAR (n)|根据字符集而定的固定长度字符串，最大长度 2000 bytes|NCHAR (n)|根据字符集确定需要扩展的长度，最大长度 8000（页大小为 16 KB 或以上时）||
|NVARCHAR2|根据字符集而定的可变长度字符串，最大长度 4000 bytes|NVARCHAR2 (n)|根据字符集确定需要扩展的长度，最大长度 8000（页大小为 16 KB 或以上时）||
|ROWID|数据表中记录的唯一行号，10 bytes|VARCHAR (n)、VARCHAR2 (n)|可变长字符串，最大长度 8000（页大小为 16 KB 或以上时）||
|NROWID|二进制数据表中记录的唯一行号，最大长度 4000 bytes|VARCHAR (n)、VARCHAR2 (n)|可变长字符串，最大长度 8000（页大小为 16 KB 或以上时）||


## 如何加 comment 备注

举例如下：

```
CREATE TABLE "AEFD01"
("BANKID" VARCHAR2(3)) STORAGE(ON "MAIN", CLUSTERBTR) ;
COMMENT ON TABLE "AEFD01" IS '会计事件定义';--备注名称
```

## 字段名和关键字冲突

  * 建议尽可能避免这些关键词，关键字在《DM_SQL 语言使用手册》附录 1 中有说明。（手册位于 **数据库安装路径** \`/dmdbms/doc\` 文件夹下）
  * 双引号可以屏蔽关键字。（同样注意，双引号也能匹配大小写）



```
--[执行语句1]:
create table testdomain(domain int);
执行失败(语句1)
第 1 行, 第 24 列[domain]附近出现错误:
语法分析出错
--[执行语句1]:
create table testdomain("DOMAIN" int);
执行成功, 执行耗时362毫秒. 执行号:1626676
影响了0条记录
1条语句执行成功
```

  * 通过配置 dm_svc.conf 文件 \`KEYWORDS\` 参数解决保留关键字的问题。  
KEYWORDS  
标识用户关键字，所有在列表中的字符串， 如果以单词的形出现在 SQL 语句中，则这个单词会被加上双引号。该参数主要用来解决用户需要使用 DM
中的保留字作为对象名使用的状况。  
修改服务名配置文件如下：



```
TIME_ZONE=(480)
LANGUAGE=(cn)
dm=(10.*.*.9:5236)
[dm]
KEYWORDS=(需要排除的关键字 以逗号做分割)
```

  * 通过 jdbc 的 url 中添加 keyWords 参数，例如 aa abc



```
jdbc:dm://dm?keyWords=aa,abc
```

> **建议**
>
> 不建议屏蔽，更换列名是最合适。



## 时间类型的默认值可以是当前时间

  * 问题截图



![问题截图](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-042.jpg)

  * 处理办法



使用查询函数 CURRENT_TIMESTAMP，如下所示：

```
SELECT CURRENT_TIMESTAMP();
```

![执行界面](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-043.jpg)

创建测试表，如下所示：

![创建测试表](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-044.png)

使用函数作为值不需要加引号。

## 含中文字符的 SQL 语句执行报错

实例如下所示：

![执行语句](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-045.jpg)

复制 SQL 语句到 DM 客户端执行要注意是英文输入模式下的标点符号。同时可以打开选项设置，勾选显示空白字符，可以很方便的发现是否有中文标点符号。

![选项设置](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-046.jpg)

## Manager 中 SQL 自动补全

SQL 自动补全功能又称为：SQL 提示补充、SQL 查询补全、SQL 联想功能。
通过启动 Manager，打开窗口，选择【启用 SQL 输入助手】开启该功能，如下图所示：

![输入助手](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-047.jpg)

## 如何使用打印信息

可使用如下方法打印信息：

```
DBMS_OUTPUT.**ENABLE**();--使 DBMS_OUTPUT 启用

DBMS_OUTPUT.PUT_LINE('11111');
```

![执行结果](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-048.jpg)

## PB 调用存储过程

PB 调用 he_test 报错 `Space must be allocated for bind parameter`，翻译过来是 `约束变量必须分配空间`。

【报错原因】：

PB 中使用 Declare 方式不能调用具有 `in out` 类型参数的存储过程，故采用外部函数的方式。

【问题描述】：

```
create or replace procedure he_test(tin in varchar2,
out1 out varchar2,
out2 out varchar2)
as
begin
out1 := tin;
out2 := tin;
end he_test;
```

PowerBuilder：

```
string tpstring,ta,tb,tc
tpstring = 'ssss'
DECLARE get_trriff PROCEDURE FOR
he_test(:tpstring,:ta,:tb) using ORA;
EXECUTE get_trriff ;
if ORA.SqlCode < 0 then
      MessaGeBox("1","1" + ORA.sqlerrtext)
      CLOSE get_trriff;
      RETURN -1
    end if
FETCH get_trriff INTO :ta,:tb;
if ORA.SqlCode < 0 then
   MessaGeBox("2","2" + ORA.sqlerrtext)
   CLOSE get_trriff;
   RETURN -1
end if
sle_1.text = ta
sle_2.text = tb
```

如果要获取 OUTPUT 可把 procedure 作为 transaction 的外部函数引用 `(local external function)`。新建 `-standare class- transation - local external function` 右键 `- paste special-sql - remote sp`。

## shell 执行 DM 语句

  * 编写 SQL 脚本，写入需要执行的 SQL 语句。



![编写脚本](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-049.jpg)

  * shell 脚本中执行 DM 语句的格式如下：



![shell 脚本](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-050.png)

  * 执行脚本



![执行结果](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-051.jpg)

详细用法可参考《DM_Disql 使用手册》，手册位于数据库安装路径 `/dmdbms/doc/special` 文件夹下。

## update 和 order by 如何一起用

错误示例：

```
update scm set row_id = rownum where bill_id =12345 order by id;
```

正确示例：

```
update scm t set row_id =  (select rn from (select bill_id, id, row_number() over(order by id) rn from scm tt where bill_id = 12345) tt where t.id = tt.id) where bill_id = 12345;
```

## 写 SQL 如何才能不带上模式名

在 DM 数据库里面，为何 SQL 要加上模式名（数据库名）访问？

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202409061016495H5FG1CYWYPGGS5995)

其实这是把 MySQL 或者 SQL Server 的思维代入 DM 数据库造成的，MySQL 的体系架构是单实例多库的，一个用户可以访问多个数据库，然后指定当前数据库写 SQL 的时候就不用带上数据库名了。

达梦的体系架构是单库多实例的，也就是没有多个数据库的概念了，从 MySQL 或者 SQL Server 转到达梦，就需要建多个用户 + 表空间来对应 MySQL 的多个数据库。

第一步：例如 MYSQL 中有 TESTDB1,TESTDB2 两个库，都用 root 用户来访问，首先在达梦数据库中创建两个表空间：

```
CREATE TABLESPACE TESTDB1 DATAFILE 'TESTDB1.DBF' SIZE 128;
CREATE TABLESPACE TESTDB2 DATAFILE 'TESTDB2.DBF' SIZE 128;
```

第二步：创建两个用户，授予对应的权限：

```
--以下 SQL 用 SYSDBA 用户登录执行
CREATE USER TESTDB1 IDENTIFIED BY "123456789" DEFAULT TABLESPACE TESTDB1;
CREATE USER TESTDB2 IDENTIFIED BY "123456789" DEFAULT TABLESPACE TESTDB2;
GRANT RESOURCE TO TESTDB1;
GRANT RESOURCE TO TESTDB2;
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240906102014FOAJMAYJGQTJP8LIDA)

第三步：迁移 MySQL TESTDB1 数据库里面的表，数据库迁移工具就使用 TESTDB1 用户来迁移

迁移 MySQL TESTDB2 数据库里面的表，数据库迁移工具就使用 TESTDB2 用户来迁移

第四步：访问 TESTDB1 用户（模式）下的表，就使用 TESTDB1 用户登录来访问，就不需要加模式名 TESTDB1

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240906102233DFIIF7SOPXYCX93PJR)

如果要用 JDBC 来访问数据库，设置如下：

```
jdbc.driver=dm.jdbc.driver.DmDriver
jdbc.url=jdbc:dm://127.0.0.1:5236
jdbc.username=TESTDB1
jdbc.password=123456789
```

补充说明一下：假设一个用户拥有多个模式，该用户访问自己所属的其他非同名模式下的对象时，如何才能不带模式名呢？

这里以 SYSDBA 用户为例：

```
CREATE SCHEMA "TEST" AUTHORIZATION "SYSDBA";

CREATE TABLE TEST.TTTT(C1 INT);

INSERT INTO TEST.TTTT VALUES(1);

COMMIT;

SELECT * FROM TTTT; --会报错

SET SCHEMA TEST;  --切换当前模式为TEST

SELECT * FROM TTTT;  --执行成功
```

## 分段连接、拼接字符串函数

【问题描述】：

  * Oracle 的分段连接函数：WMSYS.WM_CONTACT，在 DM 库中有对应的函数吗？
  * DM 中有办法实现 MySQL 的 group_concat 函数吗？
  * DM 有没聚合，拼接字符串的函数？



【解决方法】：

DM 数据库可以用 wm_concat。推荐使用 listagg。

可参《DM_SQL 语言使用手册》-LISTAGG/LISTAGG2 集函数，手册位于数据库安装路径 `/dmdbms/doc` 文件夹下。

## DISQL 语句不结束怎么处理

对于普通 SQL，输入 `;` 后回车结束。对于执行语句块（例如，创建触发器、存储过程、函数、包和模式等时），输入 `/` 结束。

## 获取 GUID 的函数

【问题描述】：

SQL Server 中有 uniquidentifer 类型的 GUID；DM 中的 GUID 是什么？

【解决方法】：

DM 中没有 uniquidentifer，获取 GUID 的函数 `sys_guid()`。

sys_guid() 用的是 binary(16)，16 个字节的二进制，和 SQL Server 的 uniqueidentifier 类型一样的。

## 需要 transform 定义 DDL 语句

【问题描述】：

在 dbms_metadata 下，获取一个 table 的 DDL 语句，现在表相关的序列，外键都出来了，用户只想定制只返回 create table 的语句。

  * ORALCE 的 DBMS_METADATA.GET_DDL 定义语句如下：



```
DBMS_METADATA.GET_DDL (
object_type IN VARCHAR2,
name IN VARCHAR2,
schema IN VARCHAR2 DEFAULT NULL,
version IN VARCHAR2 DEFAULT 'COMPATIBLE',
model IN VARCHAR2 DEFAULT 'ORACLE',
transform IN VARCHAR2 DEFAULT 'DDL') RETURN CLOB;
```

其中可以关闭索引、外键等关联如下：

![结果](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-052.png)

  * DM 的 DBMS_METADATA.GET_DDL 定义如下：



```
FUNCTION GET_DDL(
OBJECT_TYPE IN VARCHAR(30),
NAME IN VARCHAR(128),
SCHNAME IN VARCHAR(128) DEFAULT NULL )
RETURN CLOB
```

【解决方法】：

这属于需求开发，建议联系相应项目的技术服务人员/销售提交功能需求。

## 如何查询 SQL 计划中各操作符的执行时间

```
SELECT N.NAME, TIME_USED, N_ENTER FROM V$SQL_NODE_NAME N,
V$SQL_NODE_HISTORY H WHERE N.TYPE$ = H.TYPE$ AND EXEC_ID = 538;

--538 为该 SQL 的执行号，dm.ini 中 ENABLE_MONITOR 参数需要改为 3。

--可以创建一个存储过程，来封装这个查询的 sql，输入参数为 sql 语句的执行号：
create or replace procedure sql_et(eid int) is
    begin
    select
            name as "OP",
            time_used/1000 || 'ms' as "TIME",
            cast(time_used * 100.0/sum(time_used) over() as dec(10, 2)) || '%' as "PERCENT" ,
            rank() over (order by time_used desc) as "RANK"    ,
            seq_no as "SEQ"
        from
           v$sql_node_history a,
           v$sql_node_name b
       where
           a.type$ = b.type$
           and exec_id = eid
       order by time_used desc;
   End;
```

## 执行 SQL 报错：试图在 blob 或者 clob 列上排序或比较

  * 不能 order by blob 或者 clob；
  * 有大字段的查询，不允许 distinct；



具体的语法规则，请参考《DM SQL 语言使用手册》，手册位于数据库安装路径 `/dmdbms/doc/special` 文件夹下。

## 如何查询所有自增列

可执行下列语句进行查询：

```
select b.table_name,a.name COL_NAME from  SYS.SYSCOLUMNS a,all_tables b,sys.sysobjects c where a.INFO2 & 0x01 = 0x01
and a.id=c.id and c.name= b.table_name
```

## 如何输出格式为 yyyy 年 mm 月 dd 日 HH 时 MM 分 SS 秒 的日期数据

```
SELECT to_char(sysdate,'yyyy"年"mm"月"dd"日" HH24"时"mi"分"ss"秒"') from dual；
```

## 达梦嵌套模糊查询如何编写

【问题描述】：

```
SQL1: select unit_code from s_org s
SQL2: select * from t_org where path like concat('%', SQL1.unit_code, '%')
sql1的查询结果，作为sql2的模糊查询条件，该怎么实现
select * from t_org where path like concat('%', '1111', '%')
select * from t_org where path like concat('%', '1122', '%')
select * from t_org where path like concat('%', '1123', '%')
.....
```

就像这个，如何写成一条 SQL。

【解决方法】：

可以使用 REGEXP_LIKE 函数

```
CREATE TABLE "SYSDBA"."ORG"
("ORGID" INT) STORAGE(ON "MAIN", CLUSTERBTR) ;

CREATE TABLE "SYSDBA"."TESTUSER"
("ID" INT,"A1" CHAR(10)) STORAGE(ON "MAIN", CLUSTERBTR) ;

select
        ID,
        A1
from
        TESTUSER
where
        REGEXP_LIKE (A1,
        (
                select
                        replace(wm_concat(orgid), ',', '|')
                from
                        (
                                select orgid from org
                        )
        )
        );
```

## 达梦数据库如何兼容 MySQL 的语法

【问题描述】：

如何在达梦数据库中支持以下语法：

  * 判断表存在就删除 drop table if exists table_name
  * 不存在就创建 CREATE TABLE IF NOT EXISTS



【解决方法】：

  * 需要在 dm.ini 文件中修改参数 compatible_mode=4，并重启数据库服务，就可以兼容 MySQL 数据库才支持的语法。
  * 也可以参考下 Oracle 的写法，Oracle 怎么判断表是否存在，就可以用在达梦上。



一般可以查询 DBA_TABLES 表，通过一个 IF 条件进行判断。

## 是否有类似 MySQL 的 ShA()等加密函数

可通过 dbms_obfuscation_toolkit 包实现。

## 有类似 mysql 的 LAST_INSERT_ID() 的函数吗

可以使用下面语句：

```
select @@IDENTITY;
```

举例说明：

```
CREATE TABLE new_employees
(
id_num int IDENTITY(1,1),
fname varchar (20),
minit char(1),
lname varchar(30)
);
insert into new_employees(fname,minit,lname) values('test','d','test1');
select @@IDENTITY;
```

## 如何写复制表的 SQL

语句如下：

```
create table table1 as select * from table2;
```

可以参考安装目录 \doc 目录下的《DM_SQL 语言使用手册》—表管理章节。

## 如何用命令清理归档日志

删除 n 天前的归档日志，可执行下列语句：

```
SELECT SF_ARCHIVELOG_DELETE_BEFORE_TIME(SYSDATE - n);
```

## 获取表的主键的视图

使用下列语句：

```
select * from ALL_CONSTRAINTS where CONSTRAINT_TYPE='P'; 
```

## 在 Linux 环境下，AWR 报告如何取出来

```
--启用系统包和 AWR 包
CALL SP_INIT_AWR_SYS(1);
CALL SP_CREATE_SYSTEM_PACKAGES(1);

--查询 AWR 快照
SELECT * FROM SYS.WRM$_SNAPSHOT;

--设置快照间隔
--如果不设置快照间隔，手动执行快照后 SYS.WRM$_SNAPSHOT 视图中将没有记录。
CALL DBMS_WORKLOAD_REPOSITORY.AWR_SET_INTERVAL(50);

--在两个时间点分别手动创建快照，或者等待系统自动生成
--10:00 时创建第一快照：
CALL DBMS_WORKLOAD_REPOSITORY.CREATE_SNAPSHOT();
--30 分钟后再创建一个，10:30:
CALL DBMS_WORKLOAD_REPOSITORY.CREATE_SNAPSHOT();

--查询快照
SELECT * FROM SYS.WRM$_SNAPSHOT;

--创建AWR报告
---SYS.AWR_REPORT_HTML(快照ID1,快照ID2,'AWR报告存放路径','AWR报告名称.HTML');：
SYS.AWR_REPORT_HTML(1,2,'/opt','AWR1.HTML');
```

## 达梦数据库的“函数”、“存储过程”的使用参考文档

可以参考安装目录 doc 目录下的《DM_SQL 语言使用手册》、《DM_SQL 程序设计》。

## 表的 id 字段没有重复值，但是添加主键就提示违反唯一性约束

  * 首先检查该列有没有唯一索引，如果有的话就不能添加主键。  
例如：



```
create table "SYSDBA"."TABLE_1"("COLUMN_1" CHAR(10) unique ,"COLUMN_2" CHAR(10));
--[执行语句1]:
alter table "SYSDBA"."TABLE_1" add primary key("COLUMN_1");
--执行失败(语句1)
--第1 行附近出现错误[-2864]:
--表中已存在这样的唯一关键字或主键
```

  * 使用 trim 函数找到存在重复的数据，去除重复值后就可以正常使用唯一性约束



```
CREATE TABLE "SYSDBA"."TEST"("T1" INT,"T2" VARCHAR(50));

insert into "SYSDBA"."TEST"("T1", "T2") VALUES(1,'TEST1');
insert into "SYSDBA"."TEST"("T1", "T2") VALUES(1,'TEST2');
insert into "SYSDBA"."TEST"("T1", "T2") VALUES(2,'TEST3');
insert into "SYSDBA"."TEST"("T1", "T2") VALUES(2,'TEST4');
insert into "SYSDBA"."TEST"("T1", "T2") VALUES(3,'TEST5');

--给有重复数据的列添加主键，就会报错：-6612  错误消息: 违反唯一性约束
alter table "SYSDBA"."TEST" add primary key("T1");

--你用 trim 函数查询重复值
select trim(T1) as ,count(*) from "SYSDBA"."TEST" group by trim(T1) having count(*) >1
```

![结果](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-052.jpg)

## 表创建完成后怎么修改字段顺序？

表创建之后不能修改顺序。可以把当前的表删掉重新建一下（删除之前做好备份），然后将数据重新导入。

## 是否支持 IF 函数

【问题描述】：

否支持 IF 函数，如 IF(TRUE,'A','B')/ 自定义函数实现 IF 功能

【问题解答】：

支持/使用 IF 函数，或者使用 DECODE 函数来实现。具体可以参考安装目录 doc 目录下的《DM_SQL 语言使用手册》。

## NVARCHAR2 和 VARCHAR2、VARCHAR 的区别

Oracle 的这三个类型有区别，DM 的这三个类型是没有区别的。

## 达梦不支持布尔类型，请问可以使用什么替代？

可以通过 BIT 类型替代，具体语法请参考 [BIT 类型语法](../sql-dev/dmpl-sql-datatype###位串数据类型)

## 达梦可以给自增列赋值么？

可以，可以通过设置 SET IDENTITY_INSERT [\< 模式名 >.]\< 表名 > ON ;的形式进行赋值插入指定的数据，设置 on 之后会自动还原成 off；不会影响到后续自增列的使用。

示例如下：

```
create table "SYSDBA"."TABLE_5"
(
"ID" INTEGER identity(1, 1) not null ,
"NAME" CHAR(50),
primary key("ID")
);
--插入数据
insert into "SYSDBA"."TABLE_5"("NAME") VALUES('测试数据001');
insert into "SYSDBA"."TABLE_5"("NAME") VALUES('测试数据002');
insert into "SYSDBA"."TABLE_5"("NAME") VALUES('测试数据003');
SET IDENTITY_INSERT SYSDBA.TABLE_5 ON;--设置自增列插入
insert into "SYSDBA"."TABLE_5"("ID","NAME") VALUES(5,'测试数据004');--插入指定数据给自增列
insert into "SYSDBA"."TABLE_5"("NAME") VALUES('测试数据005');
select * from "SYSDBA"."TABLE_5";
```

![结果](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-053.jpg)

## 达梦如何判断表中的字段是否为自增列？如何用脚本查询？

可以通过 SYSCOLUMNS 的 INFO2 来确认，查询自增列表和列可以使用以下语句查询；

```
select
        b.TABLE_NAME,
        a.NAME as COL_NAME
from
        SYS.SYSCOLUMNS a,
        all_tables b    ,
        SYS.SYSOBJECTS c
where
        a.INFO2&0x01=0x01
    and a.ID        =c.ID
    and c.NAME      =b.TABLE_NAME;
```

## 达梦数据库中整数相除如何让结果为小数？如 select TRUNCATE((1/5),4) from dual

方法 1：将分子或者分母设置为小数。

```
select TRUNCATE((1/5.0),4) from dual
```

方法 2：执行以下语句修改 dm.ini 参数并重启数据库服务。

```
SP_SET_PARA_VALUE(2,'CALC_AS_DECIMAL',1);
```

## 达梦有类似 MySQL 的 conv() 进制转换的的函数么?

可以使用语法：RAWTOHEX (binary)

功能：将 RAW 类数值 binary 转换为一个相应的十六进制表示的字符串。binary 中的 每个字节都被转换为一个双字节的字符串。 RAWTOHEX 和 HEXTORAW 是两个相反的函数。

例：

```
SELECT RAWTOHEX('达梦数据库有限公司');
```

查询结果为：B4EFC3CECAFDBEDDBFE2D3D0CFDEB9ABCBBE

```
SELECT RAWTOHEX('13');
```

查询结果为：3133

## 达梦如何用脚本导出某个模式下全部表定义

  * 方法一，将模式下的表定义生成到表中



```
--创建表存放表定义数据
drop table sch_table_ddl;
create table sch_table_ddl(tab_name VARCHAR, tab_ddl clob);

--创建存储过程获取表定义，并存放到指定表中
create or replace procedure get_schema_table_ddl(sch_name VARCHAR(30))
as
begin
for rec in (select table_name from dba_tables where owner = sch_name and table_name != 'SCH_TABLE_DDL')
loop
insert into sch_table_ddl values (rec.table_name,dbms_metadata.get_ddl('TABLE',rec.table_name,sch_name));
commit;
end loop;
EXCEPTION when others
then 
dbms_output.Put_line('导出异常');
end;

--测试获取SYSDBA模式下的表定义
call get_schema_table_ddl('SYSDBA');

--查询表定义
SELECT TAB_NAME,TO_CHAR(TAB_DDL) FROM sch_table_ddl;

```

  * 方法二，将模式下的表定义生成到脚本文件



```
--创建存放ctrl文件的路径
CREATE OR REPLACE DIRECTORY TMP AS 'D:\dmdbms\data\DAMENG';

CREATE OR REPLACE
PROCEDURE SP_sch_table_ddl
IS
        FILE_HANDLE UTL_FILE.FILE_TYPE;
        WRITE_CONTENT   clob;
        TAB_NAME varchar(30);
        CURSOR CUR_SP_OUT
        IS
        select table_name from dba_tables where owner = 'SYSDBA' and table_name != 'SCH_TABLE_DDL';
BEGIN
                FILE_HANDLE     := UTL_FILE.FOPEN('TMP', 'SYSDBA_DDL.sql', 'W');
        OPEN CUR_SP_OUT;
        LOOP
        --此处写入内容为表的列名
                FETCH CUR_SP_OUT INTO TAB_NAME;
                EXIT  WHEN CUR_SP_OUT%NOTFOUND;
                WRITE_CONTENT   := dbms_metadata.get_ddl('TABLE',TAB_NAME,'SYSDBA');
                --写入文件
                IF UTL_FILE.IS_OPEN(FILE_HANDLE) THEN
                        UTL_FILE.PUT_LINE(FILE_HANDLE, WRITE_CONTENT);
                END IF;
                --关闭文件
        END LOOP;
          UTL_FILE.FCLOSE(FILE_HANDLE);
END;


CALL SP_sch_table_ddl;
```

## 子查询中存在相同列名，进行查询时，报有歧义的列名

【问题描述】：

```
 --创建表test，
CREATE TABLE "SYSDBA"."TEST"
(
"ID" INT,
"NAME" VARCHAR(100));
--使用如下语句进行查询
select * from (select ID,ID,NAME from test) T1;
```

报错，有歧义的列名：

![有歧义的列名](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-054.png)

【问题解答】：

修改 ERROR_COMPATIBLE_FLAG 参数可绕过；

```
CALL SP_SET_PARA_VALUE(1,'ERROR_COMPATIBLE_FLAG',0);
```

## 收集列的统计信息时是否会收集索引统计信息

【问题说明】：

```
--收集指定用户下所有表所有列的统计信息：
DBMS_STATS.GATHER_SCHEMA_STATS('username',100,TRUE,'FOR ALL COLUMNS SIZE AUTO');
```

这个语句是否会收集索引的统计信息？

【问题解答】：

会收集，测试如下：

```
select * from "TEST"."TEST_WL";
--新建索引
create index idx_test_wl_id on test.test_wl(id);
--查看索引统计信息情况
dbms_stats.index_stats_show('TEST','IDX_TEST_WL_ID');
--收集模式下的列统计信息
dbms_stats.gather_schema_stats('TEST',100,TRUE,'FOR ALL COLUMNS SIZE AUTO');
--再次查看索引统计信息即可查看到统计信息情况
```

## 在客户端做 TEXT 数据类型查询时，无法直接看到文本内容

可以查出来，也可以用 “=” 作匹配，只是在客户端查出来的结果也一般是显示为：\< 长文本 >

如果想要在客户端工具直接显示成文本，一般对大字段进行查询都要进行特殊处理，有两种查询大字段方式：

  * cast/to_char



如：

```
select id ,cast(t_text as varchar) from test;
```

  * dbms_lob.substr



如：

```
select id ,dbms_lob.substr(t_text,8000) from test;
```

## Mysql 的 replace into 语法，用达梦怎么实现

达梦可以用 merge into 实现 replace into 操作。详细操作可以参考安装目录 DOC 目录下的 -- DM SQL 语言使用手册。

举例：

例如 MySQL 中，用 replace into 指令替换属性 id,numbers,age 的值分别为 2,100,15：

```
replace into test(code,number,age) values(2,100,15)
```

在达梦数据库中，使用 merge into 替换为：

```
merge into table1
using (select 2 code,100 number,15 age from dual) t
on(table1.code = t.code)
when matched then
update set table1.number=t.number,table1.age=t.age
when not matched then
insert (code,number,age) values(t.code,t.number,t.age)
```

## xml 解析库未加载

使用包内的过程和函数之前，如果还未创建过系统包。请先调用系统过程创建系统包。

```
SP_CREATE_SYSTEM_PACKAGES (1,'DBMS_LOB');
SP_CREATE_SYSTEM_PACKAGES (1,'DBMS_XMLGEN');
SP_CREATE_SYSTEM_PACKAGES (1,'DBMS_OUTPUT');
SET SERVEROUTPUT ON;
```

## SQL 语法错误：无效的表或视图名[dual]

【问题解决】：

dual 前面不要写模式名，用法如下：

```
select 1 from dual;
```

此外，在 DM6 数据库中，dual 伪表在 SYSTEM 库的 SYSDBA 模式下，非 SYSDBA 用户访问时需要带上库名和模式名来访问，如果需要直接访问可以用 SYSDBA 创建一个 PUBLIC 同义词：

```
--创建 public 同义词
CREATE PUBLIC SYNONYM dual for SYSTEM.SYSDBA.SYSDUAL;

--创建完成后，用户库中的非 SYSDBA 用户就可以直接访问
select to_date(sysdate,'yyyy-MM-dd') from dual;
```

## 在 disql 中，语句用 ";" 无法结束

【问题描述】：

disql 中，用";" 无法结束，或报错：The script file is not complete,last sql has not been executed.

【问题解决】：

创建触发器，存储过程，函数，包，模式等时需要用“/”结束。

## IP 函数 inet_aton()和 inet_ntoa()怎么转换成达梦语法？

目前达梦数据库没有这两个函数，但是可以用基础函数实现，如下：

```
样本数据
IP格式： 192.168.117.181   对应的数字格式：3232265653

inet_ntoa()     --整数转IP

select
   trunc(ip/16777216)||'.'|| trunc( mod(ip, 16777216)/65536) ||'.'|| trunc(mod(ip,65536)/256)||'.'|| trunc(mod(ip,256)) as ip_address
from
   (select 3232265653 as ip from dual);

输出：192.168.117.181

inet_aton()

select
   to_number(regexp_replace(ip, '([0-9]{1,3})\.([0-9]{1,3})\.([0-9]{1,3})\.([0-9]{1,3})', '\1')) * 16777216 +
   to_number(regexp_replace(ip, '([0-9]{1,3})\.([0-9]{1,3})\.([0-9]{1,3})\.([0-9]{1,3})', '\2')) * 65536 +
   to_number(regexp_replace(ip, '([0-9]{1,3})\.([0-9]{1,3})\.([0-9]{1,3})\.([0-9]{1,3})', '\3')) * 256 +
   to_number(regexp_replace(ip, '([0-9]{1,3})\.([0-9]{1,3})\.([0-9]{1,3})\.([0-9]{1,3})', '\4'))  as ip_number
from
   (select '192.168.117.181' as ip from dual);

输出：3232265653
```

## 如何利用数据库取得一个唯一值编号？

  * 可以利用自增列取得唯一值。



```
CREATE TABLE T
(
ID INT IDENTITY(1,1),
MEMO VARCHAR(256)
);
INSERT INTO T VALUES('A');
COMMIT;
SELECT * FROM T;
```

  * 可以使用序列取得唯一值。



```
CREATE SEQUENCE SEQ_1 INCREMENT BY 1;
CREATE TABLE T_SEQ(ID INT,C1 DATE);
INSERT INTO T_SEQ VALUES(SEQ_1.NEXTVAL,SYSDATE());
COMMIT;
SELECT * FROM T_SEQ;
```

  * 如果是字符串，可以使用 GUID 函数返回唯一的值。



```
SELECT GUID FROM DUAL;
```

## 达梦整数或者整除的显示，如何保持与 Oracle 相同？

达梦数据库整数除法等运算默认舍弃小数，不会进行四舍五入，与 Oracle 不同。
达梦数据库中：

```
SQL> SELECT 123456/10000 FROM DUAL;
行号       123456/10000
---------- ------------
1          12
```

Oracle 中：

```
SQL> SELECT 123456/10000 FROM DUAL;
123456/10000
------------
    12.3456
```

达梦数据库使用 to_char(CEIL(表达式))结果返回为科学记数法

```
SQL> select to_char(ceil((SYSDATE - to_date('2020-10-26 10:39:05','YYYY-MM-DD hh24:mi:ss')) * 24 * 60)) from dual;
行号       TO_CHAR(CEIL((SYSDATE-TO_DATE('2020-10-2610:39:05','YYYY-MM-DDhh24:mi:ss'))*24*60))
---------- -----------------------------------------------------------------------------------
1          6.31E2
```

此时 Oracle 返回整数

可通过修改参数设置，使达梦输出与 Oracle 一致：

修改 CALC_AS_DECIMAL 参数值为 2，并重启数据库生效并验证。

```
sp_set_para_value(2,'CALC_AS_DECIMAL',2);
```

需重启数据库生效；

## blob 字段用 length 取长度报错：参数不兼容

对 blob 字段取长度，无法使用 length， 可以使用系统包 DBMS_LOB.GETLENGTH(列名)或者 lengthb(列名);

```
Select  dbms_log.getlength(file_content) from  AR_FILE;
```

## PIVOT 行列转换函数报错

行列转换 PIVOT 子句，必须要包含一个聚合函数，聚合函数分别是：COUNT、SUM、MAX、MIN、AVG，针对不同的使用场景，选择聚合函数。

语法格式：

pivot (聚合函数(列名) for 被转换的列名 in(要显示出来的列名))

```
select
        *
from
        table_name pivot(max(column_name)  --行转列后的列的值value，聚合函数是必须要有
        for column_name in(value_1, value_2, value_3) --需要行转列的列及其对应列的属性1/2/3
        );
```

示例说明：

```
create table tmp as select * from (
select '张三' student,'语文' course ,78 score from dual union all
select '张三','数学',87 from dual union all
select '张三','英语',82 from dual union all
select '张三','物理',90 from dual union all
select '张三','物理',92 from dual union all
select '李四','语文',65 from dual union all
select '李四','数学',77 from dual union all
select '李四','英语',65 from dual union all
select '李四','物理',85 from dual);

--PIVOT行转列
select
        t.*,
        (t.语+t.数+t.外+t.物) as total
from
        (
                select
                        *
                from
                        tmp pivot ( max(score) for course in ('语文' as 语, '数学' as 数, '英语' as 外, '物理' as 物) )
        )
        t;

--返回结果集

张三   78   87   82   182   429

李四   65   77   65   85   292
```

## 字符串太长，导致 LISTAGG 函数报“字符串截断”如何处理？

处理方法：将 LISTAGG 改成 LISTAGG2。示例如下：
1、创建测试表和造测试数据：

```
--创建测试表test
create table test
as select level id ,rpad('A',1000,'B') c1 FROM DUAL CONNECT BY LEVEL<=10;
--插入测试数据
BEGIN
for I in 1..10
LOOP
INSERT INTO TEST
SELECT * FROM TEST;
END LOOP;
commit;
END;
--查询测试表的总数据量
select count(*) from test；--10240
```

2、用 listagg 函数去执行 sql 语句

```
SELECT ID ,LISTAGG(C1,'->') WITHIN GROUP (ORDER BY ID)
FROM TEST
GROUP BY ID;
--报错，字符串截断
```

3、将 listagg 修改成 listagg2，再次执行该 sql 语句

```
SELECT ID ,LISTAGG2(C1,'->') WITHIN GROUP (ORDER BY ID)
FROM TEST
GROUP BY ID;   --成功
```

## 查询语句中如何按照自定义的顺序进行排序

【问题描述】：

现有需求要根据自定义的顺序进行排序，例如：现有 PERSON 表，需要将第 7 条数据作为第一行，第 3 条数据作为第二行，第 2 条数据作为第三行进行输出，表中数据如图：

![PERSON 表数据](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-055.png)

【问题解决】：

  * 利用 CASE 语句：



```
SELECT * FROM PERSON.PERSON WHERE PERSONID IN (2,3,7)
ORDER BY CASE PERSONID
             WHEN 7 THEN 1       ---将 PERSONID=7 的数据作为第一行输出
             WHEN 3 THEN 2       ---将 PERSONID=3 的数据作为第二行输出
             WHEN 2 THEN 3       ---将 PERSONID=2 的数据作为第三行输出
        END;
```

  * 利用 DECODE 函数：



```
SELECT * FROM PERSON.PERSON WHERE PERSONID IN (2,7,3)
ORDER BY DECODE(PERSONID,7,1,3,2,2,3); 
```

以上两种方法均可得到以下输出结果：

![EMPLOYEES 表数据](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-056.png)

## sql 快捷命令：授予用户对某一模式的只读权限，并查询

```
select ‘grant select  on 模式名.’||table_name||’  to 只读用户;’ from dba_tables where owner=‘模式名’;

---例如授予 READER 用户对 SYSDBA 模式下所有表的只读权限，并查询：
select 'grant select on SYSDBA.'||table_name||' to READER' from dba_tables where owner='SYSDBA';  
```

或者

```
select 'grant select on '||owner||'.'||object_name||' to 用户名;' from dba_objects where owner in ('当前用户')  and object_type='TABLE';

---例如授予 READER 用户对 DMHR 模式下所有表的只读权限，并查询：
select 'grant select on '||owner||'.'||object_name||' to READER;' from dba_objects where owner in ('DMHR') and object_type='TABLE';  
```

## 使用 UTL_FILE 包报错：utf.file.open 无访问权限

【问题解决】：

  * 读功能，当读取的文件 dmdba 用户无法访问时会报无权限访问问题，修改其用户属组或者权限即可。
  * 写功能，达梦为安全考虑，默认调用系统包只能写入特定目录，即库目录，可以通过以下语句查询到目录路径：



```
SELECT * FROM V$DM_INI WHERE PARA_NAME LIKE '%SYSTEM_PATH%';
```

## 如何查询一个表涉及到的所有触发器

【问题解决】：

通过以下语句进行查询：

```
select * from dba_triggers where DBA_TRIGGERS.OWNER = '模式名' AND DBA_TRIGGERS.TABLE_NAME = '表名'
```

## dm 到 oracle 的 dblink 环境中，在 dm 上调用 oracle 包中的存储过程提示：获取对象失败

【问题描述】：

在 DM 上调用 oracle 数据库中的 ceshi_pkg 包的 showMessage 存储过程，出现如下报错：

![报错信息](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-057.png)

【问题解决】：
远程调用 oracle 包里的存储过程需要加上用户名，将 SQL 语句改为如下即可：

```
call scott.ceshi_pkg.showMessage@link1();
```

## 当虚拟列的基列中有空数据时，查询虚拟列或者把虚拟列作为查询条件会报错:非法的参数数据

【问题描述】：

表 xnl 中，pwd 字段为虚拟列，其基列是 passwd，passwd 中有空值。

利用如下语句进行查询，均出现报错：非法的参数数据

```
select * from "SYSDBA"."xnl"
select "id","passwd","sex","pwd" from "SYSDBA"."xnl" ;
```

【问题解决】：

把虚拟列用 ifnull 参数处理一下即可，如下图所示：

![修改虚拟列](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-058.png)

## 如何利用 SQL 语句查看数据库用户限制登录和允许登录的 IP 地址

【问题解决】：

```
---查询所有用户的 IP 黑白名单：
select a.id,b.username,a.allow_addr,a.not_allow_addr from sysuser$ a, dba_users b where a.id=b.user_id;
```

## 如何批量禁用/启用用户下的所有约束信息

【问题解决】：

  * 禁用:运行以下 SQL 语句，将得到的结果复制出来在 disql 中批量执行。



```
select 'alter table '|| t.OWNER||'.' ||t.TABLE_NAME||' disable constraint '|| t.CONSTRAINT_NAME|| ';'
from dba_constraints t
where t.OWNER not in( 'SYSDBA','SYS');
```

  * 启用：运行以下 SQL 语句，将得到的结果复制出来在 disql 中批量执行。



```
select 'alter table '|| t.OWNER||'.' ||t.TABLE_NAME||' enable constraint  '|| t.CONSTRAINT_NAME|| ';'
from dba_constraints t
where t.OWNER not in( 'SYSDBA','SYS');
```

## 插入的数据带 & 字符，显示请输入 XXX 的值，该如何解决

【问题描述】：

如下图所示，name 列插入数据带 & 字符，显示要输入 cc 的值。回车后，认定 cc 为空，因此只插入了值 aa。

![插入带 & 的字段](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-059.png)

【问题解决】：

这里插入的语句中 name 字段的值‘aa & cc’中的 & 被当成了变量，因此显示需要输入 cc 的值。此时有两种解决办法：

方法一：
sql 命令行中执行 `set define off;` 关闭替代标记功能后，重新插入数据成功。

![关闭替代标记功能](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-060.png)

方法二：
通过使用转义符取消掉 & 符号的特殊含义，具体如下：全文替换：& 替换为'||'&'||'，然后再导入就可以了。

![转义符转义 &](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-072.png)

## TRANSLATE 剔除字符串中的字符后结果为空

【问题分析】：

现需要使用 TRANSLATE 剔除字符串 a 中，包含字符集 b 的任一字符，例如在'1@111*/'中，要剔除 @、*和/中的任一字符，使其结果为'1111'。
而使用 TRANSLATE( char,from,to)，只要第三个参数 to 为空，则整个结果为空。如下图所示：

![剔除后为空](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-061.png)

可以使用 regexp_replace 函数代替，如下图所示：

![运行截图](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-062.png)

## 执行查询语句时报错：字符串转换出错

【问题描述】：

问题重现如下：

```
---建表，设置 ID 列的数据类型为 varchar
create table test1008(id varchar(20));
insert into test1008 values(12345678);
insert into test1008 values('12345678_2');
insert into test1008 values('12345678_3');
commit;

---按照以下语句进行查询报错：字符转换出错
select * from test1008 where id>=1;
```

【问题分析】：

需要检查查询语句的条件列是否正确，根据表结构可以看出 ID 列数据类型为 varchar，不是数值型数据，所以需要加上单引号，修改查询语句如下即可。

```
select * from test1008 where id>='1';
```

## 达梦的聚集索引不支持大字段操作，将聚集索引变成非聚集索引应如何操作

【问题解决】：

可通过在表上除聚集主键外的任意一列创建一个聚集索引，原聚集主键列被取代，再删除新创建的聚集索引即可。具体实例如下：

```
---创建测试表 TEST111，设置 ID 列为聚集主键
drop table  TEST111;
CREATE TABLE TEST111(ID int CLUSTER PRIMARY KEY,name varchar);
insert  into TEST111 select id ,name from sysobjects ;
commit;

---先用该表上除聚集主键以外的任意一列创建一个聚集索引
 create cluster index idx1_1 on TEST111(name);

---查询现在的表定义，原先在 id 上的聚集主键列，已经变成非聚集主键列
 select tabledef('SYSDBA','TEST111');

---最后在删除先前创建的 idx1_1 聚集索引
 drop   index idx1_1;
```

## DM 中默认除数和被除数都是整数，最后的结果也被当作整数处理

【问题描述】：

当被除数和除数都是整数，结果也直接默认取整数，此结果和 oracle 不一致。例如：

```
---dm 数据库运行结果为 0，Oracle 数据库运行结果为 0.4
select 8/20；
```

【问题解决】：

  * 达梦数据库种默认整数相除，最后的结果集是按照整数处理。
  * 如果除数或者被除数有一个是浮点型，最后的结果都是按照浮点型处理。



```
---以下运行结果均为 0.4
select 24/60.0;
或者
select 24.0/60;
```

  * 达梦数据库还在 dm.ini 配置文件中提供了 CALC_AS_DECIMAL 参数，该参数是静态参数，需要重启数据库才生效。该参数值的含义如下：  
0：默认值，表示整数类型的除法、整数与字符或 BINARY 串的所有四则运算，结果都处理成整数；  
1：表示整数类型的除法全部转换为 DEC(0，0)处理；  
2：表示将整数与字符或 BINARY 串的所有四则运算都转换为 DEC(0，0)处理。



> **注意**
>
> CALC_AS_DECIMAL 参数只有在 USE_PLN_POOL 为 0 或 1 时有效。当 USE_PLN_POOL 为 2 或 3 时，按照
> CALC_AS_DECIMAL=2 处理



例如：

```
---打开 CALC_AS_DECIMAL=1,整数类型的除法全部转换为 DEC(0，0)浮点型处理，运行结果为 0.4
select 24/60=0.4
```

## LIKE 语句 CLOB 字段模糊查询报错：字符串截断

【问题解决】：

DM7 LIKE 语句中 CLOB 类型的最大长度默认值为 31KB，DM8 LIKE 语句中 CLOB 类型的最大长度默认值为 10240KB，当查询的 CLOB 字段长度超过这个值后就会报错。
处理方法：使用命令 `sp_set_para_value(1,'CLOB_LIKE_MAX_LEN',10240);` 修改 CLOB_LIKE_MAX_LEN 的值，之后重启数据库生效。
CLOB_LIKE_MAX_LEN：LIKE 语句中 CLOB 类型的最大长度，单位 KB，有效值范围（8~102400），静态参数，修改后需要重启。

## 在一条 select 查询中无法同时执行两个 distinct()函数

【问题分析】：

distinct() 函数的执行原理为从被选择出的具有重复行的每一组中仅返回一个符合筛选条件的这些行的拷贝，而同时执行两个 distinct() 函数意味着需要从被选择出的具有重复行的每一组中返回两个符合不同筛选条件的这些行的拷贝，从执行原理上产生了冲突，所以无法同时执行两个 distinct() 函数。
解决办法：将两个 distinct() 函数分别用两条 select 查询语句分次执行。

```
---执行报错：
select distinct(JOB_ID),distinct(MIN_SALARY) from DMHR.JOB;  

---分开执行 distinct，执行成功
select distinct(JOB_ID) from DMHR.JOB;
select distinct(MIN_SALARY) from DMHR.JOB;
```

## 插入数据时报错：违反引用约束[CONSxxxxx]

【问题解决】：

若在表 A 的 fid 字段上设置了外键，引用了表 B 的 pid 字段，则当在表 A 的 fid 字段插入数据前，需确保表 B 的 pid 字段已存在相同数据，否则会因为违反引用的约束，无法正常完成数据插入。

## 达梦有很多内置函数，比如 sp_set_para_value, sf_set，有没有一张表或者视图可以查询出来

可通过查询 v$ifun 视图查看：

```
select * from v$ifun;
```

## 对于 resource 角色的用户跨用户访问其他模式对象，向外键所在表插入修改数据报错：没有引用表[T1]上的权限

【问题描述】：

对于 resource 角色的用户跨用户访问其他模式对象，赋予外键和外键引用主键表的全部 DML 权限，向外键所在表插入修改数据报错：没有引用表[T1]上的权限。具体实例如下：

```
---建立用户 test1 和 test2，并授予默认权限
create user test1 identified by ****;
create user test2 identified by ****;

---在 test1 上创建表 t1 和 t2，并设置主键和外键
create table test1.t1(c1 int,c2 int,primary key(c1));
create table test1.t2(c1 int,c2 int,primary key(c1));
alter table test1.t2 add constraint foreign key(c2) references test1.t1(c1);

---将 test1 上表 t1 和 t2 的所有 DML 权限授予给 test2 用户
grant select,insert,delete,update on test1.t1 to test2;
grant select,insert,delete,update on test1.t2 to test2;

---使用 TEST2 用户登录数据库，执行如下语句：
INSERT INTO TEST1.T1 VALUES(1,1); --执行成功，不报错
COMMIT;
INSERT INTO TEST1.T2 VALUES(1,1);--报错：没有引用表[T1]上的权限
```

【问题解决】：

对于存在外键的表，需要增加 references 权限的授予,赋予如下权限即可。

```
grant references on test1.t1 to test2;
```

## Oracle XML 函数在 DM 中相同功能函数替代案例说明

【问题分析】：

Oracle XML 函数在 DM 中有相应函数替代，ORACLE SYS_XMLAGG 在达梦数据库中可以用 XMLAGG 进行替代，XMLTABLE 可以视实际的情况使用 XMLTABLE 或者 XMLQUERY 来进行替代，同时 XMLTABLE 在写法上和 ORACLE 有一定的差异。以下简要介绍 XMLAGG 和 XMLTABLE 函数：

1.XMLAGG 函数替代案例

```
---Oracle端 SYS_XMLAGG 函数使用方式
select SYS_XMLAGG(xmltype(reg_data)) data from bdc_regdata where sd_id='430811005' and reg_type='14';

---DM 端 XMLAGG 函数使用方式
 select XMLAGG(xmltype(reg_data)) data from bdc_regdata where sd_id='430811005' and reg_type='14'
```

2.XMLTABLE 函数替代案例一

```
---Oracle端 SYS_XMLTABLE 函数使用方式
select * from xmltable('//抵押权登记' passing (select SYS_XMLAGG(xmltype(reg_data)) data 
  from bdc_regdata where sd_id='430811005' and reg_type='14') );

---DM 端 SYS_XMLTABLE 函数使用方式
select XMLQUERY(t.reg_data,'/不动产登记簿信息/抵押权登记信息/抵押权登记') 
   from bdc_regdata t where t.sd_id='430811005' and t.reg_type='14' ;
```

3.XMLTABLE 函数替代案例二

```
---Oracle端 SYS_XMLTABLE 函数使用方式
select szbs
  from xmltable('//目录' passing
                (select SYS_XMLAGG(xmltype(data)) data from clob_test)
                columns data_id VARCHAR2(28) PATH '不动产单元号',
                szbs number PATH '所在本数')
  where data_id = '430811005';

---DM 端 SYS_XMLTABLE 函数使用方式
select szbs
  from CLOB_TEST t,xmltable('//目录' passing (xmltype(t.data)) columns bdcid VARCHAR2(28) PATH '不动产单元号', szbs number PATH '所在本数' )
  where bdcid='430811005';
```

关于 XMLTABLE、XMLAGG、XMLQUERY 以及其他 XML 相关的函数使用方法以及详细介绍请参考《SQL 语言使用手册》（手册位于数据库安装路径 `/dmdbms/doc` 文件夹下）。

## 子查询语句执行报错，提示单行子查询返回多行

报错截图如下：

![报错截图](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-063.png)

【问题解决】：

问题原因：子查询语句输出结果并非单值，主查询语句的 where 筛选条件中 FK_ID 的值与子查询语句的输出结果不能相互匹配，where 条件出现一对多情况。

![执行截图](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-064.png)

解决办法：根据具体业务需求在子查询语句中添加 where 筛选条件，或者在主查询语句中添加 all、any 等关键字，使子查询语句输出结果为单值，确保主查询语句的 where 筛选条件中 FK_ID 的值与子查询语句的输出结果相互匹配。查询语句修改后的执行结果如下所示：
1.在子查询语句中添加 where 筛选条件：

![执行截图](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-065.png)

2.在主查询语句中使用 all 关键字：

![执行截图](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-066.png)

3.在主查询语句中使用 any 关键字：

![执行截图](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-067.png)

## 插入数据到视图，报错：提示违反视图[t1_v]CHECK 约束

【问题描述】：

插入数据到视图，报错，提示违反视图[t1_v]CHECK 约束，报错截图和视图数据来源表的查询结果截图如下：

![报错截图](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-068.png)

视图数据来源表的查询结果：

![来源表的查询结果](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-069.png)

【问题分析】：

出现以上报错是由于在创建视图 t1_v 时指定了 witch check option 关键字，这也就是说，新增或更新后的每一条数据仍然要满足创建视图时指定的 where 条件。因为新增的记录的 id 值为 7，超过了 where 条件中限制的 t.id\<6 的范围，所以报错，提示违反视图[t1_v]CHECK 约束。
解决方法：调整新增的记录的 id 值，使 id 值保持在 t.id\<6 的范围内，语句即可正常执行。

## BLOB 如何转换成可视化字符串

通过以下 SQL 语法进行转换：

```
SELECT
utl_raw.cast_to_varchar2(dbms_lob.substr(表.列))
from 表
```

## 多字段组合唯一性约束限制

【问题描述】：

唯一性约束可以实现对多个字段的组合唯一性约束限制，现有如下类型的唯一性要求，如表 TEST(C1 INT,C2 INT)，要求在 C1 \<0 的情况下，C1 和 C2 的组合不参与唯一性判断；在 C1>=0 时，要求 C1 和 C2 的组合必须唯一。

【问题解决】：

该需求可以通过条件表达式来实现：

```
create unique index index_ttt on test(case when  c1 <0 then null else cast(c1 as varchar)||','||cast(c2 as varchar) end);
```

## 如何查询出 varchar 字段中包含的非数字数据

【问题描述】：

字段中存在 varchar 类型，正常情况存储的为数字，如何查询出字段中包含的非数字数据。如：

```
create table reg_test(c1 int,c2 varchar(100));
insert into reg_test values(1,'1');
insert into reg_test values(2,'11');
insert into reg_test values(3,'12');
insert into reg_test values(4,'1a3');
insert into reg_test values(4,'13a');
insert into reg_test values(4,'13a$');
insert into reg_test values(4,'13￥');
insert into reg_test values(4,'13.');
```

【问题解决】：

可通过以下 SQL 语法解决：

```
select * from reg_test where not regexp_like(c2, '^[[:digit:]]*$');
```

## 如何设置部分用户禁止删除某张数据表

【问题描述】：

针对数据表无法设定 TRUNCATE 操作权限，为了提高安全性，要求对部分用户禁止针对某张数据表做 truncate 和 drop 操作。应该如何设置。

【问题解决】：

可以通过触发器实现权限设定：

```
CREATE OR REPLACE TRIGGER  tri_prevent_drop_truncate
  BEFORE TRUNCATE OR DROP ON DATABASE
BEGIN
  IF :eventinfo.objectname='表名' and :eventinfo.schemaname='用户名'
  THEN
     raise_application_error (-20000, '禁止DROP或TRUNCATE表TEST!');
  END IF;
END;
```

## 使用 to_date 函数进行数据类型转换过程中出现报错：非法的时间日期类型数据

【问题描述】：

使用 to_date 函数进行数据类型转换过程中出现报错：非法的时间日期类型数据。如下图所示：

![报错截图](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-070.png)

【问题解决】：

可以通过修改 DATETIME_FAST_RESTRICT 参数为 0 解决。如下图所示：

![报错截图](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-071.png)

> **注意**
>
> DATETIME_FAST_RESTRICT为动态系统级参数（默认为1），TO_DATE（字符串，FAST格式）或 ALTERSESSION 设置了
> DATE 的格式为 FAST 格式后，CAST 字符串 AS DATE 时： 0：字符串可以带时间； 1：字符串不允许带时间，带时间会报错。
> 其中FAST格式（标准格式）为：YYYY-MM-DD、YYYY/MM/DD、YYYY:MM:DD、YYYY.MM.DD。



## MySQL 迁移到 DM 的存储过程运算结果与原库不一致

【问题描述】：

MySQL 中执行 `SELECT SIGN(65^4)^1*100/10;` 运算结果为 0；而 DM 数据库中执行结果为 11。

【问题分析】：

此问题是由于 MySQL 和 DM 数据库运算优先级不一致导致。

DM 数据库运算优先级：

![DM 数据库运算优先级](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-073.png)

MySQL 数据库运算优先级：

![MySQL 数据库运算优先级](https://download.dameng.com/eco/docs/asset/faq/sql-gramm/faq-sql-gramm-074.png)

因此 MySQL 中该运算 ``SELECT SIGN(65^4)^1*100/10;`` 实际执行顺序为：`select (sign(65^4)^1)*100/10;`
DM 中该运算 ``SELECT SIGN(65^4)^1*100/10;`` 实际执行顺序为：`select sign(65^4)^(1*100/10);`

解决方法：在达梦的过程中实现与 MySQL 一样的顺序，对异运算加括号干预：`select (sign(65^4)^1)*100/10;`

## 创建索引后，在管理工具导航栏索引下看不到索引信息

【问题描述】：

执行以下语句创建索引，命令执行成功，但在管理工具中无法查看索引信息。

```
create table test(id int,name varchar(20));
create index idx_test_id on test('id');
```

【问题解决】：

该问题的原因是引号使用错误：单引号在数据库中表示字符串。
用单引号括起来表示索引目标是一个常量字符串，而不是某个字段。但针对某个 id 对象创建索引时，应该直接使用字段名称，而不应该用单引号括起来。
以上创建过程使用以下命令查看索引类型为函数索引，而非普通索引，所以不会在管理工具中显示。

```
---查看索引类型为函数索引
select index_type from "SYS"."DBA_INDEXES" where index_name='IDX_TEST_ID';
```

正确创建索引方法：

```
---方法一：不加双引号
create index idx_test_id on test(id);

---方法二：用双引号将字段名称括起来
create index idx_test_id on test("ID");
```

## count 查询某张表显示为空，但是 select * from 查询某张表是有数据的

【问题描述】：

count 查询某张表显示为空，但是 select * from 某张表是有数据的。

【问题解决】：

通过看两条语句的执行计划可以发现，两个计划走的索引不一致，select *语句计划走的索引为表自带索引，count 语句计划走的索引为 BM$_33557020，此索引为位图索引，删除此位图索引解决。

```
SQL> explain select count(*) from ZWFW_XWSJZX.XWXT_HISTORYDATA_OPT_LOGS;
      #NSET2:[1，1，4]
           #PRJT2:[1，1，4];exp_num(1)，is_atom(FALSE)
               #AAGR2:[1，1，4];grp_num(0)，sfun_num(1),distinct_f1ag[0]; slave_empty(0)
                    #CSCN2:[1，1，4];INDEX33557021(BM$_33557020) 
已用时间:0.522(毫秒).执行号:0.

SQL> explain select top 2 * from ZWFW_XWSJZX. XWXT_HISTORYDATA_OPT_LOGS;
      #NSET2:[2621，2，482]
           #PRJT2:[2621，2，482];exp_num(12)，is_atom(FALSE)
               #TOPN2:[2621，2，482];top_num(2)
                   #CSCN2:[2621，12871527，482];INDEX33555462(XWXT_HISTORYDATA_OPT LOGS) 
已用时间:0.557(毫秒).执行号:0
```

## 查询数据时怎么样可以不带模式名

【问题解决】：

方式一：通过使用模式所属的用户登录，查询时可以只用写表名。

方式二：通过设置 `set schema 模式名 `,使得其他用户也可以不用加模式名操作该模式的对象。此种方法的前提是需要登录的用户具有该模式对象的权限。

未设置模式时：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20220909094609IM284AR9QE9P0AJQQL)

设置模式后查询成功：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20220909094732Q92YPZBZNQSYCC3X0P)

## 数据库报错 select 包含过多表

【问题描述】：

当 select 后查询不同表的列时，其中表的数量超过了时，报错：“select 包含过多表（不能超过 100 个）”

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2023030209403424DRLAON7QBFG4D9C2)

可以在 v$dm_ini 视图中直接找到 MAX_TABLES_IN_SELECT 参数，对其进行修改，但是需要注意的是，该参数值上限为 150。

查看默认参数值：

```
select * from  v$dm_ini WHERE para_name = 'MAX_TABLES_IN_SELECT';
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20220916153324C35HAC89RC4ULBUTC3)

修改参数值为 110：

```
SP_SET_PARA_VALUE(1,'MAX_TABLES_IN_SELECT',110);
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202209161555082H9ZD7AR6BT40N42YB)

再次执行查询语句，查询成功。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202209161555479QJKB1BNDO2A7XUEMK)

> **注意**
>
> MAX_TABLES_IN_SELECT 该参数属于系统会话级参数，修改参数值后只对该会话生效，参数值上限为150。



## 如何通过 V$SQL_BINDDATA_HISTORY 视图，查看历史 SQL 绑定变量具体的值

【问题解决】

1、开启参数 ENABLE_MONITOR 和 ENABLE_MONITOR_BP；
2、通过以下 SQL 替换其中的 ? 为具体的执行号（exec_id），即可明文查看绑定变量的值。

```
WITH SBH AS
(SELECT EXEC_ID, SF_EXTRACT_BIND_DATA_NUM(BINDDATA, 1) AS ARG_NUM, BINDDATA
FROM V$SQL_BINDDATA_HISTORY S
WHERE EXEC_ID = ?)
SELECT SBH.EXEC_ID,
SBH.ARG_NUM,
LEVEL AS ARG_POS,
SF_EXTRACT_BIND_DATA(BINDDATA, LEVEL, 1) AS ARG_TYPE,
SF_EXTRACT_BIND_DATA(BINDDATA, LEVEL, 2) AS ARG_VAL
FROM SBH
CONNECT BY LEVEL <= ARG_NUM;
```

参数说明：

|**参数**|**默认值**|**属性**|**说明**|
|---|---|---|---|
|ENABLE_MONITOR|1|动态，系统级|用于打开或者关闭系统的监控功能。1：打开；0：关闭。|
|ENABLE_MONITOR_BP|1|动态，系统级|是否监控绑定参数相关信息。该监控项的生效必须是在 ENABLE_MONITOR 打开的情况下。0：不监控；1：监控。|


## 如何通过 SQL 获取指定表的定义

【问题解决】

可以通过调用系统存储过程函数 SP_TABLEDEF() 来获取指定表的定义。

SP_TABLEDEF 定义如下：

```
void
SP_TABLEDEF (
schname varchar(128),
tablename varchar(128)
)
```

功能说明：以结果集的形式返回表的定义，当表定义过长时，会以多行返回。
参数说明：schname：模式名；tablename：表名。
返回值：无。

举例说明：
CALL SP_TABLEDEF('SYSDBA','T2');

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221111094946VIYNNU4J0MP0GQ6HJ3)

返回 SYSDBA 模式下 T2 表的定义。

## 嵌套层次太深

【问题描述】

在单表的某函数转换的字段上创建触发器，在对该字段进行插入和修改时报错：”嵌套层次太深”。

建表：

```
create table stu(id int,name varchar(20),ag varchar(20));
insert into stu values(1,'喜','2022-11-28 15:46:30');
```

触发器：

```
create or replace trigger tri_stu
before update of ag on stu
for each row 
begin 
update stu a set ag=to_char(to_date(ag,'yyyy-MM-dd hh24:mi:ss'),'yyyymmddhh24miss');
end;
```

更新数据：

```
update stu a set ag=to_char(to_date(ag,'yyyy-MM-dd hh24:mi:ss'),'yyyymmddhh24miss');
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202303020941164K1U0KYXQ4V004QZDP)

【问题解决】

将触发器修改为：

```
create or replace trigger tri_stu
before update of ag on stu
for each row 
begin 
 select to_char(to_date(:NEW.ag,'yyyy-MM-dd hh24:mi:ss'),'yyyymmddhh24miss') into :NEW.ag from dual;
end;
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230302094145L5414LFLKCPQFZ4KEM)

再次更新数据成功：

```
update stu a set ag=to_char(to_date(ag,'yyyy-MM-dd hh24:mi:ss'),'yyyymmddhh24miss');
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230302094215AXQIF51G2TFMM7YR4J)

查询数据：

```
select * from stu;
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2022120115405250IFDNCVSXKKCMR888)

## 为什么有时候简单的 insert 语句会阻塞查询事务。

【问题分析】

按照一般理解，insert 操作不应阻塞 select 查询事务。insert 操作在 DM 中通常只使用意向锁，但在某些情况下 insert 操作会为对象加上 X 独占锁，导致需要查询该表的其他事务无法上 IS 意向共享锁，形成了查询被阻塞的现象。

例如对存在位图索引的表进行 insert 操作时，将会对该表加上独占锁，导致需要操作该表的其他事务均被阻塞。因此，位图索引要谨慎使用，否则可能顾此失彼。尤其不应在频繁更新的表上创建位图索引，否则可能严重影响业务系统正常运行。这里仅以位图索引举例，实际可能有不同的原因导致此现象，需要根据场景中的事务与锁情况具体分析。

示例如下：

  1. 在第一个会话中执行以下命令。



```
create table T1 (ID int);
create bitmap index idx_id on T1(ID);
insert into T1 values (1);
---（不提交）
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230302094331RLNWUYAXPEE26QYKUG)

  2. 新建会话查询。



```
select * from T1;
--（被阻塞）
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202212121442387AK59XGKJ2PCVJ5JDN)

  3. 在第一个会话中查询锁。



```
select l.* from v$lock l,sysobjects o where l.table_id = o.id and o.name='T1';
--（T1 表被加了独占锁）
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221212144606K8I3MD98DAXRZGCDZ9)

## 表数量与实际的表数量不一致

【问题描述】

使用 SQL 语句 `select * from all_tables;` 查出来的表数量与实际的表数量不一致，存在个别表名并非真正的表的情况，这些表在管理工具的模式下看不到，如 SREF_CON_TAB 开头的表。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202303020943590YY87GEIOC8XEKING7)

【问题解决】

达梦创建外键，该外键关联的字段是本表的字段时，会创建 3 张临时表，表名为：

  * SREF_CON_TAB+ 外键约束 id+_LEVEL
  * SREF_CON_TAB+ 外键约束 id+_REFING
  * SREF_CON_TAB+ 外键约束 id+_REFED



如果想根据这里的临时表信息查询其基表信息，请使用如下 SQL：

```
select
       b.id           ,
       owner          ,
       constraint_name,
       r_owner        ,
       table_name
from
       SYS.DBA_CONSTRAINTS a,
       sysobjects b
where
       a.CONSTRAINT_NAME=b.name
   and b.id            in
       (
               select
                       REGEXP_SUBSTR(table_name, '[0-9]+', 1)
               from
                       all_tables
               where
                       tablespace_name='TEMP'
                   and TABLE_NAME like 'SREF_CON_TAB%'
       );
```

## 如何通过地图上两个点的经纬度计算距离

【问题解决】

执行以下 SQL 命令创建函数，可以通过创建的函数利用经纬度计算出地图上两个点距离：

  1. 创建弧度的函数



```
CREATE OR REPLACE FUNCTION Radian(d number) RETURN NUMBER
is
PI number :=3.141592625;
begin
return  d* PI/180.0;
end ;
```

  2. 经纬度计算距离的函数



```
CREATE OR REPLACE FUNCTION Get_Distance(lat1 number,
                                      lng1 number,
                                      lat2 number,
                                      lng2 number) RETURN NUMBER is
 earth_padius number := 6378.137;
 radLat1      number := Radian(lat1);
 radLat2      number := Radian(lat2);
 a            number := radLat1 - radLat2;
 b            number := Radian(lng1) - Radian(lng2);
 s            number := 0;
begin
 s := 2 *
      Asin(Sqrt(power(sin(a / 2), 2) +
                cos(radLat1) * cos(radLat2) * power(sin(b / 2), 2)));
 s := s * earth_padius;
 s := Round(s * 10000) / 10000;
 return s;
end;
```

  3. 测试验证



故宫坐标:（116.40355,39.923678）

天坛坐标:（116.417133,39.887923）

```
select Get_Distance('116.40355','39.923678','116.417133','39.887923') from dual
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202301111037048PWVT84XFWX36HR0EE)

## dblink 访问远程库中的大字段表数据时报错：字符串截断

【问题描述】

本地库通过 dpi 方式访问远程库中的大字段表数据时报错：字符串截断

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230302094427E0GL4P8Z99PCG8OAWO)

【问题解决】

调整参数 DBLINK_LOB_LEN 的值大于表中 BLOB 大字段数据长度，例如：

  1. 修改库 1 和库 2 ：DBLINK_LOB_LEN=512。



```
SP_SET_PARA_VALUE(1,DBLINK_LOB_LEN,512);
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230302094454LTGT3E20Z3F0WMSV3Y)

  2. 库 1 创建 BLOB 表，并插入 6 张大小为 276K 的图片（512>276）。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230302094519ORVIL0YA24AM3UFXCH)

  3. 库 2 通过 dblink 成功查询库 1 大字段表。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230302094549QEIN4HVRH7H2GP01SA)

## 如何不退出 disql 使用代理功能

【问题解决】

授予代理用户代理权限。

例如：被代理用户 USER1 的口令和密码为 USER111/USER111，代理用户 SYSDBA 的用户名和密码为 SYSDBA/SYSDBA 。

  1. 授予代理用户代理权限。



```
ALTER USER USER1 GRANT CONNECT THROUGH SYSDBA；
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202303020947469SMV1A26AMGQ2XILSX)

  2. 查看当前用户。



```
select user;
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230302094812HW57A87VOOU3AE3VT8)

  3. 使用代理用户 SYSDBA 登录被代理用户 USER1 的数据库。



```
conn SYSDBA/SYSDBA#{proxy_client=USER1};
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202301130915344OBLDW03RFEXHIL12A)

  4. 查看当前用户。



```
select user;
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202303020952249R0AM1RPJVKI470OUD)

  5. 回收代理权限语句。



```
alter user USER1 revoke connect through sysdba;
```

## 如何通过 sql 查询表的分布类型、分布列、以及创建时间

【问题解决】

可以通过以下 sql 查询表的分布类型、分布列、以及创建时间。

```
select
        T1.table_name,
        dis_type     ,
        列名                ,
         分布列名 ,
        CREATED
from
        (
                SELECT
                        a.SCHEMA_NAME,
                       -- a.TABLE_NAME ,
                        a.TABLE_TYPE ,
                        a.DIS_TYPE   ,
                        '("'
                        ||c.column_name
                        ||'")' 列名           ,
                        to_char( a.DIS_COLS) 分布列名,
                        c.TABLE_NAME
                from
                        (
                                select
                                        *
                                from
                                        ALL_TAB_COLUMNS b
                                where
                                        b.OWNER      ='模式名'
                                    AND b.COLUMN_NAME='PRIPID'
                        )
                        c
                left join ALL_TABLES_DIS_INFO a
                on
                        c.TABLE_NAME = a.TABLE_NAME
                        and c.OWNER      =a.SCHEMA_NAME
                where
                         a.SCHEMA_NAME='模式名'
        )
        T1
        left join 
        DBA_OBJECTS T2
        on  T2.OBJECT_NAME   =T1.TABLE_NAME
        and t2.OWNER=t1.SCHEMA_NAME
where
    T1.schema_name='模式名'
    and T2.OBJECT_TYPE='TABLE'
```

查询结果如下：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230223091544K1WW8BIOPNRKZLN2WX)

## 达梦等价实现 MySQL 的列转行拼接功能

【问题描述】

达梦如何等价实现 MySQL 的列转行拼接功能？

【问题解决】

  1. 创建建测试表；



```
create table t1 (id int,name varchar(20));
insert into t1 values(1,'a1');
insert into t1 values(2,'b');
insert into t1 values(3,'c');
insert into t1 values(4,'d');
insert into t1 values(5,'e');
insert into t1 values(6,'ff');
insert into t1 values(7,'gg');
insert into t1 values(8,'h');
insert into t1 values(9,'i');
commit;
```

  2. MySQL 列转行拼接换行符；



```
SELECT GROUP_CONCAT(name SEPARATOR '\n') FROM t1 WHERE id IN (1,2,7,9);

```

  3. DM 列转行拼接换行符。



```
select listagg(name,chr(10)) from t1 WHERE id IN (1,2,7,9);
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230302095848B74P6DD8AU6RW85MUZ)

## 如何并行创建分区表上的索引

【问题描述】

如何并行创建分区表上的索引。

【问题解决】

  1. 创建含无效索引类型的分区表



```
CREATE TABLE TEST(C1 INT, C2 INT) PARTITION BY HASH(C1) PARTITIONS 3 ;
```

  2. 创建无效索引



```
CREATE INDEX INDEX_C1 ON TEST(C1) UNUSABLE;
```

  3. 并行重建分区表上的索引,最大支持并行数 8



```
ALTER INDEX INDEX_C1 REBUILD SHARE ASYNCHRONOUS 8;
```

## 达梦是否支持 Oracle 中的 userenv() 函数？

【问题描述】

Oracle 中 userenv 函数返回关于当前会话的信息。此信息可以用于编写一个应用程序特定的审计跟踪表或确定特定语言的角色目前使用的会话。

Oracle 中 userenv 函数有两种用法：

```
--第一种用法
select userenv(‘isdba’) from dual;
--第二种用法
select SYS_CONTEXT('USERENV','ISDBA') isdba from dual;
```

达梦是否支持 Oracle 中的 userenv() 函数？

【问题解决】

达梦数据库支持 userenv 函数获取当前会话的上下文信息，但只支持 oracle 中的第二种用法：

```
select SYS_CONTEXT('USERENV','ISDBA') isdba from dual;
```

达梦数据库中 userenv 函数的参数说明:

|**参数**|**功能**|
|--|--|
|CURRENT_SCHEMA|返回当前模式名|
|CURRENT_SCHEMAID|返回当前模式 ID|
|CURRENT_USER|返回当前的用户名|
|CURRENT_USERID|返回当前的用户 ID|
|DB_NAME|返回数据名|
|HOST|返回客户端的主库名|
|INSTANCE_NAME|返回实例名|
|IP_ADDRESS|返回客户端的 IP 地址|
|ISDBA|如果当前会话用户拥有 DBA 权限，则返回 TRUE，否则，返回 FALSE|
|LANG|语言包简写，中文返回―CN‖，英文返回―EN‖|
|LANGUAGE|语言包，返回库的编码方式|
|NETWORK_PROTOCOL|通信协议|
|SERVER_HOST|实例运行的主机名|
|SESSION_USER|会话的用户名|
|SESSION_USERID|会话的用户 ID|
|SID|当前会话的 ID|


## 对象视图存在，但查询报错："指定的对象数据库中不存在"

【问题描述】

在 dba_objects 视图中查询到该对象存在，且 dba_objects 里的 object_type 为 VIEW。

```
select owner,object_name,object_type,status from dba_objects where object_name='M_VIEW';
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230321101619LR9C7EHXEY8H81LOC2)

在通过以下 SQL 查询时报错 -6815："指定的对象数据库中不存在"

```
select dbms_metadata.get_ddl('VIEW','M_VIEW','SYSDBA')
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230321101546KONZIMACQPMBKZP9WF)

【问题分析】

通过以下 SQL 查询到该对象是个物化视图

```
sp_viewdef('SYSDBA','M_VIEW');
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202303211018352IE0BMFC32JHO0TFBG)

由于该对象是个物化视图在使用 dbms_metadata.get_ddl 方法时 OBJECT_TYPE 参数应使用物化视图对应的类型名称即 MATERIALIZED_VIEW。

【问题解决】

调整 dbms_metadata.get_ddl 方法中 OBJECT_TYPE 参数使用物化视图对应的类型名称 MATERIALIZED_VIEW。

```
select to_char(dbms_metadata.get_ddl('MATERIALIZED_VIEW','M_VIEW','SYSDBA') );
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230321102421CS8FB0LKECRCM2FLUW)

## 当前用户无法调用其他用户创建的存储函数/存储过程/包

【问题描述】

当前用户无法调用其他用户创建的存储函数/存储过程/包

例如：用户 A 创建如下存储函数：

```

CREATE TABLE TEST (ID NUMBER,NAME VARCHAR2(20));
INSERT INTO TEST VALUES(101,'TONY');
COMMIT;
CREATE OR REPLACE FUNCTION VALID_ID(vid in number) return boolean
is
  temp_id number;
begin
  select id into temp_id from test where id=vid;
  return (true);
  exception when no_data_found then return (false);
end valid_id;
```

B 用户需要直接调用 A 用户创建的函数。

```
--B用户执行
declare
rtn boolean;
begin
rtn := VALID_ID(101);
print rtn;
end;
/
```

报错：

```
-2207: 第4 行附近出现错误:无法解析的成员访问表达式[VALID_ID]

-5505: 没有执行[VALID_ID]对象权限
```

【问题分析】

第一个报错是由于用户 B 下不存在或未找到相关数据库对象，可通过创建同义词，并开启 ENABLE_PL_SYNONYM 参数进行处理。

第二个报错是由于用户 B 没有执行对象的权限，可通过授权解决，授权后用户 B 可以正常调用用户 A 下的对象。

【问题解决】

问题一：-2207: 第 4 行附近出现错误:无法解析的成员访问表达式 [VALID_ID]。

  1. 开启 ENABLE_PL_SYNONYM 参数，可以通过全局同义词执行非系统用户创建的包或者存储过程。1 是，0 否。



```
sp_set_para_value(1,'ENABLE_PL_SYNONYM',1);
```

  2. 用户 B 创建同名同义词。



```
CREATE OR REPLACE SYNONYM SCHEMA_B.VALID_ID FOR SCHEMA_A.VALID_ID;
```

问题 二：-5505: 没有执行[VALID_ID]对象权限。

  * DBA 用户执行授权 B 用户执行 A 用户存储函数 VALID_ID 的权限。



```
GRANT EXECUTE ON SCHEMA_A.VALID_ID TO SCHEMA_B;
```

## disql 如何同时给多条 sql 赋予变量值？

【问题解决】

例如：有如下两条查询 SQL：

```
select * from bus_car where color='&color';
select * from bus_car2 where color='&color';
```

现在需要同时为两条 SQL 的 color 赋与相同的值进行查询，步骤如下：

  1. 编辑 SQL 脚本 q.sql。



```
DEF color=&color;
select * from bus_car where color='&color';
select * from bus_car2 where color='&color';
```

  2. 登录 disql 执行脚本。



```
[dmdba@192-168-164-6 bin]$ ./disql SYSDBA/SYSDBA

服务器[LOCALHOST:5236]:处于普通打开状态
登录使用时间 : 2.363(ms)
disql V8
SQL> start q.sql

```

  3. 根据提示输入变量值。



```
SQL> DEF color=&color;
输入 color的值:白色
原值 1:DEF color=&color;
新值 1:DEF color=白色;

```

  4. 为变量赋值成功并输出查询结果。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202303311451480E7JUATVP8U4KDX4NT)

## 如何查询列存储分区表的单列使用大小

【问题解决】

如果想要查询列存储存分区表的单列使用大小可以参考以下方法。

  1. 创建查询列存储分区表的单列使用大小函数 huge_col_use_space。



```
create or replace function huge_col_use_space(sch_name varchar, tab_name varchar, col_name varchar) return bigint
as
type name_arr_t is table of varchar;
sch_id  int;
tab_id  int;
col_id  int;
ptab_id int;
name_arr  name_arr_t;
sz_single bigint;
sz_totle  bigint;
sql       varchar;
i         int;
begin
--1、获取schid
select id into sch_id from sysobjects where type$='SCH' and schid=0 and name=sch_name;
print 'sch_id:'||sch_id;
--2、获取tabid
select id, pid into tab_id, ptab_id from sysobjects where type$='SCHOBJ' and schid=sch_id and name=tab_name;
print 'tab_id:'||tab_id;
--3、如果要计算的是个子表，则获取其根表的id,用于步骤4获取colid（如果是以colid作为参数的话，步骤3、4是不需要的）
if (ptab_id = -1) then

ptab_id = tab_id;
else
 select id into ptab_id from sysobjects where pid=-1 connect by prior pid=id start with id=pid;
end if;
print 'root_tab_id:'||ptab_id;
--4、获取colid
select colid into col_id from syscolumns where id=ptab_id and name=col_name;
print 'col_id:'||col_id;
--5、获取所有叶子子表信息,层次查询的叶子节点就是叶子子表
select name bulk collect into name_arr from
(select name, pid, id from sysobjects where type$='SCHOBJ' and schid=sch_id and name like tab_name||'%')
where connect_by_isleaf=1
connect by prior id=pid start with id=tab_id;
--6、查询各个叶子子表的辅助表统计该列的长度
sz_totle = 0;
for i in 1..name_arr.count loop
sql = 'select sum(n_len) into ? from ' ||name_arr(i)||'$AUX where colid='||col_id;
execute immediate sql using out sz_single;
print 'tab['||name_arr(i)||'] size:'||sz_single;
if sz_single is not null then
  sz_totle = sz_totle + sz_single;
end if;
end loop;
return sz_totle;
end;
/
```

  2. 调用该函数 huge_col_use_space 查询列存储分区表的单列使用大小。



```
select  huge_col_use_space （'用户名','表名','列名'）；
```

## 如何查询列存储分区表的物理占用空间大小

【问题解决】

如果想要查询列存储分区表的物理占用空间大小可以参考以下方法。

  1. 创建查询列存储分区表的物理占用空间大小存储过程 TABLE_COUNT。



```
CREATE OR REPLACE PROCEDURE SYSDBA.TABLE_COUNT(V_SCH_NAME VARCHAR(30),V_TABLE_NAME VARCHAR(128)) AS
BEGIN
SELECT /*+ CASE_WHEN_CVT_IFUN(0) */
V_TABLE_NAME AS TABLE_NAME,--表名
IS_HUGE,--是否为HUGE表
IS_PARTITION, --是否为分区表
SF_GET_TABLE_COUNT(V_SCH_NAME,V_TABLE_NAME) AS ROW_COUNT, --表行数
CASE IS_HUGE WHEN 'N' THEN TABLE_USED_SPACE(V_SCH_NAME,V_TABLE_NAME) * SF_GET_PAGE_SIZE() / 1024.0 /1024 ELSE HUGE_TABLE_USED_SPACE(V_SCH_NAME,V_TABLE_NAME) END || 'M' AS USED_SPACE_MB --表大小，单位MB
FROM (
SELECT
V_TABLE_NAME,
CASE WHEN (INFO3 & 0X3F)  BETWEEN 0X21 AND 0X27 THEN 'Y' ELSE 'N' END IS_HUGE, --判断表类型
(SELECT CASE WHEN COUNT(*) > 0 THEN 'Y' ELSE 'N' END FROM SYS.SYSOBJECTS WHERE PID = X.ID AND SUBTYPE$='UTAB' AND SCHID = (SELECT ID FROM SYS.SYSOBJECTS WHERE TYPE$='SCH' AND NAME = V_SCH_NAME)) IS_PARTITION--判断是否为分区表
FROM SYS.SYSOBJECTS X
WHERE SCHID = (SELECT ID FROM SYS.SYSOBJECTS WHERE TYPE$='SCH' AND NAME = V_SCH_NAME) AND
     SUBTYPE$='UTAB' AND PID = -1 AND NAME=V_TABLE_NAME
);
END;
/
```

  2. 调用该存储过程 TABLE_COUNT 查询列存储分区表的单列使用大小。



```

call TABLE_COUNT('模式名','表名')；
```

## 达梦数据库有没有用于分割字符串的函数？

【问题描述】

达梦数据库有没有用于分割字符串的函数？例如 SQL server 的 string_split 函数和 Oracle 的 split 函数。

【问题解决】

可以通过创建分割字符串函数来实现，以下提供两种方法供参考。

方法一：

```
CREATE OR REPLACE TYPE TYPE_SPLIT AS TABLE OF VARCHAR2 (4000);
CREATE OR REPLACE FUNCTION SPLIT(P_STRING VARCHAR2, P_SEP VARCHAR2 := ',')
 RETURN TYPE_SPLIT
 PIPELINED IS
 IDX  PLS_INTEGER;
 V_STRING VARCHAR2(4000) := P_STRING;
BEGIN
 LOOP
   IDX := INSTR(V_STRING, P_SEP);
   IF IDX > 0 THEN
     PIPE ROW(SUBSTR(V_STRING, 1, IDX - 1));
     V_STRING := SUBSTR(V_STRING, IDX + LENGTH(P_SEP));
   ELSE
     PIPE ROW(V_STRING);
     EXIT;
   END IF;
 END LOOP;
END;
```

示例：调用函数 split 。

```
select * from table(SPLIT('北京|上海|广州|深圳','|'));
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230413104642MICKLTU6AQB231S9KB)

方法二：

```
create or replace type t_test as object(col varchar2(500));
create or replace type t_test_table as table of t_test;
CREATE or replace FUNCTION f_splitSTR(
s   varchar(8000),--待分拆的字符串
split_ varchar(10)  --数据分隔符
)RETURN t_test_table
AS
DECLARE
v_test t_test_table := t_test_table();
BEGIN
WHILE instr(s,split_)>0
loop
v_test.extend();        --   append   one   null   element
v_test(v_test.count) := t_test(LEFT(s,instr(s,split_)-1));
s=STUFF(s,1,instr(s,split_),'');
END loop;
v_test.extend();  
v_test(v_test.count) := t_test(s);
return v_test;
END;
```

示例：调用函数 f_splitSTR 。

```
select * from table(f_splitSTR('北京|上海|广州|深圳','|'));
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230413104703TYOZR0TFSPFZCPJVS9)

## 如何从动态 SQL 中返回新记录 ROWID 的值

【问题解决】

例如有如下表 T3。

```
create table t3(name varchar2(20),pdate timestamp(6));
```

创建存储过程返回新记录 ROWID 的值：

```
create or replace procedure rowidtest (tname varchar2,cprowid out varchar2) is
begin
insert into t3 values (tname,sysdate)
returning rowid into cprowid;
end;
/
```

## 使用 length（）查询 blob 列字节长度时报错：“参数不兼容”

【问题描述】

SQL 语句查询 blob 列字节长度时使用 length(BLOB 列)，报错“参数不兼容”。

【问题分析】

达梦中 length 函数用于字符串，所以字段类型是 blob 时无法使用 length 函数。

【问题解决】

以下提供两种解决方案。

方案 1：使用系统包 dbms_Lob.Getlength。

```
SELECT DBMS_LOB.GETLENGTH(GRADE) FROM TEST1;
```

方案 2：使用 lengthb 函数。

```
SELECT LENGTHB(GRADE) FROM TEST1;
```

## 如何通过 sql 语句更改单列 blob 图片

【问题解决】

可以通过创建更新图片的存储过程来更新图片。

例如：

创建测试表 :T3；

```
CREATE TABLE "T3"("C" BLOB,"ID" INTEGER);
```

创建存放图片目录；

```
CREATE OR REPLACE DIRECTORY "IMAGES" AS 'D:\Program Files\deckpt\yule';--具体路径
```

创建更新图片存储过程；

```
CREATE OR REPLACE PROCEDURE IMG_UPDATE
(TID INT,
FILENAME VARCHAR(200))AS
F_LOB BFILE;
B_LOB BLOB;
BEGIN
--update 语句
update t3 set c=EMPTY_BLOB () where id=TID
RETURN c INTO B_LOB;
F_LOB:= BFILENAME ('IMAGES', FILENAME);
DBMS_LOB.FILEOPEN (F_LOB, DBMS_LOB.FILE_READONLY);
DBMS_LOB.LOADFROMFILE (B_LOB, F_LOB,
DBMS_LOB.GETLENGTH (F_LOB));
DBMS_LOB.FILECLOSE (F_LOB);
COMMIT;
END;
```

执行存储过程更新图片，传入 id 值和图片名称。

```
CALL IMG_UPDATE(1,'2.jpg');
```

## 表数据分组拆分合并，同时列转多行

【问题描述】

有如下表：

```
CREATE TABLE T1 (ID INT,NAME VARCHAR(20));
INSERT INTO T1 VALUES(1,'张三');--姓名
INSERT INTO T1 VALUES(2,'100');--语文
INSERT INTO T1 VALUES(3,'90');--数学
INSERT INTO T1 VALUES(4,'88');--英语
INSERT INTO T1 VALUES(5,'99');--历史
INSERT INTO T1 VALUES(6,'98');--地理
INSERT INTO T1 VALUES(7,'77');--生物
INSERT INTO T1 VALUES(8,'95');--物理
INSERT INTO T1 VALUES(9,'87');--化学
INSERT INTO T1 VALUES(10,'87');--思想政治
INSERT INTO T1 VALUES(1,'李四');--姓名
INSERT INTO T1 VALUES(2,'100');--语文
INSERT INTO T1 VALUES(3,'90');--数学
INSERT INTO T1 VALUES(4,'88');--英语
INSERT INTO T1 VALUES(5,'99');--历史
INSERT INTO T1 VALUES(6,'98');--地理
INSERT INTO T1 VALUES(7,'77');--生物
INSERT INTO T1 VALUES(8,'95');--物理
INSERT INTO T1 VALUES(9,'87');--化学
INSERT INTO T1 VALUES(10,'87');--思想政治
COMMIT;
```

计划实现如下效果：
姓名 语文 数学 英语 历史 地理 生物 物理 化学 思想政治
张三 100 90 88 99 98 77 95 87 87
李四 100 90 88 99 98 77 95 87 87

【问题解决】

通过使用 rownum 、case when 实现一列转多行。

```
SELECT MAX(C1) 姓名,MAX(C2) 语文,MAX(C3)数学,MAX(C4) 英语,MAX(C5) 历史,MAX(C6)地理,MAX(C7)生物,MAX(C8)物理,MAX(C9)化学,MAX(C10)思想政治  FROM (
SELECT CASE WHEN MOD(ROWNUM,10) =1 THEN NAME END AS C1,
      CASE WHEN MOD(ROWNUM,10) =2 THEN NAME END  AS C2,
      CASE WHEN MOD(ROWNUM,10) =3 THEN NAME END  AS C3,
      CASE WHEN MOD(ROWNUM,10) =4 THEN NAME END  AS C4,
      CASE WHEN MOD(ROWNUM,10) =5 THEN NAME END  AS C5,
      CASE WHEN MOD(ROWNUM,10) =6 THEN NAME END AS C6,
      CASE WHEN MOD(ROWNUM,10) =7 THEN NAME END  AS C7,
      CASE WHEN MOD(ROWNUM,10) =8 THEN NAME END  AS C8,
      CASE WHEN MOD(ROWNUM,10) =9 THEN NAME END  AS C9,
      CASE WHEN MOD(ROWNUM,10) =0 THEN NAME END  AS C10    FROM T1)
GROUP BY TRUNC(ROWNUM/10.0 +0.9);
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202306151145430DUBAIFBTQNZY0FE1C)

## 通过数据库将中文转换为拼音

【问题描述】

如何通过数据库将中文转换为拼音。

【问题解决】

方法一：使用方法实现。

```
--小写全拼
select FN_GETPY('中华人民共和国')

zhonghuarenmingongheguo

--大写全拼
select FN_GETPY('中华人民共和国',1)

ZHONGHUARENMINGONGHEGUO

--首字母大写全拼
select FN_GETPY('中华人民共和国',2)

ZhongHuaRenMinGongHeGuo

--小写字母简拼
select FN_GETPY('中华人民共和国',3)

zhrmghg

--大写字母简拼
select FN_GETPY('中华人民共和国',4)

ZHRMGHG
```

方法二：使用自定义函数实现，自定义函数见附件“中文转拼音函数”。

[中文转拼音函数.txt](https://eco.dameng.com/eco-file-server/file/eco/download/20230615152426GGFLCUB6EJIX0HNJ7C)

测试如下：

```
select fn_getpy('你好');
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230615152614AXTNY4WXT76OR9AVVC)

注意：多音字的拼音转换只能转成一个音可能会不正确，生僻字可能转换不正确。

## 如何通过数据库生成未来一段时段的年月数据库

【问题描述】

如何通过数据库生成未来一段时段的年月数据库，用于在应用系统中作为字典表使用。

生成年月在数据库事务系统及分析系统中都可能会使用到。例如：OLAP 系统中年月数据库可以作为维度表来使用，OLTP 系统中年月数据可以作为表单的选项字典等。

【问题解决】

可以参考如下 SQL 生成年月数据，例如：生成 2023-01 至 2050-12 区间的年月数据。

```
select
        to_char(add_months(to_date('2023-01', 'yyyy-mm'), rownum-1), 'yyyy') year,
        to_char(add_months(to_date('2023-01', 'yyyy-mm'), rownum-1), 'mm') month
from
        dual connect by rownum<= months_between(to_date('2050-12', 'yyyy-mm'), to_date('2023-1', 'yyyy-mm'))+1
```

## 使用 sql 复制表结构无法将主键和约束复制到新表

【问题描述】

使用 AS 复制相同的表结构。

```
CREATE TABLE table_name AS SELECT * FROM other_table WHERE 1=2;(或者 LIMIT 0)
```

使用 LIKE 复制相同的表结构。

```
CREATE TABLE table_name LIKE other_table;
```

以上这两种方式都无法将主键和约束复制到新表上去。

【问题解决】

可以使用如下方法来复制表结构。

  1. 通过系统包查询建表的 ddl 语句。



```
SELECT DBMS_METADATA.GET_DDL('TABLE','表名','模式名');
```

  2. 修改 ddl 语句，更改表名重新执行 ddl 语句即可创建相同表结构的新表。



## 如何将 16 进制数据转换为 10 进制或者将 10 进制数据转换为 16 进制

【问题解决】

16 进制转换为 10 进制，可以通过 to_number 函数实现，如下：

```
select to_number('十六进制串','xxx') from dual;
```

10 进制转换为 16 进制，可以通过 to_char 函数转换，如下：

```
select to_char(123,'xxx') from dual;
```

to_number 函数和 to_char 函数的详细使用方法可参考数据库安装目录 doc 目录下的《DM8_SQL 语言使用手册》。

## 空间数据如何调用 ST_WKT 将几何类型数据通过文本方式表示出来

【问题解决】

在达梦数据库中，ST_WKT 是指 Well-Known Text（WKT）格式，用于表示几何类型的数据。ST_GEOMETRY 是达梦数据库提供的一个扩展函数，可以将几何类型的数据转换为 WKT 格式，或将 WKT 格式的数据转换为几何类型的数据。如何调用 ST_WKT 可参考如下方法：

  1. 首先需要调用系统过程 SP_INIT_GEO_SYS(1) 创建 DMGEO 包；



```
SP_INIT_GEO_SYS(1);
```

  2. 创建一个包含几何类型数据的表，例如；



```
CREATE TABLE mytable (id INT, geom ST_GEOMETRY);
```

  3. 使用 INSERT 语句将几何类型的数据插入到表中；



```
INSERT INTO mytable (id, geom) VALUES (1, dmgeo.ST_GeomFromText('POINT(1 1)',0));
--查询表数据
select * from mytable
```

  4. 使用 SELECT 语句查询包含几何类型数据的表，并使用 ST_AsText() 函数将几何类型的数据转换为 WKT 格式。



```
SELECT id, dmgeo.ST_AsText(geom) FROM mytable;
```

## 使用达梦管理工具创建自增列表后，如何获取上一次 insert 语句的 id

【问题解决】

管理工具中创建自增表默认使用序列方式实现自增，需要 @@IDENTITY 获取上一次 insert 语句的 id ，例如：

  1. 创建序列自增表；



```
CREATE TABLE new_employees1
(
id_num int IDENTITY(1,1),
fname varchar (20),
minit char(1),
lname varchar(30)
);
```

  2. 插入数据；



```
insert into new_employees1(fname,minit,lname) values('test','d','test1');
```

  3. 通过 @@IDENTITY 获取上一次 insert 语句的 id。



```
select @@IDENTITY;
```

达梦有两个自增方式，上述为序列自增，已下为自增列方式。

  1. 创建自增列表；



```
CREATE TABLE "SYSDBA"."TABLE_8"
(
"ID" INT PRIMARY KEY AUTO_INCREMENT ,
"NAME" CHAR(10)
);
```

  2. 插入数据；



```
insert into TABLE_8(name) values('test');
```

  3. 通过 LAST_INSERT_ID 函数获取上一次 insert 语句的 id。



```
select LAST_INSERT_ID；
```

## 管道表函数调用方法

【问题描述】

达梦数据库中如何调用管道表函数。

【问题分析】

管道表函数是可以返回行集合的函数，用户可以像查询数据库表一样查询它。目前 DM 管道表函数的返回值类型暂时只支持 VARRAY 类型和嵌套表类型。

【问题解决】

由于管道表函数的返回参数类型为集合，可作为表函数使用，表函数是在 from 子句中以 table() 调用，达梦目前支持调用的方式如下：

```
SELECT * FROM TABLE(F_PIPE);
```

示例：

  1. 创建类型。



```
CREATE OR REPLACE TYPE OBJ_RECORD AS OBJECT (
	ID NUMBER,
	DAYTIME DATE
);
```

  2. 创建嵌套表。



```
CREATE OR REPLACE TYPE TYPE_OBJ_RECORD AS TABLE OF OBJ_RECORD;
```

  3. 创建管道函数，并将数据放入管道中。



```
CREATE OR REPLACE FUNCTION F_PIPE
RETURN TYPE_OBJ_RECORD
PIPELINED
AS
BEGIN
PIPE ROW(OBJ_RECORD(1, SYSDATE()));
PIPE ROW(OBJ_RECORD(2, SYSDATE()+1));
RETURN;
END;
/
```

  4. 调用管道表函数。



```
SELECT * FROM TABLE(F_PIPE);
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230713111722DURT054H9LR82LWPPF)

## SQL 无法查询到对应数据

【问题描述】

数据库中的数据存在换行符或回车符，利用 Like ‘回车’无法查询到对应数据。

【问题解决】

可以利用 '%'||chr(13)||'%'  或 '%'||chr(10)||'%' 进行模糊匹配。

'%'||chr(10)||'%' 表示换行符。
'%'||chr(13)||'%' 表示回车符。

用法如下：

```
SELECT * from 表名称 WHERE "字段" like '%'||chr(13)||'%'   or "字段" like '%'||chr(10)||'%';
```

## 存储过程报错：“数据类型不匹配”

【问题描述】

如下存储过程执行时报错：“数据类型不匹配”。

```
DECLARE
   REC INT;
begin
   for REC in(SELECT 1 FROM DUAL)
   loop
       print REC;
   end loop;
end;
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230727105306LZRAXCXBWEO9ZA0GG8)

【问题分析】

该报错是由于 in 方式取值，引用的值不明确。

【问题解决】

改写 SQL 如下：

```
DECLARE
   REC INT;
begin
   for REC in(SELECT 1 A FROM DUAL)
   loop
       print REC.A;
   end loop;
end;
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202307271052234IN3NI3XACKSKFP30W)

## 达梦 like 模糊匹配可以走索引吗？

【问题解决】

对于 like 前缀模糊匹配或者 like 后缀模糊匹配是可以走索引的，但对于 like 前后缀都进行模糊匹配时没办法使用到索引。

具体分析如下：

  1. 对于后缀模糊匹配，name like 'str%'，可以直接用到 name 列的普通索引进行匹配。
  2. 对于前缀模糊匹配，name like '%str'，可以通过对 name 列创建反向索引使前缀模糊匹配自动应用到该反向索引进行匹配，具体方法可参考如下：



  （1）对 name 列创建反向索引。

```
CREATE  INDEX "IDX_TEST_NAME" ON "TEST_INDEX"("REVERSE"(NAME));
```

  （2）并修改参数 STR_LIKE_IGNORE_MATCH_END_SPACE 为 0，可通过会话级修改只影响当前会话或系统级修改影响所有会话。

  会话级修改：

```
SF_SET_SESSION_PARA_VALUE('STR_LIKE_IGNORE_MATCH_END_SPACE',0);
```

  系统级修改：

```
SP_SET_PARA_VALUE(1,'STR_LIKE_IGNORE_MATCH_END_SPACE',0);
```

  此时前缀模糊匹配就可以自动应用到该反向索引进行匹配。

  3. 对于前后缀模糊匹配，name like '%str%'，目前没有好的办法令该条件使用索引扫描。实际使用中要尽量减少使用该模糊过滤方法，如果实在需要使用尽量创建覆盖索引来避免全表扫描。



覆盖索引：select 的数据列只用从索引中就能够取得，不必读取数据行，换句话说查询列要被所建的索引覆盖。

## 在达梦数据库中如何替代 sql server 中 for xml  path 功能的函数？

【问题描述】

达梦数据库是否有替代 sql server 中 for xml  path 功能的函数，将 select 结果集输出成 xml 文本格式。

【问题分析】

在达梦数据库中可以用系统包 dbms_xmlgen.getxml() 来替代 sql server 中 for xml path 功能，但 getxml() 只能替代 sql server 中 for xml path 模式的功能( path 不带参数)。

【问题解决】

以下提供三种方式供参考：

创建示例表并插入数据：

```
create table t1 (id int,c1 varchar(36),c2 varchar(20));
insert into t1 select
rownum ,dbms_random.string('U',36),dbms_random.string('U',20)
FROM DUAL CONNECT BY LEVEL <=50;
COMMIT;
```

方式一：将 select 中的结果集转成 xml 格式打印 ，转后格式与 sqlserver 的 for xml path 相同  ,可以指定列。此方法根据指定的 SQL 语句，生成内部临时上下文句柄获取 XML 数据并返回，并释放临时上下文句柄。

```
select dbms_xmlgen.getxml('select id,c2 from t1 where  id<=10 ') as xmlstr from dual;
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230810165940EJWU77HAZJZP43VF1W)

方式二：通过存储过程方式将结果集转换成 xml 格式，转换后格式与 sqlserver 的 for xml path 相同。此方法会将获取的 XML 数据放在一个临时生成的 clob 中，并返回这个临时 clob，该临时 clob 必须通过 DBMS_LOB.FREETEMPORARY 进行释放 ， 可以多次打印。

```
DECLARE
CTX DBMS_XMLGEN.CTXHANDLE; --可以查看dbms_xmlgen包定义，ctxhandle是个int类型
DTA CLOB;
MAXROWS INT;
BEGIN
CTX := DBMS_XMLGEN.NEWCONTEXT('select id,c2 from t1 where id<=10');  --申请上下文句柄
print(CTX); -- 测了一下同一个会话中，每申请一个上下文句柄，CTX值加一  
select count(*) into MAXROWS from  t1 where id<=10;

DBMS_XMLGEN.SETMAXROWS(CTX, 2);   --设置每次调用 GETXML 时的最大获取行数
DTA := DBMS_XMLGEN.GETXML(CTX);   --此方法会将获取的XML数据放在一个临时生成的clob中，并返回这个临时clob，该临时 clob 必须通过 DBMS_LOB.FREETEMPORARY 进行释放。
print(DTA);  --由于每次调用getxml最大获取两行，因此这里打印了两行
DBMS_LOB.FREETEMPORARY(DTA);  --释放临时的 LOB 操作符。

DBMS_XMLGEN.SETMAXROWS(CTX, 3);
DTA := DBMS_XMLGEN.GETXML(CTX);
print(DTA);
DBMS_LOB.FREETEMPORARY(DTA);

DBMS_XMLGEN.SETMAXROWS(CTX, 5);
DTA := DBMS_XMLGEN.GETXML(CTX);
print(DTA);
DBMS_LOB.FREETEMPORARY(DTA);
 
DBMS_XMLGEN.CLOSECONTEXT(CTX); --关闭上下文句柄并释放相关资源。
END;
/
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230810170313WT6KO0CN9GAHDBRL6A)

方式三：通过存储过程方式将结果集转换成 xml 格式，转换后格式与 sqlserver 的 for xml path 相同。根据指定的最大获取行数，将获取的数据添加在传入的 clob 的内容之后。使用该函数可以重用 clob，避免额外的 clob 复制。

```
DECLARE
CTX DBMS_XMLGEN.CTXHANDLE; --可以查看dbms_xmlgen包定义，ctxhandle是int类型
DTA CLOB;
MAXROWS INT;
BEGIN
CTX := DBMS_XMLGEN.NEWCONTEXT('select id,c2 from t1 where id<=10');  --申请上下文句柄
print(CTX); -- 测了一下同一个会话中，每申请一个上下文句柄，CTX值加一 
select count(*) into MAXROWS from  t1 where id<=10;
--DTA:='';    --DTA最开始为空串，否则会报错：-2201 无效的数据库对象 

DBMS_XMLGEN.SETMAXROWS(CTX, 0);   --设置每次调用 GETXML 时的最大获取行数为0 
DTA:=DBMS_XMLGEN.GETXML(CTX);   
print(DTA);  --由于每次调用getxml最大获取两行，因此这里打印了0行 数据的xml 格式



DBMS_XMLGEN.SETMAXROWS(CTX, 2);
DBMS_XMLGEN.GETXML(CTX,DTA); --将获取的数据添加在传入的 clob 的内容之后
print(DTA);


DBMS_XMLGEN.SETMAXROWS(CTX, 5);
DBMS_XMLGEN.GETXML(CTX,DTA); --将获取的数据添加在传入的 clob 的内容之后
print(DTA);



DBMS_XMLGEN.SETMAXROWS(CTX, 3);
DBMS_XMLGEN.GETXML(CTX,DTA); --将获取的数据添加在传入的 clob 的内容之后
print(DTA);
 
DBMS_XMLGEN.CLOSECONTEXT(CTX); --关闭上下文句柄并释放相关资源。
END;
/
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230810171228MNRXY0S52WT3A42WOH)

## 含有全文索引的表执行 SQL 查询报错：“-6803: 非法的参数数据“

【问题描述】

含有全文索引的表执行 SQL 查询报错：“-6803: 非法的参数数据“，如下 SQL：

```
--创建测试表
drop table if exists DDD.TIND1;
create table DDD.TIND1 (c1 int primary key,c2 int,c3 int ,c4 varchar(10));
create context  index "IDX_TIND1_CONTEXT_C4" on DDD.TIND1(c4 asc);  --全文索引

select
        owner     ,
        index_name,
        index_type,
        INDEX_USED_SPACE(OWNER, INDEX_NAME)
from
        dba_indexes
where
        1                                  =1
    and owner                             in('DDD')
    and index_type not                    in ('CLUSTER')
    and INDEX_USED_SPACE(OWNER, INDEX_NAME)>0 ;
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230921100956T7ZN9VVJVLM3II7BTA)

【问题解决】

该问题是由于查询全文索引的使用空间出现此报错 。

【问题解决】

可以通过添加 hint 表达式来处理该报错，具体如下：

```
select
        /*+ ENABLE_IN_VALUE_LIST_OPT(5) */
        owner     ,
        index_name,
        index_type,
        INDEX_USED_SPACE(OWNER, INDEX_NAME)
from
        dba_indexes
where
        1                                  =1
    and owner                             in('DDD')
    and index_type not                    in ('CLUSTER', 'DOMAIN')
    and INDEX_USED_SPACE(OWNER, INDEX_NAME)>0 ;
```

参数解释：

|**参数**|**默认值**|**属性**|**说明**|
|---|---|---|---|
|ENABLE_IN_VALUE_LIST_OPT|6|动态，会话级|是否允许 IN LIST 表达式优化。0：不优化。1：将 IN LIST 表达式在语义分析阶段优化为 CONST VALUE LIST；2：将 IN LIST 表达式在代价优化阶段优化为 CONST VALUE LIST；4：生成传递闭包优化；8：将 IN LIST 表达式优化为范围条件，仅限于 HUGE 表；16：OR 表达式优化为 LIST IN LIST 表达式时，允许 LIST 中的列来源于不同的表；32：允许 IN LIST 表达式中的列为分区列时转化为 SEMI JOIN；64：当 IN LIST 表达式左侧包含多列，并且包含非列类型表达式时，允许将其优化为 CONST VALUE LIST；128：分析阶段将 IN VALUE LIST 表达式转换为 OR 表达式。支持使用上述有效值的组合值，如 3 表示同时进行 1 和 2 的优化。|


## 如何通过表名查询表上对应索引定义

【问题解决】

可通过以下 SQL 查询表上对应索引定义：

```
select
        indexdef(id, 1)
from
        sysobjects A
where
        pid=
        (
                select id from sysobjects where name ='bus_car'
        )
    and subtype$ = 'INDEX'
    and name != 'INDEX'
        || id
```

## 如何通过语句统计库中的普通索引和全局索引

【问题解决】

可通过以下 SQL 统计库中的普通索引和全局索引：

```
--查看分区表的索引类型（LOCAL OR GLOBAL）
select
        aa.owner         ,
        aa.table_name    ,
        aa.index_name    ,
        bb.partition_type,
        case partitioned when 'YES' then 'LOCAL' when 'NO' then 'GLOBAL' end as index_type
from
        dba_indexes aa,
        (
                select
                        owner      ,
                        object_name,
                        partition_type
                from
                        SYSHPARTTABLEINFO,
                        dba_objects
                where
                        object_id=base_table_id
                group by
                        partition_type,
                        owner         ,
                        object_name
        )
        bb
where
        aa.owner     =bb.owner
    and aa.table_name=bb.object_name;
```

## DM 可以类似 mysql 中 json 数据能够通过虚拟列实现添加索引吗

【问题解决】

MYSQL 中 json 数据通过虚拟列添加索引：

```
-建表插数据建索引
mysql> CREATE TABLE jemp (
   ->     c JSON,
   ->     g INT GENERATED ALWAYS AS (c->"$.id"),
   ->     INDEX i (g)
   -> );
Query OK, 0 rows affected (0.03 sec)

mysql> INSERT INTO jemp (c) VALUES
   ->   ('{"id": "1", "name": "Fred"}'), ('{"id": "2", "name": "Wilma"}'),
   ->   ('{"id": "3", "name": "Barney"}'), ('{"id": "4", "name": "Betty"}');
Query OK, 4 rows affected (0.00 sec)
Records: 4  Duplicates: 0  Warnings: 0

mysql> commit;
Query OK, 0 rows affected (0.01 sec)
--查看执行计划
mysql>  EXPLAIN SELECT c->>"$.name" AS name FROM jemp WHERE g > 2\G
*************************** 1. row ***************************
          id: 1
 select_type: SIMPLE
       table: jemp
  partitions: NULL
        type: range
possible_keys: i
         key: i
     key_len: 5
         ref: NULL
        rows: 2
    filtered: 100.00
       Extra: Using where
1 row in set, 1 warning (0.00 sec)
```

DM 中 json 数据通过虚拟列添加索引：

```
--建表插数据建索引
CREATE TABLE jemp (
c varchar  CHECK (c IS JSON),
g int GENERATED ALWAYS AS (json_value(c, '$.id'))
);
INSERT INTO jemp (c) VALUES
('{"id": "1", "name": "Fred"}'), ('{"id": "2", "name": "Wilma"}'),
('{"id": "3", "name": "Barney"}'), ('{"id": "4", "name": "Betty"}');
commit;
create index idx_jemp on jemp(g);
--查看执行计划
EXPLAIN select json_value(C, '$.name'),g FROM jemp where g<2;

1   #NSET2: [1, 2, 64]
2     #PRJT2: [1, 2, 64]; exp_num(3), is_atom(FALSE)
3       #BLKUP2: [1, 2, 64]; IDX_JEMP(JEMP)
4         #SSEK2: [1, 2, 64]; scan_type(ASC), IDX_JEMP(JEMP), scan_range(null2,2)
```

## 时间范围分区表，插入数据扩展分区报错

【问题描述】

时间范围分区表，插入数据扩展分区报错：“-2903：语句块/包、存储函数中的间隔分区不支持自动扩展”。

示例如下：

创建测试用表：

```
CREATE TABLE SYSDBA."EVENT_TASK_STATUS"
(
"ETS_ID" NUMBER(19,0) NOT NULL,
"ETS_HDLTIMES" NUMBER(9,0),
"ETS_STATUS" VARCHAR2(1),
"ETS_SENDTIME" TIMESTAMP(0),
"ETS_HANDLETM" TIMESTAMP(0),
"ETS_VERSION" NUMBER(19,0) DEFAULT 0,
"ETS_SYSTIME" TIMESTAMP(6) DEFAULT (SYSDATE) --新加时间列
) STORAGE(ON "MAIN",BRANCH(32,32), CLUSTERBTR) 
PARTITION BY RANGE (ETS_SYSTIME)
INTERVAL (NUMTODSINTERVAL(1,'DAY'))
(PARTITION P1 VALUES LESS THAN ('2023-08-24')STORAGE(ON "MAIN", CLUSTERBTR)
) ENABLE ROW MOVEMENT;
COMMENT ON COLUMN SYSDBA."EVENT_TASK_STATUS"."ETS_SYSTIME" IS '当数据插入时获取系统当前时间插入';
```

创建循环插入存储过程：

```
create or replace PROCEDURE p_in_EVENT_TASK_STATUS(num int,inc integer)
as
begin
    for i in 1..num
    loop
        insert into "SYSDBA"."EVENT_TASK_STATUS"("ETS_ID",
               "ETS_HDLTIMES",
               "ETS_STATUS",
               "ETS_SENDTIME",
               "ETS_HANDLETM",
               "ETS_VERSION",
               "ETS_SYSTIME")
               VALUES(CEILING(RAND() * 1000000),
               CEILING(RAND() * 1000000),
               DBMS_RANDOM.RANDOM_STRING('U',1),
               SYSDATE()+inc,
               SYSDATE()+inc,
               CEILING(RAND() * 1000000), 
               SYSDATE+inc);
  
        if (i%10000 = 0) then
            commit;
        end if;
    end loop;
end;
```

执行存储过程插入前 1 个分区的数据：

```
call P_IN_EVENT_TASK_STATUS(10000000,1);
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202310201454131JDQS501V6J1M7ZJP3)

【问题解决】

设置参数 DEL_HP_OPT_FLAG 的值为 12，重新执行改存储过程即可成功。

```
alter system set 'DEL_HP_OPT_FLAG'=12;

call P_IN_EVENT_TASK_STATUS(10000000,1);
```

DEL_HP_OPT_FLAG 参数解释如下:

|**参数名**|**默认值**|**属性**|**说明**|
|---|---|---|---|
|DEL_HP_OPT_FLAG|0|动态，会话级|控制分区表的操作优化，0：不优化；1：打开分区表 DELETE 优化；2：控制范围分区表创建的优化处理，转换为数据流方式实现；4：允许语句块中的间隔分区表自动扩展；8：开启对 TRUNCATE 分区表的优化处理；16：完全刷新时删除老数据使用 DELETE 方式；32：开启对分区表全局索引 DDL 操作的优化处理，直接操作索引数据，无须重建全局索引支持使用上述有效值的组合值，如 7 表示同时进行 1、2、4 的优化|


## DM8 如何创建并使用空间数据类型

【问题解决】

DM8 支持空间数据类型及对应的方法，使用之前需要创建对应的系统包，使用 DMGEO 包需要执行 SP_INIT_GEO_SYS(1)，使用 DMGEO2 包需要执行 SP_INIT_GEO2_SYS(1) 。一般只需要执行一个即可，建议直接使用 SP_INIT_GEO2_SYS(1) 使用 DMGEO2 系统包，相比于 DMGEO 系统包 DMGEO2 系统包增加了新的空间数据类型及几何体计算函数。相关包具体使用方法可以参考数据库安装目录下 doc 目录中《DM8 系统包使用手册》。

## 在达梦数据库中如何创建对象数组类型的 TYPE

【问题解决】

  1. 创建对象的类型;



```
CREATE OR REPLACE TYPE OBJ as OBJECT(
ID int,
C1 varchar2(12),
C2 varchar2(1000));
```

  2. 创建对象数组。



```
create TYPE MEMBER_LIST AS ARRAY  OBJ[1000]  ;
```

在创建对象数组时需要注意使用方括号。

## 主键为自增列，插入 0 后无法通过主键查询到数据

【问题描述】

主键列为 int 类型自增列，插入 0 无法查询到结果，插入其他值可以正常查询到结果。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231207140937Y9X34DJ3EMM0LG2UWK)

【问题分析】

由于主键添加了自增属性导致，在包含自增属性列上 0 是一个特殊值，传入 0 代表自动为其分配下一个自增 ID。

DM 提供两种自增列方式：IDENTITY 自增列和 AUTO_INCREMENT 自增，两种自增配置方式。当采用 AUTO_INCREMENT 自增列，插入 0 时，是否自动插入自增的下一个值受到参数 NO_AUTO_VALUE_ON_ZERO 控制，当参数为 1 时，主键插入 0 会自动插入自增的下一个值。当采用 IDENTITY 自增时，插入 0 则会报错。

【问题解决】

方法一：取消自增属性。

方法二：修改 NO_AUTO_VALUE_ON_ZERO 参数值为 0。

方法三：传参不要使用 0 作为主键 ID。

参数说明：

|**参数**|**缺省值**|**属性**|**说明**|
|---|---|---|---|
|NO_AUTO_VALUE_ON_ZERO|1|动态，会话级|用于 AUTO_INCREMENT 自增列中。表示 AUTO_INCREMENT 列插入 0 时，是否自动插入自增的下一个值。取值范围 0、1。0 否，插入 0；1 是，插入自增值。|


示例说明如下：

  1. 创建 2 张测试表采用不同的自增方式。



```
CREATE TABLE test231201 (
    id INT AUTO_INCREMENT PRIMARY KEY,---AUTO_INCREMENT方式自增
    name VARCHAR(50) NOT NULL
);

CREATE TABLE test231202
(
id INT IDENTITY(1, 100) PRIMARY KEY,---IDENTITY方式自增
name VARCHAR(50) NOT NULL
);
```

  2. AUTO_INCREMENT 自增方式的表主键插入 0 后自增为 1。



```
--主键写入 0 会自增为 1
insert into test231201 values(0,'aaa');
select * from test231201;
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202312071428114A8IJJK2JC173PIJNW)

  3. 修改 NO_AUTO_VALUE_ON_ZERO 参数值为 0 后，主键可以插入 0。



```
--修改参数值
sp_set_para_value(1,'NO_AUTO_VALUE_ON_ZERO',0)
--插入数据
insert into test231201 values(0,'bbb');
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231207143122TXIR3DWOQIGAU21R6L)

  4. IDENTITY 自增方式主键插入 0 报错。



```
insert into test231202 values(0,'aaa');
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231207143443ROW1CS51RZOW44119F)

## 如何像 MySQL 一样查询所有表的表数据行数？如何统计表占用空间大小？

【问题解决】

达梦提供 TABLE_ROWCOUNT 函数用来统计表行数，提供 TABLE_USED_PAGES 函数来统计表使用的页数，因此可参考使用以下 SQL 来查询：

```
  SELECT B.OWNER, 
         B.TABLE_NAME, 
         ROUND(TABLE_USED_PAGES(B.OWNER,B.TABLE_NAME)*(PAGE/1024)/1024/1024,2) "GB", 
         TABLE_ROWCOUNT(B.OWNER,B.TABLE_NAME) "TABLE_ROWS" 
    FROM (SELECT A.OWNER, 
                 A.TABLE_NAME 
            FROM ALL_TABLES A 
           WHERE A.OWNER IN ('USER1', 
                             'USER2') --可指定要统计的模式名
             AND A.TABLE_NAME NOT LIKE 'CTI%' 
             AND A.TABLE_NAME NOT LIKE 'SREF_CON_TAB%' 
             AND A.TABLE_NAME NOT LIKE 'BM%' ) B 
ORDER BY 3 DESC, 
         1, 
         2;
```

## 使用 for update 语法报错：此查询表达式不允许 FOR UPDATE

【问题描述】

派生表子查询或视图嵌套使用 for update 语法报错：“此查询表达式不允许 FOR UPDATE”。示例如下：

  1. 创建测试表



```
CREATE TABLE "EMP"("EMPNO" NUMBER,"ENAME" VARCHAR2(10),"JOB" VARCHAR2(9),"MGR" NUMBER,"HIREDATE" DATETIME(0),"SAL" NUMBER(7,2),"COMM" NUMBER(7,2),"DEPTNO" NUMBER(2,0),"STATUS" VARCHAR2(5));
CREATE TABLE "DEPT" ("DEPTNO" NUMBER,"DNAME" VARCHAR2(14),"LOC" VARCHAR2(13));
```

  2. 执行 SQL，出现报错：-4596: 此查询表达式不允许 FOR UPDATE。



```
select * from
      (select e.ename as ename,
              e.sal   as sal  ,
              d.dname as dname
       from emp e
       left join dept d
       on e.deptno=d.deptno
      ) t
where sal>1000 for update;
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231214163035KPF787ENLXUMBNEO7A)

【问题解决】

调整参数 VIEW_PULLUP_FLAG=2 或在 SQL 中使用 hint 表达式设置参数 VIEW_PULLUP_FLAG=2 。

```
select /*+ VIEW_PULLUP_FLAG(2) */
       * from
      (select e.ename as ename,
              e.sal   as sal  ,
              d.dname as dname
       from emp e
       left join dept d
       on e.deptno=d.deptno
      ) t
where sal>1000 for update;
```

参数说明：

|**参数名**|**属性**|**默认值**|**说明**|
|---|---|---|---|
|VIEW_PULLUP_FLAG|动态，会话级|34|是否对视图进行上拉优化，把视图转换为其原始定义，消除视图。0：不进行视图上拉优化；1：对不包含别名和同名列的视图进行上拉优化；2：对包含别名和同名列的视图也进行上拉优化；4：强制允许带变量的查询语句进行视图上拉优化，有可能造成结果集错误；8：不对 LEFT JOIN 的右孩子\RIGHT JOIN 的左孩子\FULL JOIN 左右孩子进行上拉；16：不对 LEFT JOIN 的左孩子进行上拉；32：视图上拉后，从视图符号表删除不被引用的列支持使用上述有效值的组合值，如 3 表示同时进行 1 和 2 的优化|


## 如何去除 char 类型数据后的空格

【问题描述】

应用开发时移植代码未对 char 类型后的空格进行处理，导致数据无法匹配，使用 alter table modify 将 char 类型修改为 varchar 类型后空格依然存在。

【问题解决】

在将 char 修改为 varchar 类型后使用 rtrim 函数将空格去除。示例如下:

  1. 新建测试表，一列为 char 类型，一列为 varchar 类型。



```
create table test_char(char_ char(2),varchar_ varchar(2));
--插入一个字符
insert into test_char values('a','a');
commit;
```

查询结果可以发现 char_  列末尾存在空格，此时无法匹配数据。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202312261011159191ARGL3VGN5WJU63)

  2. 修改列类型，将 char_ 列修改为 varchar 类型。



```
alter table test_char modify char_ varchar(2);
```

将 char_ 列修改为 varchar 类型后空格依旧存在。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202312261013241HYLSYQYEJLPFT01Q0)

  3. 使用 rtrim 函数去除尾部空格。



```
update test_char set char_=rtrim(char_);
commit;
```

此时空格消除。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231226101501NG30LIABE2U476CTYZ)

## 如何在达梦中实现 Oracle 存储过程里输出 N**M（N 的 M 次幂）的计算方法

【问题解决】

Oracle 实现 N 的 M 次幂写法：

```
declare
n number := 5;
m number := 2;
begin
dbms_output.enable();
dbms_output.put_line(n**m);
end;
/
--执行结果：25
```

达梦实现方法如下：

  1. 创建自定义函数。



```
CREATE OR REPLACE FUNCTION FUNC_OP_TEST(
C1 IN NUMBER,
C2 IN NUMBER
) RETURN NUMBER
AS
BEGIN
RETURN CAST(POWER(C1,C2) AS NUMBER);
END;
/
```

  2. 创建自定义运算符**，即可在不修改 Oracle 代码的情况下，实现 N 的 M 次幂计算。



```
CREATE OPERATOR ** (FUNCTION FUNC_OP_TEST, LEFTARG NUMBER, RIGHTARG NUMBER);
```

  3. 执行结果如下所示。



```
declare
n number := 5;
m number:= 2;
begin
dbms_output.enable () ;
dbms_output.put_line (n**m) ;
end;
执行成功， 执行耗时0毫秒。 执行号:632
25
```

## 使用 dblink 批量插入远程表时报错：“超出保存点上限”

【问题描述】

通过 DPI 方式创建的 DBLINK，在使用 dblink 批量插入远程表时报错：“超出保存点上限”。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240119105848AQR5PNZOL0NMCS48RX)

【问题分析】

DPI 方式创建 dblink 插入远程表批量提交受到参数 SAVEPOINT_LIMIT 限制，最高 10000。

SAVEPOINT_LIMIT 参数含义：单个事务中最大可创建的保存点个数。取值范围 0~10000，默认值 512。

【问题解决】

一下提供两种解决方法：

  1. 降低同一事务内的批量提交语句数量。
  2. 使用 odbc 方式创建 DBLINK。



示例如下：

  1. DPI 方式创建 dblink。



```
create or replace link "SYSDBA"."LINK_DPI" connect 'DPI' with "SYSDBA" identified by " SYSDBA" using '10.15.1.25:5236';
```

降低批量提交语句，由 555 行减少到 512 行，批量提交成功。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202401191100207R6W46O7IMNKHC12YK)

  2. ODBC 方式创建 DBLINK。



```
create or replace link "SYSDBA"."LINK_ODBC" connect 'ODBC' with "SYSDBA" identified by "SYSDBA" using 'dm8';
```

批量提交不减少，批量提交成功。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240119110639NUKZJJS7JR3IGVKKVG)

## 如何给表添加虚拟列

【问题解决】

虚拟列，从本质上来说，它是在表的定义中为派生列生成了元数据。可以在生成表时直接生成虚拟列，也可以在需要时再添加。虚拟列的定义不占用表的空间，当对表进行访问时，虚拟列的结果是临时计算出来的。示例如下:

  1. 创建表。



```
CREATE TABLE "SYSDBA"."ACCOUNTS"
(
"ID" INTEGER,
"NAME" VARCHAR2(100),
"PRICE" INT,
"QUAN" INT ) STORAGE(ON "MAIN", CLUSTERBTR) ;
```

  2. 添加虚拟列。



```
alter table ACCOUNTS add line_total as   (TO_CHAR(PRICE*QUAN));
```

  3. 查询虚拟列。



查询数据字典。

```
select DATA_DEFAULT,* from SYS.DBA_TAB_COLUMNS where TABLE_NAME like 'ACCOUNTS'
```

查看的表定义（VIRTUAL 关键字）如下所示：

```
CREATE TABLE "SYSDBA"."ACCOUNTS"
(
"ID" INTEGER,
"NAME" VARCHAR2(100),
"PRICE" INT,
"QUAN" INT,
"LINE_TOTAL" VARCHAR2(500) GENERATED ALWAYS AS (TO_CHAR((PRICE * QUAN))) VIRTUAL ) STORAGE(ON "MAIN", CLUSTERBTR) ;
```

## 删除 varray 集合指定下标元素报错“-7088 非法的对象方法序号”

【问题描述】

删除 varray 集合指定下标元素报错“-7088 非法的对象方法序号”。如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240221093340SSMXZJY2NCFWJQTF6U)

【问题分析】

DMSQL 程序支持三种集合类型：VARRAY 类型、索引表类型和嵌套表类型。集合支持 COUNT、LIMIT、FIRST、LAST、NEXT、PRIOR、EXISTS、DELETE、TRIM 和 EXTEND 等方法，但存在一些限制。限制如下：

  1. VARRAY 类型不支持指定索引下标 DELETE 方法；
  2. 索引表类型不支持 EXTEND 方法；
  3. 索引表类型不支持 TRIM 方法。



【问题解决】

使用索引表类型取代 varray，示例如下：

```
DECLARE
  TYPE IntList IS TABLE OF NUMBER INDEX BY INT;
  v IntList;
  i INT;
BEGIN
  print '# empty, v.count=' || v.COUNT;
  for i in 1 .. 5
  LOOP
     print i||': '||i*2-1;
     v(i) := i*2-1;
  end loop;
  print v.first;
  print '# added 5 numbers, v.count=' || v.COUNT;
  v(v.last+1)=999.99;
  v.delete(v.first);
  print '# added 1 more and delete the first, v.count=' || v.COUNT;
  print '# all elements: ';
  for i in v.first .. v.last
   loop
    print i || ': ' ||v(i);
  end loop;
END;
```

执行结果正常，实现了指定下标元素的删除，截图如下：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202402210934181EHYC6GQQLHD2YQHOX)

## 如何使用 extract 函数将两个 timestamp 字段相减

【问题描述】

使用 extract 函数将两个 timestamp 字段相减报错：“-6118: 非法的时间日期类型数据”。示例如下：

创建测试表：

```
CREATE TABLE TEST3
             ( LOGTIME1 TIMESTAMP(6), LOGTIME2 TIMESTAMP(6)
             );
 
insert into TEST3 values (to_timestamp('2024-01-08 23:37:08','yyyy-mm-dd hh24:mi:ss'), 
       to_timestamp('2024-03-07 07:40:48','yyyy-mm-dd hh24:mi:ss'));
 
commit;
```

执行如下 SQL 查询报错：

```
select to_char(extract(day from (logtime2 - logtime1))) from test3;
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240321093333UCDW1CYSLUBKMHUJEA)

那么要如何才能使用 extract 函数将两个 timestamp 字段相减。

【问题分析】

DM 的时间相减默认是 DOUBLE 数据类型，可以通过开启兼容 ORACLE 的时间来返回间隔类型。

【问题解决】

可以通过设置参数 COMPATIBLE_MODE=2 和 ORA_DATE_FMT=1 兼容 ORACLE，使用 extract 函数将两个 timestamp 字段相减。具体步骤如下：

  1. 在 dm.ini 中修改参数 COMPATIBLE_MODE=2 和 ORA_DATE_FMT=1。
  2. 重启数据库。
  3. 执行 SQL 查询验证。



```
select to_char(extract(day from (logtime2 - logtime1))) from test3;
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202403210947099RPC28V3UF5Z3BT4KE)

## 执行 sql 报错“ -7141: 方法所属对象未初始化”

【问题描述】

select 语句中，引用自定义对象类型的方法时，即执行如下 sql：

```
--其中 hr_person_test.personname 为表名.列名，personname 是自定义对象类型，类体中有 tostring 函数
select personname,hr_person_test.personname.tostring() from hr_person_test;   
```

报错“ -7141: 方法所属对象未初始化”。

【问题分析】

在 DM 中自定义对象类型的使用方法和类类型的使用方法一样，DMSQL 程序中可以声明一个类类型的变量，初始化该变量后就可以访问类的成员变量，调用类的成员方法了。 因此，personname 列为 null 的话，就被认为没有初始化。

【问题解决】

  1. 可以通过在 select 语句中加上 where is not null 即可执行成功。
  2. 也可以根据业务特点，考虑把 personname 设置成非空列。



示例如下：

  1. 创建自定义对象类型。



```
create or replace TYPE ENCRYPT_STR AS OBJECT
(
    REAL_NUM VARCHAR2(512),
    MEMBER FUNCTION toString
    return VARCHAR2,
    MAP MEMBER FUNCTION strToNumber
    RETURN NUMBER );
  
create or replace TYPE BODY ENCRYPT_STR
IS
    MEMBER FUNCTION tostring
    RETURN VARCHAR2
IS
BEGIN
    RETURN REAL_NUM;
END;
MAP MEMBER FUNCTION strToNumber
    RETURN NUMBER
IS
    RETVAL NUMBER;
BEGIN
    SELECT REAL_NUM||'123456654321' INTO RETVAL FROM DUAL;
  
    RETURN RETVAL;
END;
END;
```

  2. 创建测试表。



```
drop table if exists HR_PERSON_TEST;
 
CREATE TABLE HR_PERSON_TEST 
             (PERSONNAME ENCRYPT_STR,GONGHAO VARCHAR(128) 
             );
 
INSERT INTO HR_PERSON_TEST (PERSONNAME, 
       GONGHAO) 
       VALUES(ENCRYPT_STR('MCICBAEpaLkCCBf125SCHiACBBDkIlY9icepIZz8p6+qIXYO'), 
       '123456');
 
insert into HR_PERSON_TEST values (null,'999');
 
insert into HR_PERSON_TEST values ( encrypt_str(''),'888');
 
insert into HR_PERSON_TEST values ( encrypt_str(null),'666');
 
commit;
```

  3. 执行如下语句将报错。



```
select personname,hr_person_test.personname.tostring() from hr_person_test;
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240321100432BLJMTVKE8F32UTIK5Q)

本例中，personname 列为空值，就相当于是没有初始化，因此报错"-7141: 方法所属对象未初始化"。

  4. 加上 "where personname is not null"后，正常执行。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240321100503AS1F0XUPU6JA8K961M)

## 使用 decode 函数报错：“-6111: 字符串转换出错”

【问题描述】

执行以下 SQL 命令时报错：“-6111: 字符串转换出错”。

```
select DECODE(0,0,0,to_char(TO_DATE('20240408','YYYYMMDD'))) from dual; 
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240411144219B3WCSU9Z42Q2PIOMC8)

【问题分析】

decode 函数，经过分析时 then..else 表达式目标类型为 int，由于 CASE_WHEN_CVT_IFUN 取值包含 1，内部转换为函数时，执行阶段首先计算参数值，因此转 int 时报错。

参数解释：

|**参数名**|**默认值**|**属性**|**说明**|
|---|---|---|---|
|CASE_WHEN_CVT_IFUN|9|动态，会话级|对 CASE WHEN 查询表达式的优化处理。0：不优化；1：将 CASE WHEN 查询表达式转换为 IFOPERATOR 函数；2：将 CASE WHEN 查询表达式转换为 IFOPERATOR 函数，且有限制地进行表达式重用；4：CASE WHEN 查询表达式在运算符中转换为 OR 进行处理；8：对于 CASE WHEN 查询表达式不允许 THEN…ELSE 表达式重用；16：将参数个数少于 30 的 COALESCE 函数转为 CASE WHEN 表达式，提升执行性能；32：CASE WHEN 的条件表达式中有恒真或恒假的条件时进行简化；64：将 CASE VALUE1 WHEN VALUE2 转为 CASE WHEN VALUE1=VALUE2，可配合取值 1 时转为函数进行批量处理，提升性能支持使用上述有效值的组合值，如 5 表示同时进行 1 和 4 的优化|


【问题解决】

使 CASE_WHEN_CVT_IFUN 取值不包含 1 即可。例如：

```
select  /*+CASE_WHEN_CVT_IFUN(0)*/  DECODE(0,0,0,to_char(TO_DATE('20240408','YYYYMMDD'))) from dual;
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202404111449302NF8KSWSJRW35DTZGH)

## 使用 SQL 语句进行列排序时汉字未按拼音首字母进行排序

【问题描述】

使用 SQL 语句对汉字进行列排序时，汉字未按拼音首字母进行排序。

【问题解决】

可以通过修改排序方式来实现按汉字拼音首字母进行排序，以下提供 3 种解决方法，示例如下：

示例表：

```
create table test1 (id int,name VARCHAR(10));

INSERT into TEST1 VALUES (1,'张无忌');
INSERT into TEST1 VALUES (2,'郭靖');
INSERT into TEST1 VALUES (3,'杨过');
INSERT into TEST1 VALUES (4,'赵敏');
INSERT into TEST1 VALUES (5,'黄蓉');
INSERT into TEST1 VALUES (6,'小龙女');

select * from TEST1;
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2024052409430423B61HTT0H8852DEIG)

```
select * from TEST1 order by name;
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240524100841KIFV0095YLSEGC7LCL)

方法一：使用 NLSSORT() 函数。

修改 SQL 查询语句使用 NLSSORT() 函数实现按汉字拼音首字母进行排序。

```
SELECT * FROM TEST1 ORDER BY NLSSORT(name,'NLS_SORT = SCHINESE_PINYIN_M'); 
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240524094345BMK1XIR35C9POX2ME9)

NLSSORT() 函数说明可参考《DM8_SQL 语言使用手册》。

方法二：通过设置当前会话的自然语言排序方式为按照中文拼音排序来实现。此种方式只对当前会话生效。

```
alter session set nls_sort=schinese_pinyin_m;

select * from TEST1 order by name;
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240524095339E9OLZJ04QVGCHBM0CC)

方法三：通过设置全局参数 NLS_SORT_TYPE 指定自然语言的排序方式为按中文拼音排序。此种方式对执行完语句后的新会话有效。

```
SP_SET_PARA_VALUE(1,'NLS_SORT_TYPE',1);

select * from TEST1 order by name;
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202405240957038GJX7YJLZZRXNLZZKO)

参数 NLS_SORT_TYPE 说明：

|**参数**|**默认值**|**属性**|**说明**|
|---|---|---|---|
|NLS_SORT_TYPE|0|动态，会话级|指定自然语言的排序方式。0：按默认字符集二进制编码排序；1：按中文拼音排序；2：按中文笔画排序；3：按中文部首排序；4：按泰文排序；5：按韩文排序。仅字符集为 UTF-8 的数据库支持自然语言按泰文排序|


## 间隔分区表使用方法

【问题解决】

创建表：

```
CREATE TABLE interval_day_table01
            (
                         employee_id   NUMBER,
                         employee_name VARCHAR2(20),
                         birthday      DATETIME
            )
PARTITION BY RANGE (birthday )
            INTERVAL
            (
             NUMTODSINTERVAL(1,'DAY')
            )
(PARTITION p1 VALUES LESS THAN(to_date('2024-01-25 00:00:00','yyyy-mm-dd hh24:mi:ss'))
);
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240621151840MPL653N5ZTMBIQ1QBM)

插入测试数据:

```
insert into  interval_day_table01 values(1,'aa','2024-01-25 10:00:00');
insert into  interval_day_table01 values(1,'aa','2024-01-25 20:00:00');
insert into  interval_day_table01 values(1,'aa','2024-01-26 10:00:00');
insert into  interval_day_table01 values(1,'aa','2024-01-27 10:00:00');
insert into  interval_day_table01 values(1,'aa','2024-01-25 10:00:00');
insert into  interval_day_table01 values(1,'aa','2024-01-30 10:00:00');
insert into  interval_day_table01 values(1,'aa','2024-01-29 00:00:00');
insert into  interval_day_table01 values(1,'aa','2024-01-28 00:00:00');
insert into  interval_day_table01 values(1,'aa','2024-01-25 00:00:00');
insert into  interval_day_table01 values(1,'aa','2024-01-25 00:00:00');
insert into  interval_day_table01 values(1,'aa','2024-01-26 00:00:00');
insert into  interval_day_table01 values(1,'aa','2024-01-27 00:00:00');
commit;
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240621151937P9529BD6II1J1TMA0D)

查询分区，初始分区 P1 不能被删除：

```
select TABLE_OWNER,table_name,partition_name,HIGH_VALUE
from dba_tab_partitions
where table_name=upper('interval_day_table01') and PARTITION_NAME<>'P1';
```

根据 PARTITION_NAME 查询结果来删除对应日期的分区：

```
alter table interval_day_table01 drop PARTITION SYS_P1337_1341;
```

## to_date('12-DEC-2024','DD-MON-YYYY') 执行报错

【问题描述】

执行如下两条 SQL，预期全部执行成功，实际第一条执行报错：“-6118: 非法的时间日期类型数据”。

```
select to_date('12-DEC-2024','DD-MON-YYYY'); --执行报错
select to_date('12-12月-2024','DD-MON-YYYY'); -- 执行成功
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240704155647V1TMJAGTY7PL04JPUB)

【问题分析】

TO_DATE() 函数语法：TO_DATE (char [,fmt[,'nls']])。其中 NLS：指定日期时间串的语言类型，取值：AMERICAN、ENGLISH 或 SIMPLIFIED CHINESE，分别表示美式英语、英语和简体中文，其中 AMERICAN 和 ENGLISH 的效果相同，缺省为 SIMPLIFIED CHINESE。`select to_date('12-DEC-2024','DD-MON-YYYY')` 中日期时间串“12-DEC-2024”为英文表达式需要指定 NLS 为"AMERICAN"或“ENGLISH”。

【问题解决】

修改 SQL 指定 TO_DATE() 函数中的 NLS 为对应的日期时间串的语言类型。

```
select to_date('12-DEC-2024','DD-MON-YYYY','NLS_DATE_LANGUAGE=''AMERICAN''');
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240704160839P9IGQ9JCASXSTJJXHP)

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240704160903WJ61AMQ2Y3X5HC9UPD)

## power 函数计算大数值时有误差

【问题描述】

power 函数计算出大数值时有误差，如 2 的 60 次方，准确值为 1152921504606846976，查询 select power(2,60) 值为 1.15292150460684698E18 存在误差。

【问题分析】

达梦中 DOUBLE 类型为标准 C 语言中 DOUBLE，标准 C 语言中 DOUBLE 类型提供大约 15 至 16 位的有效十进制数字。意味着小数点后大约 15 至 16 位数字的精度可以保持准确，但超出这个范围的数字可能会有舍入误差。

【问题解决】

要获取准确的数值，可以使用 power2 函数。因为 POWER() 的返回值类型为 DOUBLE，POWER2() 的返回值类型为 DECIMAL 精度更大。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240718095538776CHDRZZCPKX854DU)

详细数值数据类型说明可参考《DM8_SQL 语言使用手册》。

## -6105:数据类型不匹配

【问题描述】

SQL 中对 CLOB 字段进行比较报错：“-6105:数据类型不匹配”。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202408221449006IKKH6YQLO6U52P5AE)

【问题解决】

方法一：修改参数 COMPATIBLE_MODE=2，兼容 Oracle 后可按字符型处理。

方法二：可以通过 dbms_lob 包中的方法对 CLOB 数据进行处理。

以下为方法二的示例：

  1. 创建测试表、插入数据。



```
create table lob1(id number,name varchar2(10),content clob);
create table lob2(id number,name varchar2(10),content clob);
insert into lob1 values (1,'aa','asd');
insert into lob1 values (2,'bb','qwe');
insert into lob2 values (1,'AA','asd');
insert into lob2 values (2,'BB','zxc');
```

  2. 执行查询报错。



```
select a.name,b.name
from lob1 a,lob2 b
where a.content = b.content;
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240822145343CWAQ82377BWG2N7HM2)

  3. 可通过以下两种方法进行查询。



使用 dbms_lob.compare 方法比较两个 LOB 数据并返回结果。

```
select a.name, 
       b.name 
  from lob1 a, 
       lob2 b 
 where dbms_lob.compare(a.content,b.content)=0;
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240822145430DTZR7AC18I6WG7QKKA)

使用 dbms_lob.substr 方法。

```
select a.name, 
       b.name 
  from lob1 a, 
       lob2 b 
 where dbms_lob.substr(a.content) = dbms_lob.substr(b.content);
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202408221458559GH8F8TB8FX2V80WLH)

dbms_lob 包详细使用说明可参考《DM8 系统包使用手册》。

## 如何通过 SQL 快速获取表、注释、索引定义

【问题解决】

可用通过创建如下方法获取表、注释、索引定义：

  1. 创建方法：



```
CREATE or REPLACE FUNCTION FN_GET_TABLE_DDL( DM_table_name   varchar2(200)) RETURN  VARCHAR2(10000) AS
DM_SCH_name  VARCHAR(200);
DM_TAB_name  VARCHAR(200);
DM_SQL  VARCHAR2(10000);
BEGIN
DM_table_name:=replace(DM_table_name,'"','');
if(instr(DM_table_name,'.')<2) then
RETURN '输入表名，需带模式名，如sysdba.Table001';--此处无需修改
end if;
SELECT
   REGEXP_SUBSTR(DM_table_name, '[^.]+', 1, 1) AS SCH,
   REGEXP_SUBSTR(DM_table_name, '[^.]+', 1, 2) AS TABLENAME into  DM_SCH_name, DM_TAB_name   from dual;

--查询结构返回
select LISTAGG(CC,chr(13)) as TSQL into DM_SQL from (
select TABLEDEF(DM_SCH_name, DM_TAB_name ) as CC union all
select 'COMMENT ON TABLE '||SCHNAME||'.'||TVNAME||' IS '''||COMMENT$||''';' as CC  from SYSTABLECOMMENTS
where TABLE_TYPE='TABLE'
and SCHNAME=DM_SCH_name and TVNAME=DM_TAB_name  union all
select 'COMMENT ON COLUMN '||SCHNAME||'.'||TVNAME||'.'||COLNAME||' IS '''||COMMENT$||''';' as CC  from SYSCOLUMNCOMMENTS
where TABLE_TYPE='TABLE'
and SCHNAME=DM_SCH_name and TVNAME=DM_TAB_name  union all
select indexdef(idx_obj.id, 1) as CC from sysobjects idx_obj, sysindexes idx where idx_obj.id = idx.id
and idx_obj.pid =
(SELECT id from sysobjects where name=DM_TAB_name  
and schid in(SELECT id from sysobjects where name=DM_SCH_name  
and type$='SCH') and subtype$='UTAB')
and idx_obj.subtype$ = 'INDEX' and idx.flag & 0x01 = 0);
return DM_SQL;
END;
```

  2. 通过该方法查询表结构：



```
select FN_GET_TABLE_DDL('SYSDBA.LOB1');--查询 SYSDBA 用户下 LOB1 表，查询的表需要带模式名。
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240906111729TCQPDP3ZLPKBW3FQ2V)

## DBLINK 查询报错：“DBLINK 大字段缓存空间不足”

【问题描述】

使用 DBLINK 查询远端大字段类型数据时报错：DBLINK 大字段缓存空间不足。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240926160206DW5B7ECK1F7FL2JLP4)

【问题分析】

使用 DBLINK 访问大字段类型时，大字段的长度存在限制。

【问题解决】

  1. 通过如下语句查询当前版本支持的最大长度。



```
select * from v$dm_ini where para_name='DBLINK_LOB_LEN';
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240926160455FRXDQ4IRXO6ZJR1GM9)

  2. 可以看到当前版本支持大字段长度最大为 2G，可以根据需要，执行如下语句，将此参数调大。该参数为动态参数，执行后即可生效，不需要重启数据库。



```
sp_set_para_value(1,'DBLINK_LOB_LEN',2097152);
```

## 存储过程中通过 scope_identity 获取不到自增列的最大值

【问题描述】

示例如下：

```
--创建自增表
create table cs2(id int identity(1,1),name varchar2(10));
--创建存储过程获取自增最大值
create or replace procedure cs
authid definer
as
begin
insert into cs2 select '1';
select scope_identity;
end;
--调用时显示为NULL
call cs();
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20241107160938CHX2DWUONZH7TWRVYO)

【问题解决】

将 scope_identity 调整为 global_identity，返回在当前会话中的任何表内所生成的最后一个标识值，不受限于特定的作用域。一个作用域就是一个模块：存储过程、触发器、函数或批处理，若两个语句处于同一个存储过程、函数或批处理中，则它们位于相同的作用域中。

```
--修改存储过程
create or replace procedure cs
authid definer
as
begin
insert into cs2 select '1';
select global_identity;
end;
--调用过程
call cs();
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20241107161103HRSQWP1EI0LO8VM8UL)

## 修改表列精度报错：“Cannot modify col with context index”

【问题描述】

修改表列精度报错：

```
alter table context_tab MODIFY name varchar2(1000);
执行失败(语句 1)
-2867: Error in line: 1
Cannot modify col with context index
```

【问题分析】

该报错是因为修改表中对应列存在全文索引导致。

【问题解决】

可以通过先保存全文索引创建语句，再进行删除，再做对应的表列修改，当修改完成后再重新创建。

## 如何批量生成所有非系统用户的所属角色对象权限的授权语句

【问题描述】

如何批量生成所有非系统用户的所属角色对象权限的授权语句？

【问题解决】

使用如下命令则可以自动拼接 grant 命令，实现批量生成所有非系统用户的所有对象权限的授权语句，省时省力。

```
SELECT 'GRANT '||A.AA||' ON '||A.BB||' TO '||A.CC||';' AS GRANTS  FROM (
select SF_GET_OBJ_FULL_NAME(OBJID, COLID)AS BB, SF_GET_SYS_PRIV(PRIVID)AS AA, 
(select name from SYSOBJECTS where ID = GRANTS.GRANTOR) GRANTOR_NAME, GRANTABLE, 
(select TYPE$ from SYSOBJECTS where ID = GRANTS.OBJID) TYPE, 
(select DECODE(SUBTYPE$,'PROC',DECODE(INFO1 & 0X01,0,'FUNCTION',1,'PROCEDURE'),SUBTYPE$) 
from SYSOBJECTS where ID = GRANTS.OBJID) SUB_TYPE,USERS.USERNAME AS CC from SYSGRANTS GRANTS LEFT JOIN DBA_USERS USERS ON  GRANTS.URID=USERS.USER_ID 
where (OBJID != -1 OR COLID != -1) and PRIVID != -1 and USERS.USERNAME IN (select USERNAME from SYS.ALL_USERS WHERE USERNAME not LIKE 'SYS%')) A
```

同时搭配以下命令可以批量获取非系统用户的角色授权语句。

```
select 'GRANT "'||A.OBJNAME||'" TO "'||A.USERNAME||'";' from (
select (select name from SYSOBJECTS where ID = GRANTS.OBJID) OBJNAME,
 (select name from SYSOBJECTS where ID = GRANTS.GRANTOR) GRANTOR_NAME,
  GRANTABLE,USERS.USERNAME from SYSGRANTS GRANTS LEFT JOIN DBA_USERS USERS ON  GRANTS.URID=USERS.USER_ID 
  where PRIVID = -1 and USERS.USERNAME IN (select USERNAME from SYS.ALL_USERS WHERE USERNAME not LIKE 'SYS%'))A
```
