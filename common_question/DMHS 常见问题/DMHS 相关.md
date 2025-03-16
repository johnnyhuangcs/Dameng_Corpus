

# DMHS 相关

本章节主要介绍 DMHS 常见问题，为用户提供 DMHS 常见问题的分析和解决思路。除此之外，用户还可前往 [达梦技术社区](https://eco.dameng.com/community/question/)参与更多问题讨论。

## 目录

  * [Dmhs 出现目的端同步 update 或 delete 操作出现报错：exec[ERROR] sync failed,fill row 1 该如何处理？](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#Dmhs%20%E5%87%BA%E7%8E%B0%E7%9B%AE%E7%9A%84%E7%AB%AF%E5%90%8C%E6%AD%A5%20update%20%E6%88%96%20delete%20%E6%93%8D%E4%BD%9C%E5%87%BA%E7%8E%B0%E6%8A%A5%E9%94%99%EF%BC%9Aexec\[ERROR\]%20sync%20failed,fill%20row%201%20%E8%AF%A5%E5%A6%82%E4%BD%95%E5%A4%84%E7%90%86%EF%BC%9F)
  * [在进行单表数据对比时，dmhs_veri 对比工具如何限定数据的对比范围（即添加 where 条件）？](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#%E5%9C%A8%E8%BF%9B%E8%A1%8C%E5%8D%95%E8%A1%A8%E6%95%B0%E6%8D%AE%E5%AF%B9%E6%AF%94%E6%97%B6%EF%BC%8Cdmhs_veri%20%E5%AF%B9%E6%AF%94%E5%B7%A5%E5%85%B7%E5%A6%82%E4%BD%95%E9%99%90%E5%AE%9A%E6%95%B0%E6%8D%AE%E7%9A%84%E5%AF%B9%E6%AF%94%E8%8C%83%E5%9B%B4%EF%BC%88%E5%8D%B3%E6%B7%BB%E5%8A%A0%20where%20%E6%9D%A1%E4%BB%B6%EF%BC%89%EF%BC%9F)
  * [在进行模式名.*对比时，dmhs_veri 数据对比工具如何通过使用黑名单功能禁用某些表进行数据对比？](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#%E5%9C%A8%E8%BF%9B%E8%A1%8C%E6%A8%A1%E5%BC%8F%E5%90%8D.*%E5%AF%B9%E6%AF%94%E6%97%B6%EF%BC%8Cdmhs_veri%20%E6%95%B0%E6%8D%AE%E5%AF%B9%E6%AF%94%E5%B7%A5%E5%85%B7%E5%A6%82%E4%BD%95%E9%80%9A%E8%BF%87%E4%BD%BF%E7%94%A8%E9%BB%91%E5%90%8D%E5%8D%95%E5%8A%9F%E8%83%BD%E7%A6%81%E7%94%A8%E6%9F%90%E4%BA%9B%E8%A1%A8%E8%BF%9B%E8%A1%8C%E6%95%B0%E6%8D%AE%E5%AF%B9%E6%AF%94%EF%BC%9F)
  * [部署 mysql8.0 到 dm8 的 DMHS 数据同步时报错：initial log analysis module failure](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#%E9%83%A8%E7%BD%B2%20mysql8.0%20%E5%88%B0%20dm8%20%E7%9A%84%20DMHS%20%E6%95%B0%E6%8D%AE%E5%90%8C%E6%AD%A5%E6%97%B6%E6%8A%A5%E9%94%99%EF%BC%9Ainitial%20log%20analysis%20module%20failure)
  * [DMHS 配置文件用户密码是否可以加密？](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#DMHS%20%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6%E7%94%A8%E6%88%B7%E5%AF%86%E7%A0%81%E6%98%AF%E5%90%A6%E5%8F%AF%E4%BB%A5%E5%8A%A0%E5%AF%86%EF%BC%9F)
  * [dmhs 目的端日志出现 ERROR 日志：SYSDBA.TEST01 table​'s col AA has too long data](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#dmhs%20%E7%9B%AE%E7%9A%84%E7%AB%AF%E6%97%A5%E5%BF%97%E5%87%BA%E7%8E%B0%20ERROR%20%E6%97%A5%E5%BF%97%EF%BC%9ASYSDBA.TEST01%20table's%20col%20AA%20has%20too%20long%20data)
  * [逻辑库添加数据文件报错：“用户自定义异常或者未知错误，error code=-10002”](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#%E9%80%BB%E8%BE%91%E5%BA%93%E6%B7%BB%E5%8A%A0%E6%95%B0%E6%8D%AE%E6%96%87%E4%BB%B6%E6%8A%A5%E9%94%99%EF%BC%9A%E2%80%9C%E7%94%A8%E6%88%B7%E8%87%AA%E5%AE%9A%E4%B9%89%E5%BC%82%E5%B8%B8%E6%88%96%E8%80%85%E6%9C%AA%E7%9F%A5%E9%94%99%E8%AF%AF%EF%BC%8Cerror%20code=-10002%E2%80%9D)
  * [源端达梦库 db_magic 发生变化，启动 dmhs 报错](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#%E6%BA%90%E7%AB%AF%E8%BE%BE%E6%A2%A6%E5%BA%93%20db_magic%20%E5%8F%91%E7%94%9F%E5%8F%98%E5%8C%96%EF%BC%8C%E5%90%AF%E5%8A%A8%20dmhs%20%E6%8A%A5%E9%94%99)
  * [DMHS 管理模块 MGR 提示: loading the execute module](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#DMHS%20%E7%AE%A1%E7%90%86%E6%A8%A1%E5%9D%97%20MGR%20%E6%8F%90%E7%A4%BA:%20loading%20the%20execute%20module)
  * [MGR[ERROR]:库文件 libcpt_dm8.so 未找到](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#MGR\[ERROR\]:%E5%BA%93%E6%96%87%E4%BB%B6%20libcpt_dm8.so%20%E6%9C%AA%E6%89%BE%E5%88%B0)
  * [DMHS 目的端报错：​"DB:创建 SOCKET 连接失败 (code=3113)​"](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#DMHS%20%E7%9B%AE%E7%9A%84%E7%AB%AF%E6%8A%A5%E9%94%99%EF%BC%9A%22DB:%E5%88%9B%E5%BB%BA%20SOCKET%20%E8%BF%9E%E6%8E%A5%E5%A4%B1%E8%B4%A5%20\(code=3113\)%22)
  * [ORACLE 到 DM8 数据同步，源端报错：“ORA-6512: at ​"SYS.XDBMS_DISGROUP​"](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#ORACLE%20%E5%88%B0%20DM8%20%E6%95%B0%E6%8D%AE%E5%90%8C%E6%AD%A5%EF%BC%8C%E6%BA%90%E7%AB%AF%E6%8A%A5%E9%94%99%EF%BC%9A%E2%80%9CORA-6512:%20at%20%22SYS.X$DBMS_DISGROUP%22)
  * [HS 同步过程是否可以监视，同步结果是否能得到反馈](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#HS%20%E5%90%8C%E6%AD%A5%E8%BF%87%E7%A8%8B%E6%98%AF%E5%90%A6%E5%8F%AF%E4%BB%A5%E7%9B%91%E8%A7%86%EF%BC%8C%E5%90%8C%E6%AD%A5%E7%BB%93%E6%9E%9C%E6%98%AF%E5%90%A6%E8%83%BD%E5%BE%97%E5%88%B0%E5%8F%8D%E9%A6%88)
  * [DMHS 报错：“DB：Network communication failure (code=3113)​"](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#DMHS%20%E6%8A%A5%E9%94%99%EF%BC%9A%E2%80%9CDB%EF%BC%9ANetwork%20communication%20failure%20\(code=3113\)%22)
  * [Oracle 到 dm 的数据同步异常](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#Oracle%20%E5%88%B0%20dm%20%E7%9A%84%E6%95%B0%E6%8D%AE%E5%90%8C%E6%AD%A5%E5%BC%82%E5%B8%B8)
  * [DM8 到 DB2 数据同步中文乱码报错](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#DM8%20%E5%88%B0%20DB2%20%E6%95%B0%E6%8D%AE%E5%90%8C%E6%AD%A5%E4%B8%AD%E6%96%87%E4%B9%B1%E7%A0%81%E6%8A%A5%E9%94%99)
  * [源端 dm7 向 dm8mpp 集群装载数据报错：“建表失败”](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#%E6%BA%90%E7%AB%AF%20dm7%20%E5%90%91%20dm8mpp%20%E9%9B%86%E7%BE%A4%E8%A3%85%E8%BD%BD%E6%95%B0%E6%8D%AE%E6%8A%A5%E9%94%99%EF%BC%9A%E2%80%9C%E5%BB%BA%E8%A1%A8%E5%A4%B1%E8%B4%A5%E2%80%9D)
  * [两端列名不一致如何装载？两端字段类型不一致如何装载？](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#%E4%B8%A4%E7%AB%AF%E5%88%97%E5%90%8D%E4%B8%8D%E4%B8%80%E8%87%B4%E5%A6%82%E4%BD%95%E8%A3%85%E8%BD%BD%EF%BC%9F%E4%B8%A4%E7%AB%AF%E5%AD%97%E6%AE%B5%E7%B1%BB%E5%9E%8B%E4%B8%8D%E4%B8%80%E8%87%B4%E5%A6%82%E4%BD%95%E8%A3%85%E8%BD%BD%EF%BC%9F)
  * [DMHS 源端服务自动终止报错：“[ins0]the number of dictionary tables xxx columns is changed xxx……please reload”](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#DMHS%20%E6%BA%90%E7%AB%AF%E6%9C%8D%E5%8A%A1%E8%87%AA%E5%8A%A8%E7%BB%88%E6%AD%A2%E6%8A%A5%E9%94%99%EF%BC%9A%E2%80%9C\[ins0\]the%20number%20of%20dictionary%20tables%20xxx%20columns%20is%20changed%20xxx%E2%80%A6%E2%80%A6please%20reload%E2%80%9D)
  * [oracle 到 dm 单向同步报错：“error during execution of trigger ​'SYS .DMHS TRIGGER”](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#oracle%20%E5%88%B0%20dm%20%E5%8D%95%E5%90%91%E5%90%8C%E6%AD%A5%E6%8A%A5%E9%94%99%EF%BC%9A%E2%80%9Cerror%20during%20execution%20of%20trigger%20'SYS%20.DMHS%20TRIGGER%E2%80%9D)
  * [达梦数据库实时同步管理平台启动后浏览器无响应](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#%E8%BE%BE%E6%A2%A6%E6%95%B0%E6%8D%AE%E5%BA%93%E5%AE%9E%E6%97%B6%E5%90%8C%E6%AD%A5%E7%AE%A1%E7%90%86%E5%B9%B3%E5%8F%B0%E5%90%AF%E5%8A%A8%E5%90%8E%E6%B5%8F%E8%A7%88%E5%99%A8%E6%97%A0%E5%93%8D%E5%BA%94)
  * [dmhs 实时同步后某张表两端的数据不一致](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#dmhs%20%E5%AE%9E%E6%97%B6%E5%90%8C%E6%AD%A5%E5%90%8E%E6%9F%90%E5%BC%A0%E8%A1%A8%E4%B8%A4%E7%AB%AF%E7%9A%84%E6%95%B0%E6%8D%AE%E4%B8%8D%E4%B8%80%E8%87%B4)
  * [如何快速将 oracle 中存在 long raw 字段的表装载到 DM 表中](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#%E5%A6%82%E4%BD%95%E5%BF%AB%E9%80%9F%E5%B0%86%20oracle%20%E4%B8%AD%E5%AD%98%E5%9C%A8%20long%20raw%20%E5%AD%97%E6%AE%B5%E7%9A%84%E8%A1%A8%E8%A3%85%E8%BD%BD%E5%88%B0%20DM%20%E8%A1%A8%E4%B8%AD)
  * [HS 单向同步报错：”表中指定的列名在目的表中不存在导致数据装载失败“](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#HS%20%E5%8D%95%E5%90%91%E5%90%8C%E6%AD%A5%E6%8A%A5%E9%94%99%EF%BC%9A%E2%80%9D%E8%A1%A8%E4%B8%AD%E6%8C%87%E5%AE%9A%E7%9A%84%E5%88%97%E5%90%8D%E5%9C%A8%E7%9B%AE%E7%9A%84%E8%A1%A8%E4%B8%AD%E4%B8%8D%E5%AD%98%E5%9C%A8%E5%AF%BC%E8%87%B4%E6%95%B0%E6%8D%AE%E8%A3%85%E8%BD%BD%E5%A4%B1%E8%B4%A5%E2%80%9C)
  * [如何将应用程序中数据库字符串中账户口令变成密文](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#%E5%A6%82%E4%BD%95%E5%B0%86%E5%BA%94%E7%94%A8%E7%A8%8B%E5%BA%8F%E4%B8%AD%E6%95%B0%E6%8D%AE%E5%BA%93%E5%AD%97%E7%AC%A6%E4%B8%B2%E4%B8%AD%E8%B4%A6%E6%88%B7%E5%8F%A3%E4%BB%A4%E5%8F%98%E6%88%90%E5%AF%86%E6%96%87)
  * [DMHS 源端无法访问 kafka 集群](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#DMHS%20%E6%BA%90%E7%AB%AF%E6%97%A0%E6%B3%95%E8%AE%BF%E9%97%AE%20kafka%20%E9%9B%86%E7%BE%A4)
  * [DM 到 DM 的 HS 双向同步存在数据回写问题](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#DM%20%E5%88%B0%20DM%20%E7%9A%84%20HS%20%E5%8F%8C%E5%90%91%E5%90%8C%E6%AD%A5%E5%AD%98%E5%9C%A8%E6%95%B0%E6%8D%AE%E5%9B%9E%E5%86%99%E9%97%AE%E9%A2%98)
  * [MySQL 数据同步到 DM 时源端同步日志报错：[insert xxx] not support](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#MySQL%20%E6%95%B0%E6%8D%AE%E5%90%8C%E6%AD%A5%E5%88%B0%20DM%20%E6%97%B6%E6%BA%90%E7%AB%AF%E5%90%8C%E6%AD%A5%E6%97%A5%E5%BF%97%E6%8A%A5%E9%94%99%EF%BC%9A\[insert%20xxx\]%20not%20support)
  * [目的端自动创建索引](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#%E7%9B%AE%E7%9A%84%E7%AB%AF%E8%87%AA%E5%8A%A8%E5%88%9B%E5%BB%BA%E7%B4%A2%E5%BC%95)
  * [DMHS 数据装载时报错 “name 列的数据类型 sysname 在映射表中没有找到”](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#DMHS%20%E6%95%B0%E6%8D%AE%E8%A3%85%E8%BD%BD%E6%97%B6%E6%8A%A5%E9%94%99%20%E2%80%9Cname%20%E5%88%97%E7%9A%84%E6%95%B0%E6%8D%AE%E7%B1%BB%E5%9E%8B%20sysname%20%E5%9C%A8%E6%98%A0%E5%B0%84%E8%A1%A8%E4%B8%AD%E6%B2%A1%E6%9C%89%E6%89%BE%E5%88%B0%E2%80%9D)
  * [DMHS 数据装载时报错：“字符串截断”](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#DMHS%20%E6%95%B0%E6%8D%AE%E8%A3%85%E8%BD%BD%E6%97%B6%E6%8A%A5%E9%94%99%EF%BC%9A%E2%80%9C%E5%AD%97%E7%AC%A6%E4%B8%B2%E6%88%AA%E6%96%AD%E2%80%9D)
  * [在进行 DMHS 装载数据时报错：恢复模式检查失败](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#%E5%9C%A8%E8%BF%9B%E8%A1%8C%20DMHS%20%E8%A3%85%E8%BD%BD%E6%95%B0%E6%8D%AE%E6%97%B6%E6%8A%A5%E9%94%99%EF%BC%9A%E6%81%A2%E5%A4%8D%E6%A8%A1%E5%BC%8F%E6%A3%80%E6%9F%A5%E5%A4%B1%E8%B4%A5)
  * [SQLSERVER 部署 DMHS ， 在配置维护计划时报错：创建维护计划失败。](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#SQLSERVER%20%E9%83%A8%E7%BD%B2%20DMHS%20%EF%BC%8C%20%E5%9C%A8%E9%85%8D%E7%BD%AE%E7%BB%B4%E6%8A%A4%E8%AE%A1%E5%88%92%E6%97%B6%E6%8A%A5%E9%94%99%EF%BC%9A%E5%88%9B%E5%BB%BA%E7%BB%B4%E6%8A%A4%E8%AE%A1%E5%88%92%E5%A4%B1%E8%B4%A5%E3%80%82)
  * [源端 oracle 数据包卡住 agent 日志及 veri 的日志均无报错信息](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#%E6%BA%90%E7%AB%AF%20oracle%20%E6%95%B0%E6%8D%AE%E5%8C%85%E5%8D%A1%E4%BD%8F%20agent%20%E6%97%A5%E5%BF%97%E5%8F%8A%20veri%20%E7%9A%84%E6%97%A5%E5%BF%97%E5%9D%87%E6%97%A0%E6%8A%A5%E9%94%99%E4%BF%A1%E6%81%AF)
  * [源端 oracle 目的端 DM 如何使用一条命令装载多张表？](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#%E6%BA%90%E7%AB%AF%20oracle%20%E7%9B%AE%E7%9A%84%E7%AB%AF%20DM%20%E5%A6%82%E4%BD%95%E4%BD%BF%E7%94%A8%E4%B8%80%E6%9D%A1%E5%91%BD%E4%BB%A4%E8%A3%85%E8%BD%BD%E5%A4%9A%E5%BC%A0%E8%A1%A8%EF%BC%9F)
  * [DMHS 执行端报错：”数据过长字符串截断同步失败”](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#DMHS%20%E6%89%A7%E8%A1%8C%E7%AB%AF%E6%8A%A5%E9%94%99%EF%BC%9A%E2%80%9D%E6%95%B0%E6%8D%AE%E8%BF%87%E9%95%BF%E5%AD%97%E7%AC%A6%E4%B8%B2%E6%88%AA%E6%96%AD%E5%90%8C%E6%AD%A5%E5%A4%B1%E8%B4%A5%E2%80%9D)
  * [DMHS 将数据从源端装载至目的端后比对时两端数据质量不一致](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#DMHS%20%E5%B0%86%E6%95%B0%E6%8D%AE%E4%BB%8E%E6%BA%90%E7%AB%AF%E8%A3%85%E8%BD%BD%E8%87%B3%E7%9B%AE%E7%9A%84%E7%AB%AF%E5%90%8E%E6%AF%94%E5%AF%B9%E6%97%B6%E4%B8%A4%E7%AB%AF%E6%95%B0%E6%8D%AE%E8%B4%A8%E9%87%8F%E4%B8%8D%E4%B8%80%E8%87%B4)
  * [ORACLE 到 ORACLE 的 DMHS 数据装载时报错：“表空间大小不足”](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#ORACLE%20%E5%88%B0%20ORACLE%20%E7%9A%84%20DMHS%20%E6%95%B0%E6%8D%AE%E8%A3%85%E8%BD%BD%E6%97%B6%E6%8A%A5%E9%94%99%EF%BC%9A%E2%80%9C%E8%A1%A8%E7%A9%BA%E9%97%B4%E5%A4%A7%E5%B0%8F%E4%B8%8D%E8%B6%B3%E2%80%9D)
  * [如何根据源端的 rowid 及目的端 ##rowid 进行数据对比？](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#%E5%A6%82%E4%BD%95%E6%A0%B9%E6%8D%AE%E6%BA%90%E7%AB%AF%E7%9A%84%20rowid%20%E5%8F%8A%E7%9B%AE%E7%9A%84%E7%AB%AF%20##rowid%20%E8%BF%9B%E8%A1%8C%E6%95%B0%E6%8D%AE%E5%AF%B9%E6%AF%94%EF%BC%9F)
  * [oracle 到 dm 的 DMHS 数据同步如何通过 HS 更新某列的值？](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#oracle%20%E5%88%B0%20dm%20%E7%9A%84%20DMHS%20%E6%95%B0%E6%8D%AE%E5%90%8C%E6%AD%A5%E5%A6%82%E4%BD%95%E9%80%9A%E8%BF%87%20HS%20%E6%9B%B4%E6%96%B0%E6%9F%90%E5%88%97%E7%9A%84%E5%80%BC%EF%BC%9F)
  * [DMHS 数据同步如何使用 print 打印某一时间段错误的 rowid 或 sql 语句](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#DMHS%20%E6%95%B0%E6%8D%AE%E5%90%8C%E6%AD%A5%E5%A6%82%E4%BD%95%E4%BD%BF%E7%94%A8%20print%20%E6%89%93%E5%8D%B0%E6%9F%90%E4%B8%80%E6%97%B6%E9%97%B4%E6%AE%B5%E9%94%99%E8%AF%AF%E7%9A%84%20rowid%20%E6%88%96%20sql%20%E8%AF%AD%E5%8F%A5)
  * [DMHS 数据同步时 CVT 文件不支持 to_char(to_date()) 嵌套函数。](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#DMHS%20%E6%95%B0%E6%8D%AE%E5%90%8C%E6%AD%A5%E6%97%B6%20CVT%20%E6%96%87%E4%BB%B6%E4%B8%8D%E6%94%AF%E6%8C%81%20to_char\(to_date\(\)\)%20%E5%B5%8C%E5%A5%97%E5%87%BD%E6%95%B0%E3%80%82)
  * [DMHS VERI 二次数据对比完成后如何根据指定的 rowid 进行数据修复](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#DMHS%20VERI%20%E4%BA%8C%E6%AC%A1%E6%95%B0%E6%8D%AE%E5%AF%B9%E6%AF%94%E5%AE%8C%E6%88%90%E5%90%8E%E5%A6%82%E4%BD%95%E6%A0%B9%E6%8D%AE%E6%8C%87%E5%AE%9A%E7%9A%84%20rowid%20%E8%BF%9B%E8%A1%8C%E6%95%B0%E6%8D%AE%E4%BF%AE%E5%A4%8D)
  * [使用 DMHS VERI 进行数据对比时报错：​"标识符过长​"](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#%E4%BD%BF%E7%94%A8%20DMHS%20VERI%20%E8%BF%9B%E8%A1%8C%E6%95%B0%E6%8D%AE%E5%AF%B9%E6%AF%94%E6%97%B6%E6%8A%A5%E9%94%99%EF%BC%9A%22%E6%A0%87%E8%AF%86%E7%AC%A6%E8%BF%87%E9%95%BF%22)
  * [HSEM 部署数据对比时报错：“veri.conf 文件中获取 task_id、ver_ppid 失败”](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#HSEM%20%E9%83%A8%E7%BD%B2%E6%95%B0%E6%8D%AE%E5%AF%B9%E6%AF%94%E6%97%B6%E6%8A%A5%E9%94%99%EF%BC%9A%E2%80%9Cveri.conf%20%E6%96%87%E4%BB%B6%E4%B8%AD%E8%8E%B7%E5%8F%96%20task_id%E3%80%81ver_ppid%20%E5%A4%B1%E8%B4%A5%E2%80%9D)
  * [启动 veri 对比服务报错： “error while loadind shared libraries: libodbc.so.1”](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#%E5%90%AF%E5%8A%A8%20veri%20%E5%AF%B9%E6%AF%94%E6%9C%8D%E5%8A%A1%E6%8A%A5%E9%94%99%EF%BC%9A%20%E2%80%9Cerror%20while%20loadind%20shared%20libraries:%20libodbc.so.1%E2%80%9D)
  * [如何确认 rowid 是否修复成功](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#%E5%A6%82%E4%BD%95%E7%A1%AE%E8%AE%A4%20rowid%20%E6%98%AF%E5%90%A6%E4%BF%AE%E5%A4%8D%E6%88%90%E5%8A%9F)
  * [使用 HSEM 进行数据对比时界面显示异常](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#%E4%BD%BF%E7%94%A8%20HSEM%20%E8%BF%9B%E8%A1%8C%E6%95%B0%E6%8D%AE%E5%AF%B9%E6%AF%94%E6%97%B6%E7%95%8C%E9%9D%A2%E6%98%BE%E7%A4%BA%E5%BC%82%E5%B8%B8)
  * [ORACEL 到 DM 的数据同步在对目的端大字段列进行更新时报错：​"回滚版本记录过旧​"](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#ORACEL%20%E5%88%B0%20DM%20%E7%9A%84%E6%95%B0%E6%8D%AE%E5%90%8C%E6%AD%A5%E5%9C%A8%E5%AF%B9%E7%9B%AE%E7%9A%84%E7%AB%AF%E5%A4%A7%E5%AD%97%E6%AE%B5%E5%88%97%E8%BF%9B%E8%A1%8C%E6%9B%B4%E6%96%B0%E6%97%B6%E6%8A%A5%E9%94%99%EF%BC%9A%22%E5%9B%9E%E6%BB%9A%E7%89%88%E6%9C%AC%E8%AE%B0%E5%BD%95%E8%BF%87%E6%97%A7%22)
  * [ORACEL 到 DM 的数据同步过程中如何检查源端装载表的数据字典否存在](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#ORACEL%20%E5%88%B0%20DM%20%E7%9A%84%E6%95%B0%E6%8D%AE%E5%90%8C%E6%AD%A5%E8%BF%87%E7%A8%8B%E4%B8%AD%E5%A6%82%E4%BD%95%E6%A3%80%E6%9F%A5%E6%BA%90%E7%AB%AF%E8%A3%85%E8%BD%BD%E8%A1%A8%E7%9A%84%E6%95%B0%E6%8D%AE%E5%AD%97%E5%85%B8%E5%90%A6%E5%AD%98%E5%9C%A8)
  * [Oracle 到 DM 数据同步如何将同步到目的端的表名转换为大写？](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#Oracle%20%E5%88%B0%20DM%20%E6%95%B0%E6%8D%AE%E5%90%8C%E6%AD%A5%E5%A6%82%E4%BD%95%E5%B0%86%E5%90%8C%E6%AD%A5%E5%88%B0%E7%9B%AE%E7%9A%84%E7%AB%AF%E7%9A%84%E8%A1%A8%E5%90%8D%E8%BD%AC%E6%8D%A2%E4%B8%BA%E5%A4%A7%E5%86%99%EF%BC%9F)
  * [配置过滤规则后，DMHS 启动报错](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#%E9%85%8D%E7%BD%AE%E8%BF%87%E6%BB%A4%E8%A7%84%E5%88%99%E5%90%8E%EF%BC%8CDMHS%20%E5%90%AF%E5%8A%A8%E6%8A%A5%E9%94%99)
  * [DMHS 源端初始化用户时报错：PUB[ERROR]:获取客户端字符集失败](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#DMHS%20%E6%BA%90%E7%AB%AF%E5%88%9D%E5%A7%8B%E5%8C%96%E7%94%A8%E6%88%B7%E6%97%B6%E6%8A%A5%E9%94%99%EF%BC%9APUB\[ERROR\]:%E8%8E%B7%E5%8F%96%E5%AE%A2%E6%88%B7%E7%AB%AF%E5%AD%97%E7%AC%A6%E9%9B%86%E5%A4%B1%E8%B4%A5)
  * [Oracle 到 DM 数据同步目的端无数据](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#Oracle%20%E5%88%B0%20DM%20%E6%95%B0%E6%8D%AE%E5%90%8C%E6%AD%A5%E7%9B%AE%E7%9A%84%E7%AB%AF%E6%97%A0%E6%95%B0%E6%8D%AE)
  * [DMHS 目的端同步长时间打印“CHECKPOINT WAIT SITEID:XXXX，TRXID: XXXX”](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#DMHS%20%E7%9B%AE%E7%9A%84%E7%AB%AF%E5%90%8C%E6%AD%A5%E9%95%BF%E6%97%B6%E9%97%B4%E6%89%93%E5%8D%B0%E2%80%9CCHECKPOINT%20WAIT%20SITEID:XXXX%EF%BC%8CTRXID:%20XXXX%E2%80%9D)
  * [DMHS 目的端日志提示：“收到来自 xx.xx.xx.xx 的连接请求，xxxxx，这次请求被关闭”](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#DMHS%20%E7%9B%AE%E7%9A%84%E7%AB%AF%E6%97%A5%E5%BF%97%E6%8F%90%E7%A4%BA%EF%BC%9A%E2%80%9C%E6%94%B6%E5%88%B0%E6%9D%A5%E8%87%AA%20xx.xx.xx.xx%20%E7%9A%84%E8%BF%9E%E6%8E%A5%E8%AF%B7%E6%B1%82%EF%BC%8Cxxxxx%EF%BC%8C%E8%BF%99%E6%AC%A1%E8%AF%B7%E6%B1%82%E8%A2%AB%E5%85%B3%E9%97%AD%E2%80%9D)
  * [dmhs 初始化脚本如何在 disql 中运行](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#dmhs%20%E5%88%9D%E5%A7%8B%E5%8C%96%E8%84%9A%E6%9C%AC%E5%A6%82%E4%BD%95%E5%9C%A8%20disql%20%E4%B8%AD%E8%BF%90%E8%A1%8C)
  * [Oracle 到 DM 数据同步在线装载数据时提示“回滚段版本过旧”](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#Oracle%20%E5%88%B0%20DM%20%E6%95%B0%E6%8D%AE%E5%90%8C%E6%AD%A5%E5%9C%A8%E7%BA%BF%E8%A3%85%E8%BD%BD%E6%95%B0%E6%8D%AE%E6%97%B6%E6%8F%90%E7%A4%BA%E2%80%9C%E5%9B%9E%E6%BB%9A%E6%AE%B5%E7%89%88%E6%9C%AC%E8%BF%87%E6%97%A7%E2%80%9D)
  * [如何打印绑定变量的具体变量值](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#%E5%A6%82%E4%BD%95%E6%89%93%E5%8D%B0%E7%BB%91%E5%AE%9A%E5%8F%98%E9%87%8F%E7%9A%84%E5%85%B7%E4%BD%93%E5%8F%98%E9%87%8F%E5%80%BC)
  * [目标端 start exec 报错：“please set constraint and trigger on cpt mudule to 0”](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#%E7%9B%AE%E6%A0%87%E7%AB%AF%20start%20exec%20%E6%8A%A5%E9%94%99%EF%BC%9A%E2%80%9Cplease%20set%20constraint%20and%20trigger%20on%20cpt%20mudule%20to%200%E2%80%9D)
  * [进行 kafka 同步时，启动 start_dmhs_kafka.sh 报错](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#%E8%BF%9B%E8%A1%8C%20kafka%20%E5%90%8C%E6%AD%A5%E6%97%B6%EF%BC%8C%E5%90%AF%E5%8A%A8%20start_dmhs_kafka.sh%20%E6%8A%A5%E9%94%99)
  * [Oracle19c 同步到 DM 源端 hs 实例日志告警：​"无法定位对象 xxxxxx 的 DDL 事件​"](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#Oracle19c%20%E5%90%8C%E6%AD%A5%E5%88%B0%20DM%20%E6%BA%90%E7%AB%AF%20hs%20%E5%AE%9E%E4%BE%8B%E6%97%A5%E5%BF%97%E5%91%8A%E8%AD%A6%EF%BC%9A%22%E6%97%A0%E6%B3%95%E5%AE%9A%E4%BD%8D%E5%AF%B9%E8%B1%A1%20xxxxxx%20%E7%9A%84%20DDL%20%E4%BA%8B%E4%BB%B6%22)
  * [未找到动态库文件 path: libdrekafka.so](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#%E6%9C%AA%E6%89%BE%E5%88%B0%E5%8A%A8%E6%80%81%E5%BA%93%E6%96%87%E4%BB%B6%20path:%20libdrekafka.so)
  * [DMHS 源端报错：“检测到 DDL 同步辅助表有缺失，请重建”](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#DMHS%20%E6%BA%90%E7%AB%AF%E6%8A%A5%E9%94%99%EF%BC%9A%E2%80%9C%E6%A3%80%E6%B5%8B%E5%88%B0%20DDL%20%E5%90%8C%E6%AD%A5%E8%BE%85%E5%8A%A9%E8%A1%A8%E6%9C%89%E7%BC%BA%E5%A4%B1%EF%BC%8C%E8%AF%B7%E9%87%8D%E5%BB%BA%E2%80%9D)
  * [DM 同步到 KAFKA ，目的端 HS 服务报错：“Invalid JSON format message”](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#DM%20%E5%90%8C%E6%AD%A5%E5%88%B0%20KAFKA%20%EF%BC%8C%E7%9B%AE%E7%9A%84%E7%AB%AF%20HS%20%E6%9C%8D%E5%8A%A1%E6%8A%A5%E9%94%99%EF%BC%9A%E2%80%9CInvalid%20JSON%20format%20message%E2%80%9D)
  * [DMHS 源端到目的端同步缓慢](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#DMHS%20%E6%BA%90%E7%AB%AF%E5%88%B0%E7%9B%AE%E7%9A%84%E7%AB%AF%E5%90%8C%E6%AD%A5%E7%BC%93%E6%85%A2)
  * [DMHS 装载自增列报错](https://eco.dameng.com/document/dm/zh-cn/faq/faq-DMHS.html#DMHS%20%E8%A3%85%E8%BD%BD%E8%87%AA%E5%A2%9E%E5%88%97%E6%8A%A5%E9%94%99)



## 正文

DMHS 安装部署可参考文章：[《DMHS 安装部署》](https://eco.dameng.com/docs/zh-cn/start/dmhs-install.html)

## Dmhs 出现目的端同步 update 或 delete 操作出现报错：exec[ERROR] sync failed,fill row 1 该如何处理？

【问题解决】：

dmhs 在做 update 或 delete 操作时会将源端的影响行数与目的端的影响行数进行对比，确保源端和目的端修改的行数是一样的。可通过修改 exec 的参数 affect_row 为 0。
affect_row：是否启用影响行数检查，可选 0（禁用）/1（启用），默认值 1。启动该项检查可以有效的保证同步的正确性，但是如果本身源库和目标库数据就不一致，则建议关掉该项检查，因为检查了也无意义，但打开了会频繁的打印出错信息，反而影响同步性能。
注意：该方法并不能解决源端和目的端数据不一致的问题，因此只适用于不要求源端和目的端数据一致的场景。

## 在进行单表数据对比时，dmhs_veri 对比工具如何限定数据的对比范围（即添加 where 条件）？

【问题解决】：

对比工具 rev106302 及以后的版本支持该功能。涉及如下三个参数：
（1）COND：WHERE 条件，同时加到源端数据库和目的端数据库。
（2）COND_SRC：WHERE 条件，源端数据库。
（3）COND_DEST：WHERE 条件，目的端数据库。
注意对比命令格式,以 linux 环境为例：

```
./dmhs_veri conf=veri.xml "table=(DMHS.T1==SYSDBA.T1)" "COND=(ID>1 and ID<10)" mode=normal count=0
```

说明：1.此命令中指定数据范围对比一般首取参数 COND。COND_DEST、COND_SRC 需要在指定场景使用。
2.ID 列一般为主键列。

## 在进行模式名.*对比时，dmhs_veri 数据对比工具如何通过使用黑名单功能禁用某些表进行数据对比？

【问题解决】：

dmhs_veri 数据对比工具 rev106302 版本之后支持黑名单过滤功能，即模式名.*对比时可以不对某些表进行数据对比。注意对比命令格式，以 linux 环境为例：

```
---禁用单表对比：
./dmhs_veri  jobname=test1 "table=(DMHS.*==DMHS.*) except_tables=\(DMHS.T1\) MODE=normal
---禁用多表对比：
./dmhs_veri  jobname=test1 "table=(DMHS.*==DMHS.*) except_tables=\(DMHS.T1,DMHS.T2\) MODE=normal
```

说明：目前此命令仅支持禁用少量表，否则命令超长执行不成功。

## 部署 mysql8.0 到 dm8 的 DMHS 数据同步时报错：initial log analysis module failure

【问题描述】：

部署 mysql8.0 到 dm8 的 DMHS 数据同步，在源端 dmhs_console 执行 start cpt 时，出现以下报错：

dmhs_console 端报错信息：`CSL[ERROR]: initial log analysis module failure`，或者提示 `execute success`，且 dmhs_server 端出现如下报错信息：

```
CPT[INFO]: MYSQL_V3.1.2_D64
CPT[INFO]: MySQL lower_case_table_names is: 1
PUB[ERROR]: os_file_open_readonly open error, file: /home/data/mysql8028/8004/logs/0010108004-mysql-bin.000001, direct io flag: 0, errno: 13
CPT[ERROR]: File /home/data/mysql8028/8004/logs/0010108004-mysql-bin.000001 open error, failed to locate synchronization staring offset!
CPT[ERROR]: Failed to get rotate event log, binary log file () has not been added!
MGR[ERROR]: initial log analysis module failure
MGR[ERROR]: unable to start capture, ID no: 1
```

【问题解决】：

出现该报错是因为 dmdba 没有权限读取 mysql binlog 导致的，解决办法有两种：
方法一：直接将 binlog 上层的 log 文件夹权限级联修改为 644 （效果等同添加 o+r 或 a+r 权限），如 chmod -R 644 /home/data/mysql8028/8004/logs
方法二：将 DMHS 运行程序用户组改为 MySQL，具体操作如下：
（1）将用户添加工作组，如 `usermod -G dmdba mysql`。
（2）将 dmhs 执行目录赋予组权限。如：`chown -R dmdba:mysql /opt/dmhs/bin`。
（3）切换成 dmdba 用户运行 dmhs 服务正常（备注：启动 dmhs 服务之前注意检查环境变量，例如此同步环境可能需要加载 libodbc.so.2 和 libmysqlclient.so.18 的相关路径）。

> **注意**
>
> 一般情况下，建议使用方法二，方法一仅当 binlog 存放在单独文件夹时推荐使用，否则随着新的 binlog 文件产生，仍旧会出现该报错。



## DMHS 配置文件用户密码是否可以加密？

【问题描述】：

如下图所示的 DMHS 配置文件用户密码需要加密，明文显示不安全。

![配置文件信息](https://download.dameng.com/eco/docs/asset/faq/DMHS/faq-dmhs-001.png)

【问题解决】：

DMHS 配置文件中的用户密码可以加密，具体步骤如下：

1.通过控制台获取密码对应密文：

```
./dmhs_console 
DMHS> encrypt "123456"
密文：427f0c435836e8d9
```

2.在配置文件中使用密文。

![修改配置文件](https://download.dameng.com/eco/docs/asset/faq/DMHS/faq-dmhs-002.png)

## dmhs 目的端日志出现 ERROR 日志：SYSDBA.TEST01 table's col AA has too long data

【问题描述】：

dmhs 目的端日志出现 ERROR 日志：SYSDBA.TEST01 table's col AA has too long data, will be truncated destination  prec 24, but input 45(xxxxxx)

【问题解决】：

分析：该报错表明，dmhs 向目的端 SYSDBA.TEST01 表中插入数据时 AA 的列值超长，插入值为"xxxxxx"。该报错的产生原因有两种：

（1）目的端数据库的列定义长度小于源端。

（2）源端和目的端对字符串长度计算不同。

解决：可通过将目的端的列定义长度增大，并重新手动插入该条数据解决。

## 逻辑库添加数据文件报错：“用户自定义异常或者未知错误，error code=-10002”

【问题描述】：

dm6 环境部署有 dmhs 数据同步，在给逻辑库执行以下命令添加数据文件时，执行提示：“用户自定义异常或者未知错误，error code=-10002”。

```
alter database XNY add datafile '/dbdata/dmdata/xny/XNY16.dbf' size 8;
```

数据库和 DMHS 版本信息如下：

|**数据库版本（DB）**|**V6.0.2.80-Build(2019.10.25)**|
|---------------------------|---------------------------|
|数据同步软件（DMHS）|V3.1.2-Build(2018.09.12-79669trunc)_6|


【问题解决】：

分析：经排查发现，此问题出现在给源端添加数据文件时，且源端使用的 DDL 脚本为 V2 版本。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20220817171042ETD1TMKJ8H81X1NDDJ)

此情况下，禁用对应逻辑库( XNY 库)的触发器后，再执行添加，即可成功。示例如下：

```
ALTER TRIGGER XNY.SYSDBA.DMHS_DDL_TRIGGER_BEFORE DISABLE;
ALTER TRIGGER XNY.SYSDBA.DMHS_DDL_TRIGGER_AFTER DISABLE;
```

## 源端达梦库 db_magic 发生变化，启动 dmhs 报错

【问题描述】：

dmhs 源端达梦库 db_magic 发生变化，启动 dmhs 后报错：

```
Database Magic is not consistent(847550855:278241428), maybe DB is restored or changed, please check connected DB or reload dictionary using CLEAR mask and delete checked file: dmhs_1207_0.ini
```

【问题解决】：

可通过如下步骤解决：

  1. 清理源端库历史归档日志，通过比较 \`V$ARCHIVED_LOG \` 中 name 字段中的归档日志文件与 dmarch.ini 中配置的归档目录中的归档文件，删除归档目录中含有但不在 \`V$ARCHIVED_LOG\` 中 name 字段中的归档日志文件。
  2. 删除 dmhs 执行目录下含有 conf 和 ini 的文件与文件夹 。



```
cd dmhs/bin  
rm -rf conf  
rm -rf ini
```

  3. 重新启动 dmhs 后，即可正常同步 \`./dmhs_server dmhs.hs\`。



## DMHS 管理模块 MGR 提示: loading the execute module

【问题描述】：

dmhs 启动 exec 模块后管理模块 MGR 提示: "loading the execute module"。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221020105000B41HM87W8GFUN0CTF8)

【问题解决】：

以上报错主要原因是 libdmoci.so 动态库文件与 DMHS 版本不匹配导致。更换与 DMHS 版本匹配的 libdmoci.so 动态库文件即可。

## MGR[ERROR]:库文件 libcpt_dm8.so 未找到

【问题描述】：

cpt 捕获器端，控制台中进行初始装载时异常，日志报错："MGR[ERROR]: 库文件 libcpt_dm8.so 未找到, 出错: 0, libdmoci.so: cannot open shared object file: No such file or directory"。

出现报错后核实 libcpt_dm8.so（dmhs 安装路径）和 libdmoci.so（dm 数据库安装路径）均存在，且环境变量 LD_LIBRARY_PATH 中也已经配置相应的路径。

【问题分析】：

注意，安装 dmhs 时会有如下提示：提示输入相关依赖路径，多个路径以":"隔开，以 DM 数据库为例：/opt/dmdbms/bin:/usr/local/lib。此配置项会添加到服务脚本的 NEED_LIB_PATH 的变量值中。如果是通过服务方式启动 dmhs 服务，环境变量会优先调用服务脚本中的 NEED_LIB_PATH 变量，该值可通过修改 DMHS 相关服务的后台脚本重启生效。以上执行需确保 DM 数据库 bin 目录下的对应 dmoci 相关动态库已更新。

因此，出现以上报错一般是安装过程中 NEED_LIB_PATH 变量缺失导致。可以单独修改服务脚本中的 NEED_LIB_PATH 值，NEED_LIB_PATH 变量值的取值以实际情况为准。

## DMHS 目的端报错："DB:创建 SOCKET 连接失败 (code=3113)"

【问题解决】

该报错信息大部分与数据库连接有关，排查方向如下：

  1. 首先检查数据库服务是否正常。



检查数据库进程是否存在

```
ps -ef |grep dms
```

检查数据库能否正常登录

```
./disql username/password@ip:端口 
```

  2. 确认相关数据库架构。如 DM 是否为单机、主备、MPP、DSC 等。
  3. 检查不同数据库架构 dmhs.hs 配置参数是否正确、完整，如执行端为以下场景：



  * 所有架构首先需检查 IP 和端口号是否与 dmhs.hs 配置一致。  
检查 dmhs.hs 配置文件中 db_server,db_port 参数是否和数据库 IP 端口一致.

  * DM 主备场景中, db_server 连接为服务名且与 dm_svc.conf 保持一致、且存在 group 参数。  
\--db_server 配置为服务名连接时，需检查 dm_svc.conf 文件中服务名是否一致，ip 端口是否配置正确。建议先使用服务名进行登录测试。

    
        ./disql username/password@服务名
    

\--dm_svc.conf 配置文件 linux 放到 /etc 目录下，window 系统放到
C:\Windows\System32、C:\Windows\System64 下。  
\--存在 group 参数表示 DMHS 主备功能，即同时在数据库集群的每个节点上都部署并启动 DMHS 服务，其中根据优先级设置，只有一个 DMHS
服务为主服务，并进行实际的数据同步；而其他节点的 DMHS 服务则为备服务，主要进行状态监测以及在必要的时候进行主机接管操作。

  * MPP 场景中检查是否存在 db_type 且存在 DM8_MPP、DM8_MPP_LOCAL 等限定参数。  
\--MPP 场景中查看 db_type 参数确定数据库类型，目的端为节点数相同的 MMP 集群 db_type 为
DM8_MPP_LOCAL,目的端为节点数不同的 MPP 集群 db_type 为 DM8_MPP。

  * DSC 场景中,db_server 连接为服务名且与 dm_svc.conf 保持一致，如果为单节点也可以配置 IP。



## ORACLE 到 DM8 数据同步，源端报错：“ORA-6512: at "SYS.X$DBMS_DISGROUP"

【问题描述】

DMHS 同步 ORACLE 11g RAC 到 DM8，其中 ORACLE RAC 两个节点使用 ASM 做共享存储类型，REDO 存放在 ASM 磁盘组，Oracle 归档存放在本地。通过 nfs 映射归档到部署 DMHS 的机器，当映射属性设置如下时报错：

```
[oracle@pdmrac2:/home/oracle]$cat/etc/exports
/oracle/archivelog/pdmrac2192.168.1.201(ro,all_squash)
```

源端 oracle 的 DMHS 日志报错如下：

```
2022-11-0123:56:54CPT[ERROR]:DB:ORA-15056:additional error message
ORA-27041:unable to open file 
Linux-x86_64 Error:13:Permission denied
ORA-06512:at "SYS.X$DBMS_DISKGROUP",line 391
ORA-06512:at line 1
```

【问题分析】

nfs 服务器的 /etc/exports 配置的用户映射选项 all_squash 对共享访问的用户及所属组都映射为匿名用户；而 Oracle RAC 访问归档，需要有 ASM 的 asmadmin 组属性，所以建 nfs 映射归档，必须采用 no_all_squash 非匿名访问映射文件。

【问题解决】

创建 nfs 映射归档，采用 no_all_squash 非匿名访问映射文件，即可解决该问题。

```
[oracle@pdmrac2:/home/oracle]$cat/etc/exports
/oracle/archivelog/pdmrac2192.168.1.201(ro,no_all_squash)
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221110160419KB0JVOWF2M4RVKJQIV)

nfs 共享文件的路径视情况添加，当创建的 nfs 共享文件的路径，和 v$archived_log.name 视图查询出的归档路径相同时，就不需要使用 dir_replace 设置路径映射。

## HS 同步过程是否可以监视，同步结果是否能得到反馈

【问题解决】

支持 HS 同步过程监视和同步结果反馈，结果存储在表中，供应用查询。

在 DMHS 开启监控同步状态后，会在 DMHS 目的端数据库中生成状态记录表 U_HS_STAT_TAB，表属性如下：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202211110956461JZJG5D4TIC9ZZF08I)

初始化时会将所有同步表登记到该表中，每张同步表一行记录，【STATUS】置为【OK】。当同步过程中出现错误时，将报错表的【STATUS】更新为【ERROR】，并将【UPDATE_TIME】更新为【sysdate】。当通过 DMHS 重新装载或不通过 DMHS，人工处理报错数据后，可以自动或手动将【同步状态】更新为【OK】并更新【UPDATE_TIME】。

搭建步骤：

  1. 在源端添加参数如下图：



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221111095934N2NE8DXM48TR6I8AQL)

字段说明：

name:同步链路的名称，自定义，根据同步情况取名。

link_id:同步链路的 id（识别号），全局唯一。

  2. 在目的端添加参数如下图：



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221111100109PF0MMOSNXS42P6QK1I)

字段说明：

stat_tab:开启同步监控，生成监控表。

  3. 重启同步。
  4. 在目的端数据库可通过以下 SQL 查看表。



```
select*from"SYSDBA"."U_HS_STAT_TAB";
```

测试：

准备测试环境，模拟一三区同步。现准备四张同步表，分别为 TEST1107，TEST1107_1，TEST1107_2，TEST1107_3。其中 TEST1107 模拟正常同步，TEST1107_1 模拟目的端比源端少数据的情况下同步，TEST1107_2 模拟目的端没有表的同步，TEST1107_3 模拟源端目的端表结构不一致的同步。结果如下：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221111100625AFTMEUMTCBNEANU465)

从上图可以看到 TEST1107 和 TEST1107_1 同步正常，而 TEST1107_2 和 TEST1107_3 同步异常，状态显示错误。手动处理错误同步，再次查看状态正常，如下：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221111100720U2FU03UVBKDERFD708)

结论：

从上述情况看，目前 DMHS 能够监控到表级同步的状态，同步结果能得到反馈。

## DMHS 报错：“DB：Network communication failure (code=3113)"

【问题描述】

DMHS 在装载过程中报错： “DB：Network communication failure (code=3113)"。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221122091457FFCQ3T1CDZYLK6ZXWE)

【问题分析】

源端和目的端数据库服务在数据装载期间正常，此问题可能是由于网络波动导致装载报错。

【问题解决】

装载的时候在装载选项中增加 trytimes|10。

> **注意**
>
> 遇到类似问题时，可将重试次数 trytimes 的值调整为10，防止因为网络波动导致装载失败。



## Oracle 到 dm 的数据同步异常

【问题描述】

使用 dmhs 做 Oracle 到 dm 的数据同步时，源端创建表成功，但是无法同步到目的端，报错信息如下：

```
2022-06-20 23:15:23 CPT[INFO]: 切换到日志文件(/u01/app/oracle/oradata/ORCL/redo02.log:11), seq:17!
2022-06-20 23:15:23 CPT[INFO]: SCN 1107903515定位在/u01/app/oracle/oradata/ORCL/redo02.log
2022-06-20 23:17:58 CPT[ERROR]: 事务2251937252639574包含2个建表操作:
1.create table t11(a int,b int)
2.create table t11(a int,b int)
该操作未识别
2022-06-20 23:19:02 MGR[INFO]: 来自 127.0.0.1(dmhs_console) 的连接已经断开
```

【问题分析】

由于在 Oracle 端的 sys 用户和 DMHS 用户上都执行了 ddl 的脚本，会在 sys 用户和 DMHS 用户下产生两个相同的触发器，因此在同步时会解析两次 ddl，导致出现该报错。

【问题解决】

将 DMHS 的触发器删除，重启服务，再次同步即可成功。

## DM8 到 DB2 数据同步中文乱码报错

【问题描述】

源端 DM8 和目的端 DB2 数据库字符集均为 UFT8，在进行多行更新时，如果有特殊中文，比如'龑 2','巚 2'，会有乱码报错如下：

```
DM->DB2反向同步时报错：“EXE[ERROR]: SITEID:100 RUN08170.PMZQB(UPDATE) SEQID:1266241109 
TRXID:1639730 OP_ID:4 STATE:HYC00 CODE: 30058 ROWID:76 ERR:[IBM]CWBNL0203 - cwbodmsg.dll(93084) 
codeset=ISO-8859-1CWBNL0203 - cwbodmsg.dll(93084) codeset=ISO-8859-1 
path=/opt/ibm/iaccess/mri2989/”
```

【问题分析】

根据报错提示分析是由于 DB2 字符集转换失败导致同步失败。

【问题解决】

  1. 在 DB2 端 HS 服务启动脚本中添加环境变量。



```
export DMHS_DB2=V7
export DMHS_DB2_CCSID=1208
```

  2. 修改 DM 端 dmhs.hs 配置文件。



```
<char_code>PG_UTF8</char_code>
```

## 源端 dm7 向 dm8mpp 集群装载数据报错：“建表失败”

【问题分析】

在源端 dm7 向 dm8mpp 集群装载数据时，因为源端中的表为普通表存在主键，有唯一索引，导致装载进行 create 操作时无法选择分布方式，导致装载出现问题，报错：“建表失败”。

【问题解决】

先使用 dts 迁移表结构，迁移时选择分布方式为按主键列 hash 分发，即可将表结构迁移至 mpp 集群，再使用 hs 装载数据即可成功。

## 两端列名不一致如何装载？两端字段类型不一致如何装载？

【问题描述】

DMHS 进行数据装载时，两端列名不一致如何装载？两端字段类型不一致如何装载？

【问题解决】

前提条件：

  1. 目的端表已经创建完成；
  2. 源端用户的字典已经装载完成。



具体方法如下：

进入源端 dmhs/bin 目录下执行 `./dmhs_console` 打开一个会话执行以下命令：

  1. 两端列名不一致时装载：



```
copy  0   "sch.name='模式名' and tab.name='表名'"     INSERT|EXTENT|GROUP|15|BLOCKS|30000|AS|"源列名 AS 目的端列名"
```

  2. 两端字段类型不一致时装载：(例如：源字段类型：date，目的端字段类型：varchar)



```
copy  0   "sch.name='模式名' and tab.name='表名'"    INSERT|EXTENT|GROUP|15|BLOCKS|30000|AS|"to_char(源列名,'yyyymmddhh24miss') AS 目的列名"
```

  3. 不包含任何时的装载：



```
copy  0   "sch.name='模式名' and tab.name='表名'"    INSERT|EXTENT|GROUP|15|BLOCKS|30000
```

## DMHS 源端服务自动终止报错：“[ins0]the number of dictionary tables xxx columns is changed xxx……please reload”

【问题描述】

DMHS 源端服务自动终止，重新启动也不能正常运行，检查日志发现报错：" [ins0]the number of dictionary tables xxx columns is changed xxx……please reload"。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202212111644332FEJ2XWES7MRWH4Q7U)

【问题分析】

当 DMHS 同步链路没有开启 DDL 同步时，源端表结构一旦发生变更就会导致离线字典和实际表结构存在差异，数据同步会自动停止。

【问题解决】

  1. 进入源端 dmhs/bin 目录，删除 dmhs_cpt.tmp 文件（删除该文件后会关闭 cpt 模块）；
  2. 执行命令 \`dmhs/bin/DmhsService start\` 重新启动 DMHS 服务；
  3. 执行 \`./dmhs_console\` 命令打开一个会话，使用 \`copy 0 "sch.name ='模式名' and tab.name = '表名'" DICT\` 命令，更新日志里提示存在差异的源端表离线字典；
  4. 重启 cpt 模块，恢复同步链路即可。



## oracle 到 dm 单向同步报错：“error during execution of trigger 'SYS .DMHS TRIGGER”

【问题描述】

oracle 19c 的集群架构为 rac+dg，达梦是单机，oracle 同步 ddl 到达梦时，oracle 的 rac 集群执行 switchove to 切换到 oracle 的 dg(备机) 上。oracle 的日志中报错：“error during execution of trigger 'SYS .DMHS TRIGGER”，导致切换过程要到 20 分钟左右。

【问题分析】

oracle 的 switchover to 会生成的 alter 之类的 ddl 语句，这些 ddl 语句被达梦 HS 创建的捕获 DDL 触发器捕获到了，从而导致 oracle 切换变慢。

【问题解决】

可以将捕获 DDL 的触发器禁用后，重新装载数据字典，使用系统表方式进行 DDL 同步来解决此问题。

## 达梦数据库实时同步管理平台启动后浏览器无响应

【问题描述】

达梦数据库实时同步管理平台部署采用单独 DM8 数据库作为管理平台内置库，部署完成后，数据库服务及实时同步管理平台均启动，在浏览器输入地址、用户、密码点击登录，浏览器无响应，检查后台日志并无明显报错信息。

【问题解决】

遇到此类问题需要仔细检查相关配置文件信息是否有误，修改配置信息后需重启 web 服务。

这里在检查连接内置库配置文件：`/opt/dmhs/web/webapps/manager/WEB-INF/config/connectPool.xml`
时发现在 `<Dbtype> dm7 </Dbtype>` 中 将 dm8 写成了 dm7，修改后重启 web 服务成功登录。

## dmhs 实时同步后某张表两端的数据不一致

【问题描述】

在 dmhs 实时同步的业务场景中，有时会因为主键冲突或者多对一的覆盖等问题导致某张表的数据不一致，此时最好建议重新装载问题表以确保数据一致性。

【解决办法】

重新装载问题表以确保数据一致性。具体步骤如下：

  1. 检查目的端的表是否有主键，如果有主键，可以不用清空旧数据 直接在源端装载即可。须注意的是，装载时目的端 hs 日志会提示大量的主键冲突，可能造成日志文件增大的情况，需要关注磁盘使用率。
  2. 源端登录 dmhs_console，在命令行里执行 stop cpt ,暂停实时同步，执行装载命令（不清空现有数据，直接装载）



```
copy 0 "SCH.NAME='模式名' AND TAB.NAME IN ('表名')" INSERT|THREAD|8
```

  3. 如果需要先清空数据再装载，可加上 TRUNCATE 掩码。命令如下：



```
copy 0 "SCH.NAME='模式名' AND TAB.NAME IN ('表名')" TRUNCATE|INSERT|THREAD|8
```

  4. 如果装载表数据量太大，导致报错：回滚记录过旧，则需要根据 rowid 进行分批，将装载语句合并在一个文本中，例如装载表名 PLAT.T_TEST,总数据量 502 万行，可以创建一个文本 copy.txt ,内容如下：



```
copy 0 "SCH.NAME='PLAT' AND TAB.NAME IN ('T_TEST')" INSERT|WHERE|"rowid >=1  and rowid < 1000000"
copy 0 "SCH.NAME='PLAT' AND TAB.NAME IN ('T_TEST')" INSERT|WHERE|"rowid >=1000001  and rowid < 2000000"
copy 0 "SCH.NAME='PLAT' AND TAB.NAME IN ('T_TEST')" INSERT|WHERE|"rowid >=2000001  and rowid < 3000000"
copy 0 "SCH.NAME='PLAT' AND TAB.NAME IN ('T_TEST')" INSERT|WHERE|"rowid >=3000001  and rowid < 4000000"
copy 0 "SCH.NAME='PLAT' AND TAB.NAME IN ('T_TEST')" INSERT|WHERE|"rowid >=4000001  and rowid < 5000000"
copy 0 "SCH.NAME='PLAT' AND TAB.NAME IN ('T_TEST')" INSERT|WHERE|"rowid >=5000001"
```

> **注意**
>
> 由于频繁变动的数据往往是最近的数据，所以越往后的 rowid 分组粒度尽量细一些，才能避免回滚记录过旧的问题。



文档 copy.txt 内容准备好以后，登录 dmhs_console 执行，以 @ 为前缀执行，例如：

```
DMHS> @/home/dmdba/dmhs/bin/copy.txt
```

  5. 等装载完成后，在 dmhs_console 中执行 start cpt 即可继续实时同步。



## 如何快速将 oracle 中存在 long raw 字段的表装载到 DM 表中

【问题描述】

DMHS 如何快速将 oracle 中存在 long raw 字段的表装载到 DM 表中。

【问题解决】

可在装载命令中使用 all_rows 参数,具体写法如下：

```
copy 0 "sch.name='源模式名' and tab.name='源表名'" INSERT|EXTENT|GROUP|15|BLOCKS|30000|AS|"/*+all_rows*/ 源列名 AS 目的列名"
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230116151605QU22ACMU7QPEJT33BI)

## HS 单向同步报错：”表中指定的列名在目的表中不存在导致数据装载失败“

【问题描述】

MYSQL-DM HS 单向同步报错：”表中指定的列名在目的表中不存在导致数据装载失败“。

【问题分析】

表名大小写不一致，在源端 mysql 中表的列名是小写，目的端 DM 中表的列名是大写。

【问题解决】

修改目的端配置文件 dmhs.hs 添加参数 `<case_sensitive>0</case_sensitive>`

参数说明：

case_sensitive：EXEC 模块在对比 CPT 生成的对像名和本地库的对像名时，是否需要区分大小写。
1 为敏感，不同大小写的对像名视为不同对像；0 则不敏感，视作同一对像。

## 如何将应用程序中数据库字符串中账户口令变成密文

【问题描述】

某项目要求应用程序的数据库字符串中不能出现数据库账户口令明文。

【问题解决】

DMHS 配置明文密码加密，禁止将明文密码存储在配置文件、数据库或者其他外部数据源中。

具体方法如下：

DMHS 配置如下：

```
[root@Kylin1 bin]#more dmhs.hs
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230117102943M9KPAUN0YPKV9IGYCU)

  1. 加密登录口令为密文格式



```
DMHS > ENCRYPT “PASSWORD”
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230117103143LRJ2R94LX4W22Y9DCN)

  2. 将配置文件 dmhs.hs 中明文密码替换成密文。



首先配置  \< base >  模块新增 \<pwd_encipher> 标签，含义为配置文件中的数据库登录口令是否需要加密，可选 0（否）或 1（是），其次是将标签 \<db_pwd> 明文密码用密文进行替换，这样就加密成功。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202301171036256I2ARKPW04I9C1O7RZ)

加解密说明：

加密命令：使用命令 ENCRYPT 加密 某个字符串为密文格式。

```
DMHS > ENCRYPT “SYSDBA”
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230117103943H99WFFTT5THFLVFGZ2)

当配置文件中数据库的登录信息为明文时，由于需要配置口令等敏感信息，那么就可以配置口令加密后的密文，防止登录信息被盗用。

解密命令：使用命令 ENCRYPT 解密加密生成的密文为明文格式。

```
DMHS > DECRYPT “01bb4f2a983095fe”
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2023011710415772R1M9J4Q53FVWK6IV)

当配置文件中数据库的登录口令为密文时，可以使用该命令反解出来校验密文的正确性。

## DMHS 源端无法访问 kafka 集群

【问题描述】

DMHS 源端服务器到 kafka 集群服务器网络正常，端口畅通，但是使用 dmhs 服务转发信息至 kafka 集群时超时报错：`”Expiring xx record(s) from [topic-name]:xx ms has passed since batch creation plus linger time” .`

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230215142622H0P1RKUEK3A03KN1Y8)

【问题分析】

该问题是由于 kafka 集群服务监听配置 advertised.listeners 值为域名时，导致 DMHS 源端无法通过集群 IP 访问 kafka 集群。

【问题解决】

  1. 修改 DMHS 源端服务器系统文件 /etc/hosts，把 kafka 集群配置的域名和对应服务器 IP 解析配置到本地。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230215142842SA7FRSZOH8PLPOETA3)

  2. 修改 dmhs_kafka.properties 配置文件，bootstrap.servers 也采取”域名:端口”的配置形式。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230215142911ZA7GZ5BJC3M33I3ON8)

  3. 重启 DMHS 服务。



## DM 到 DM 的 HS 双向同步存在数据回写问题

【问题描述】

达梦两套 DSC 集群做 HS 双向数据同步，在 DM1 到 DM2 使用 HS 同步过程中，DM1 源端写入一条数据，DM2 目的端同步完成这条数据后又向 DM1 源端同步这条数据，导致 HS 出现回写现象。

【问题解决】

  1. 可以在 exec 端的 dmhs.hs 配置文件中配置 level=0,可以避免数据库回写问题。
  2. 如果配置完成后还是存在回写文件，可以检查数据字典装载情况，数据字典装载不准确，也可能会导致此问题发生，可以通过重新装载数据字典解决此问题。



## MySQL 数据同步到 DM 时源端同步日志报错：[insert xxx] not support

【问题描述】

MySQL 作为 dmhs 源端进行同步时，源端 MySQL 正常向表插入数据后，源端同步日志出现如下警告信息：

```
CPT[WARN]: sql state : [insert into table_name values('xxxx')] not support
```

【问题分析】

通过日志可以看出是 insert 的类型不被支持，此时目的端并未同步警告信息里的数据，确认源端 cpt 掩码为：\<ddl_mask>op:obj\</ddl_mask>，排除掩码的问题。

该问题出现的原因可能和 MySQL 的 binlog 的模式有关系，如果 binlog 的模式不是 row 模式，可能会出现上面所述的 insert 不支持的警告信息。

【问题解决】

设置 MySQL 的 binlog 模式为 row，具体方法如下：

登录 mysql 执行如下命令可以直接将 binlog 的模式为 row：

```
SET GLOBAL binlog_format = 'ROW';
```

修改完成后会立即生效。

## 目的端自动创建索引

【问题描述】

在使用 DMHS 进行数据实时同步过程中发现目的端自动创建索引。

【问题分析】

在 EXEC 同步时，表上有无索引直接关系到同步的性能，当启用该功能以后，工作线程在操作表之前会先判断表上是否存在索引，如果没有，则自动对表前面 2000 行的数据进行采样，提取出能最大区分数每个数据的列的组合并在该组合上创建索引。

【问题解决】

如果想要取消 DMHS 自动创建索引，可调整 dmhs.hs 参数，设置 check_index  参数为 0。

## DMHS 数据装载时报错 “name 列的数据类型 sysname 在映射表中没有找到”

【问题描述】

在进行 SQLSERVER 到 DM 的 DMHS 数据装载时报错“name 列的数据类型 sysname 在映射表中没有找到”

【问题解决】

可在目的端数据库辅助表 dmhs_dtype_map 中新增插入一条数据，增加 sysname 数据类型映射，字段内容分别为如下内容：

```
src_dbtype：536928257
dest_dbtype:33554439
src_name:sysname
dest_name:varchar(128)
```

## DMHS 数据装载时报错：“字符串截断”

【问题描述】

在进行 SQLSERVER 到 DM 的 DMHS 数据装载时报错：“ cord:-70005，ERR:字符串截断，列【XX】，类型【char】，定义长度【50】，插入长度【53】”

【问题分析】

由于 SQLSERVER 中 nchar 一个汉字占用一个精度，而在 DM 中 nchar、char 一个汉字占用两个精度导致在进行数据装载时报错。

【问题解决】

手动调整目的端对应字段的精度*2。

## 在进行 DMHS 装载数据时报错：恢复模式检查失败

【问题描述】

源端为 SQLSERVER 时在进行 DMHS 数据装载时报如下错误：

```
ERROR:   数据库【库名】的恢复模式为：FULL，请全库备份并开启定时备份事物日志的维护计划，防止事物日志满。
ERROR：数据库【库名】恢复模式检查失败
```

【问题分析】

SQLSERVER 对应的 DMHS 增加了 SQLSERVER 数据库备份强制校验，在进行数据装载时必须开启备份数据库和事物日志备份维护计划。

【问题解决】

在 SQLSERVER 中配置数据库备份以及事物日志备份维护计划，具体方法如下：

  1. 连接 SQLSERVER 数据库，选择【维护计划】模块，右击选择【维护计划向导】。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230420153249PXIQ03SLBAVVYQE4PE)

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2023042015332822TBOCP4ZXRZ4FHK49)

  2. 维护计划作业命名，选择“更改”，设置备份策略。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230420153401HMG8IB8PDC7D6MDKI7)

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230420153414I3RQN4YHGSELWTN7UX)

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230420153453QE9ONRJ7EQGKI6B0HY)

  3. 选择维护任务，如进行数据库备份，则选择“备份数据库（完整）”，如进行实时日志备份，选择“备份数据库（事物日志）”，然后点击“下一步”。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230420153556V8W43DFYHV8N0VTV6A)

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230420153620LDHL77FL7FB2H5637F)

  4. 选择需要备份的数据库，及备份文件目录，点击“下一步”。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230420153705LQQI3KCZZFHCUNWZ1Y)

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2023042015373452RBML6DEII57JSX9Y)

  5. 选择报告文件生成目录，点击“下一步”



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202304201538452AIMPJQUR97I4ZRJTF)

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230420153858V4N4VWN0ADOUXDQ7PF)

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230420153924D8NJGRIPLXP0EPLLSX)

至此，SQLSERVER 维护计划设置成功，可以开始装载。

## SQLSERVER 部署 DMHS ， 在配置维护计划时报错：创建维护计划失败。

【问题描述】

SQLSERVER 部署 DMHS 时，创建维护计划失败报错：“从 IClassFactory CISID 为 [17BCA6E8-A95D-497E-B2F9-AF6AA475916F] 的 COM 组件创建实例失败，原的是出现以下错误: 00111。Mcrosoft.sqlserver.ManagedDTS)”

【问题解决】

在控制台中输入已下命令启动 DTS 服务：

```
regsvr32 "C:\Program Files (x86)\Microsoft SQL Server\100\DTS\Binn\dts.dll"
```

如果失败，使用管理员身份打开控制台，重新执行该命令。

> **注意**
>
> 引号中的路径为 SQLSERVER 软件安装目录中 dts.ddl 的位置。



## 源端 oracle 数据包卡住 agent 日志及 veri 的日志均无报错信息

【问题描述】

源端 oracle rac 环境，目的端 DSC+DW，当使用 DMHS  VERI 对比几十亿条数据或更多条数据的时候，由于网络等问题不能完全获取源端数据，veri 对比界面会卡住不再继续获取源端数据，两端的 agent 日志及 veri 日志都没有明显的报错信息。

【问题分析】

当 veri 对比卡住的时候，通过 pstack 打印 veri 堆栈信息，发现获取源端数据的时候一直在 read 读取源端数据等待返回，当登录源端数据库查询发现获取数据的会话已不存在，通过源端的 agent 日志发现会存在一个或几个线程没有完成数据获取，这时就可以使用 veri 对比的断点续比功能。

【问题解决】

断点续比也就是说 veri 对比会根据 agent 的分组参数进行分组获取源端数据，通过源端 agent 日志可以查看几个线程获取完数据并且落地，未获取完数据的线程可能是卡住了需要让这几个线程重新获取数据。通过这个方法来解决对比卡住的问题。

使用 DMHS  VERI 的断点续比功能具体的配置及命令如下：

  1. 保证 dmhs_veri_agent_ora 程序所在目录的 temp 文件夹为空，dmhs_veri 程序所在目录的 temp 文件夹为空。
  2. dmhs_veri_agent_ora 对应 agent.xml 中 extent 为 1，thr_num 必须为 0、group_num 必须大于 0。



源端 agent.xml：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230504110444IKWQKPPVCNBYWNFCM8)

目的端 agent.xml：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202305041105020IDKF9BYS6XT4F1S6B)

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230504110541SI5IDFHBNYUB8YXUB2)

  3. 第一次续比命令：



```
dmhs_veri ****  get_raw_data=0 is_hint=2 check_agent_time=60 
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230504110600ORQ8YC3VEELV8BJ8VD)

两端开始获取数据包，当源端 agent 日志提示"打印推送完毕，可以关闭时"，等待一会儿再去强杀 dmhs_veri_agent_ora，dmhs_veri 会自动退出，如果 dmhs_veri 长时间未能退出，需要强杀 dmhs_veri。

  4. 第二次续比命令：



```
)dmhs_veri ****  get_raw_data=0 is_hint=2 check_agent_time=60   continue=2
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230504110618TUMV1OYDQ2LF7JS1B2)

> **注意**
>
> dmhs_veri_agent_ora 程序所在目录的 temp 目录和 dmhs_veri 程序所在目录的 temp 目录及 dmhs_veri
> 程序所在目录的 dmhs.conf 不要改动。



## 源端 oracle 目的端 DM 如何使用一条命令装载多张表？

【问题描述】

源端数据库为 oracle，目的端数据库为 dm，当目的端的表结构与源端表结构一致时，如何使用一条命令装载多张表？

【问题解决】

可以通过以下命令来装载多张表。

```
copy  0  "sch.name='模式名' and tab.name=('表名1','表名2','表名3','表名4') INSERT|EXTENT|GROUP|15|BLOCK|3000
```

## DMHS 执行端报错：”数据过长字符串截断同步失败”

【问题描述】

DMHS 执行端报错：”数据过长字符串截断同步失败”。

【问题分析】

源端目的端表结构一致，字符集均是 utf-8，推测可能和 DMHS 对 NVARCHAR2 的处理有关，NVARCHAR2（360）正常可以存 360 个字符，但 DMHS 当作字节来处理的，通过数据库 insert 可以正常写入，但通过 DMHS 会报字符串截断。

【问题解决】

  1. 源端目的端表结构和字符集保持一致。
  2. 在 dmhs.hs 中添加 \<length_in_char>99\</length_in_char> 参数
  3. 重启 HS 同步链路，重新装载异常表。



## DMHS 将数据从源端装载至目的端后比对时两端数据质量不一致

【问题描述】

通过 DMHS 将数据从源端装载至目的端后，由于两端空格问题，导致对比结果打印两端数据质量不一致。

【问题解决】

在 veri.xml 配置文件中的 src 模块与 dest 模块，配置 \<trim_space>1\</trim_space>

参数 \<trim_space> 解释：字符串列对比时是否截断末尾空格。

## ORACLE 到 ORACLE 的 DMHS 数据装载时报错：“表空间大小不足”

【问题描述】

ORACLE 到 ORACLE 的 DMHS 数据装载时，目的端表空间大小与源端保持一致，但是装载时报错：“表空间大小不足”。

【问题分析】

源端是分区表且每个分区子表都有单独表空间，装载至目的端后，分区子表全部使用用户默认表空间,导致装载数据时报错表空间不足

【问题解决】

在源端 cpt 模块配置添加 \<ddl_with_ts>1\</ddl_with_ts> 参数，重启源端 DMHS 服务，重新进行装载。

## 如何根据源端的 rowid 及目的端 ##rowid 进行数据对比？

【问题描述】

源端数据库为 oracle ，目的端数据库为 dm ，在装载表的过程中先装载非大字段列，大字段列则是以更新的方式装载，为保证装载数据的准确性所以目的端所有表都添加了隐藏列 ##rowid 列，在没有主键的表中设置 ##rowid 为唯一索引(目的端 ##rowid 与源端 rowid 对应)，将源端的 rowid 装载到目的端的 ##rowid 中。在进行 DMHS  VERI 对比时，如何根据源端的 rowid 及目的端 ##rowid 进行数据对比？

【问题解决】

可通过以下命令来进行对比：

```
./dmhs_veri  jobfile=XXX.xml   COND_SRC="rowid='源端rowid'"  COND_DEST="##rowid='源端rowid'"  get_raw_data=0
```

## oracle 到 dm 的 DMHS 数据同步如何通过 HS 更新某列的值？

【问题描述】

源端数据库为 oracle，目的端数据库为 dm ，源端数据库表中的字段类型为 date，目的端数据库中字段类型为  varchar，在装载过程中未进行函数转换，如何通过 HS 更新某列的值 ？

【问题解决】

登录 HS 控制台执行如下命令即可：

```
copy  0  "sch.name='源端模式名'  and  tab.name='源端表名'"   update|extent|blocks|300|as|"to_char(源端列,'YYYYMMDD')  as  目的端列"
```

参数说明：

extent：分组装载时，以表数据 extent 为单位进行划分。ORACLE 数据库中的 rowid 由表 id，文件 id，数据块 id，行内编号组成，当指定 EXTENT 模式分组时，源端不需要查询表数据的 rowid，而是通过 EXTENT 的文件 id，数据块 id，计算出 rowid，再按照 rowid 进行分组。

blocks：分组装载时，指定每个分组中包含数据页的页数，该参数仅当源端为 ORACLE 类型数据库时生效。extent|blocks|300：表示的是表中数据被分割多个组进行更新，每个组读取 300 个数据页的数据。

## DMHS 数据同步如何使用 print 打印某一时间段错误的 rowid 或 sql 语句

【问题描述】

oracle 到 dm 的 DMHS 数据同步如何使用 print 打印某一时间段错误的 rowid 或 sql 语句。

【问题解决】

1.登录控制台 .`/dmhs_console`

2.通过以下命令可以打印某一时间段错误的 rowid 或 sql 语句。

```
print  "2022-08-01 08:00:00"  "2022-09-01 08:00:00"    站点号   0(打印sql语句)/1(打印rowid)   "模式名"   "表名"
```

说明：

①2022-08-01 08:00:00：表示开始时间。
②2022-09-01 08:00:00：表示结束时间。
③ 站点号：源端站点号。

执行完成后，在当前目录下会生成一个 ROWID_FILE 目录，此目录的文件中记录错误信息。

## DMHS 数据同步时 CVT 文件不支持 to_char(to_date()) 嵌套函数。

【问题描述】

源端为 ORACLE  RAC 目的端为 DSC+DW 在进行 HS 数据同步时，源端字段类型为 date 同步到目的端字段类型变为 varchar，CVT 文件不支持 to_char(to_date()) 嵌套函数。

【问题解决】

目前 cvt 文件不支持嵌套函数，可以通过触发器实现字段类型转换，目的端触发器可以参考如下写法：

```
create or replace trigger tri_stu
before insert  or update  of  aa  on stu
for each row
begin
select to_char(to_date(:NEW.aa,'yyyy-MM-dd hh24:mi:ss'),'yyyymmddhh24miss') into :NEW.aa from dual;
end;
```

## DMHS VERI 二次数据对比完成后如何根据指定的 rowid 进行数据修复

【问题描述】

源端 ORACLE RAC 目的端 DSC+DW 在使用 DMHS  VERI 二次数据对比完成后，根据两次对比结果通过 exists 整理出源端需要修复的 rowid，如何根据指定的 rowid 进行数据修复呢 ？

【问题解决】

  1. 通过两次对比在内置库生成的表中过滤出共有的 rowid，具体 sql 如下：



```
select  *   from   "第一次对比模式名"."表名"   a   where   exists( select  *   from  "第二次对比模式名"."表名"  b  where   a.VROWID=b.VROWID)
```

当查询的是源表数据时，VROWID 表示的是源端的 rowid；当查询的是目的表数据时，VROWID 表示的是目的端的 rowid。

  2. 将整理出的 rowid 整理成文本，上传到源端服务器目录下，通过如下语句进行装载修复：



```
copy  0  "sch.name='源端模式名' and  tab.name='源端表名'"  INSERT|ROWID_FILE|"rowid文件的路径"|rows|1|AS|"源端列  as  目的端列"
```

## 使用 DMHS  VERI 进行数据对比时报错："标识符过长"

【问题描述】

用 DMHS  VERI 进行数据对比时，jobfile 文件中包含函数转换(如：源端字段类型为 date，目的端类型为 varchar，需使用 to_char (源端列,'YYYYMMDDHH24MISS'))  对比过程中报错："标识符过长"。

【问题解决】

将 jobfile 文件中 \<col_map_flag> 配置为 1

```
<col_map_flag>1</col_map_flag>
```

参数说明：

列映射配置标记，有效值范围 [0-3]，默认值为 0.

0 不 进 行 列 映 射 ;

1 表 示 数 据 库 函 数 的 映 射, 例 如 "C1||'X'==SUBSTR(C1,2)";

2 表 示 DMHS 列 映 射 ， 例 如 "TO_CHAR(C1,'YYYY-MM-DD')==C1";

3 表示普通列映射，例如 "C1==D1"

## HSEM 部署数据对比时报错：“veri.conf 文件中获取 task_id、ver_ppid 失败”

【问题描述】

源端 Oracle 目的端 DM 通过 HSEM 部署数据对比时报错：“执行 job1 失败！ jobExec execute failed! 执行 VERI 失败：veri.conf 文件中获取 task_id、ver_ppid 失败，或其值与本程序值不匹配”

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230526110437YIZB4G874ROROU419V)

【问题分析】

该报错一般是由于 hs_agent 的脚本中 NEED_LIB_PATH 有缺失和组件依赖缺失导致。

【问题解决】

  1. 检查相关组件是否缺失。



```
[dmdba@dmdsc01 veri_DSC11_DSC150-DSC15]$ ldd dmhs_veri
```

如果存在相关组件未找到，需要增加相关组件，如下：libodbc.so.1 => not found。

```
[dmdba@dmdsc01 veri_DSC11_DSC150-DSC15]$ ldd dmhs_veri
linux-vdso.so.1 (0x00007ffeed04a000)
libc.so.6 => /lib64/libc.so.6 (0x00007f840e02b000)
libm.so.6 => /lib64/libm.so.6 (0x00007f840dca9000)
librt.so.1 => /lib64/librt.so.1 (0x00007f840daa0000)
libpthread.so.0 => /lib64/libpthread.so.0 (0x00007f840d880000)
libdl.so.2 => /lib64/libdl.so.2 (0x00007f840d67c000)
libdmhs_pub.so => ./libdmhs_pub.so (0x00007f840d344000)
libdmhs_xml.so => ./libdmhs_xml.so (0x00007f840d135000)
libdmhs_lic.so => ./libdmhs_lic.so (0x00007f840cf29000)
/lib64/ld-linux-x86-64.so.2 (0x00007f840e3ef000)
libodbc.so.1 => not found
```

通过软连接增加相关组件。

```
##查看数据库服务器 libodbc 相关文件的目录
[dmdba@dmdsc01 veri_DSC11_DSC150-DSC15]$ find /soft/ -name libodbc.so
/soft/dmhs/odbc/lib/libodbc.so
##根据查询返回的结果设置软连接
[dmdba@dmdsc01 veri_DSC11_DSC150-DSC15]$ ln -s /soft/dmhs/odbc/lib/libodbc.so /soft/dmhs/odbc/lib/libodbc.so.1
##添加环境变量
[dmdba@dmdsc01 veri_DSC11_DSC150-DSC15]$ vi ~/.bash_profile
export PATH=/soft/dsc/dmdbms/bin:/soft/dmhs/odbc/bin:/soft/dmhs/odbc/lib:$JAVA_HOME/bin:$PATH
[dmdba@dmdsc01 veri_DSC11_DSC150-DSC15]$ source ~/.bash_profile
```

  2. 修改 ORACLE 源端 hs_agnet/DmhsAgentServiceHsAgent 服务脚本中的环境变量。



```
NEED_LIB_PATH=/u01/app/oracle/product/11.2.0/db_1/lib:/soft/dmhs/odbc/lib
```

具体 lib 目录 $ORACLE_HOME/lib 和 unixodbc 的路径，请根据实际环境情况进行调整。

  3. 重启 hs_agent 服务，然后重新执行对比。



## 启动 veri 对比服务报错： “error while loadind shared libraries: libodbc.so.1”

【问题描述】

源端 ORACLE 目的端 DM 在进行 DMHS 数据对比时，启动 veri 对比服务报错：“error while loadind shared libraries: libodbc.so.1”: cannot open shares object file :No such file or directory"

【问题分析】

根据报错提示找不到 libodbc.so.1, 测试源端 oracle isql 可正常连接，那么该问题一般是由于环境变量问题导致，可通过在服务器上调整环境问题进行尝试解决。

【问题解决】

  1. 排查数据库服务器 libodbc 相关文件在哪个目录。



```
find  / -name libodbc.*
find / -name libodbcinst.*
```

例如：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230526111857BEKFYP1WPBGOIR65E5)

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230526111927RXQFZJDMSRNHIY7OP3)

  2. 根据查询返回的结果设置软连接，例如：



```
ln -s  /usr/lib64/libodbcinst.so.2 /usr/lib64/libodbcinst.so.1.0.0
ln -s  /usr/lib64/libodbc.so.2 /usr/lib64/libodbc.so.1.0.0
```

  3. 添加服务器环境变量。



  * 源端 oracle 用户添加环境变量：



```
vim .bash_profile
export $LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/lib64
```

  * 使环境变量生效



```
source  .bash_profile
```

## 如何确认 rowid 是否修复成功

【问题描述】

源端 Oracle 目的端 DSC+DW 通过 DMHS  VERI 二次对比完成后，根据对比结果整理出源端待修复的 rowid，如何确认这些 rowid 是否修复成功呢？

【问题解决】

1.根据两次对比结果生成的表使用 exists 关联出源端需要修复的 rowid 进行修复

2.修复完成后，可以通过指定 rowid 文件只对比修复的数据来判断是否修复成功。

可以通过  jobfile 文件指定需要检查是否已修复的 rowid 文件来进行对比。 指定 rowid 文件对比有两种情况：

  * 目的端表添加了隐藏列 ##rowid (从源端同步的 rowid）根据源端 rowid 进行对比；
  * 根据源端 rowid 查询目的端对应的 rowid，根据两端的 rowid 进行对比。



jobfile 文件具体写法如下：

```
<?xml version="1.0" encoding="GB2312" ?>
 <job>
     <name>JOB</name>
     <table_list>
 <table_info>
 <table_name>原模式.表名==目的模式.表名</table_name>
 <col_map_flag>3</col_map_flag>
 <tab_cmp_flag>0</tab_cmp_flag>
 <rowid_src>/dmhs/a.txt</rowid_src>
 <rowid_dst>/dmhs/b.txt</rowid_src>
<col_list>
<col>源端列==目的端列</col>
<col>源端列==目的端列</col>
</col_list>
</table_info>
</table_list>
</job>
```

注意：如果表字段中包含函数转换，需要将 \<col_map_flag> 设置为 1

## 使用 HSEM 进行数据对比时界面显示异常

【问题描述】

HSEM 部署在 Windows 环境上，在使用 HSEM 进行数据对比时，出现结果界面显示异常，异常截图如下：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202307211125214670TVLXYKYCNP8DOG)

【问题分析】

在使用 HSEM 进行数据对比时，web 界面显示异常，原因可能为环境变量变化导致系统命令 wmic 无法执行，从而结果文件的 txt 文件无法进行截断入内置库，导致 web 界面因内置库数据不全而显示异常。

【问题解决】

检查 windows 系统环境变量 PATH 中是否配置了 ```%SystemRoot%`\System32\`Wbem`，且需要将 ``%SystemRoot%`\System32\` 和 `%SystemRoot%` 及 ```%SystemRoot%`\System32\`Wbem` 按顺序配置在前 3 位，如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202307211134024VZ5TL68BXCECXPHCT)

## ORACEL 到 DM 的数据同步在对目的端大字段列进行更新时报错："回滚版本记录过旧"

【问题解决】

可以通过如下命令进行解决：

```
copy  0  "sch.name='模式名'  and  tab.name='表名'"  update|blob|nolock|rowid_file|"rowid_file文件路径"|AS|"U  AS  U1"
```

参数说明：

blob：该掩码必须和 UPDATE 掩码组合使用，源端表中的大对象数据去更新目标库中对应的大对象，该功能是用来修复目标库大对象和源端不一致的方法，可以指定条件抽取源端某个表某些行的大对象更新到目标库中。

nolock：查询之前将不再对表上 S 锁，可以减少对应用的影响，也可以使用在普通上锁模式总不成功的情况，但是这种方式可能导致装载到目的端数据跟源端产生不一致的现象，请慎用。

## ORACEL 到 DM 的数据同步过程中如何检查源端装载表的数据字典否存在

【问题解决】

  1. 登录源端库，通过以下 SQL 检查对应表的 ID。



```
select  *  from  sys.obj$   where  type#='表名';
```

  2. 根据以上 sql 获取源端表的 ID 后，在 HS 的 DICT 目录中查找对应的字典是否存在。



DICT 目录：用来保存同步表的离线字典文件，存在于 DMHS 源端。在使用 COPY 命令进行表字典或者数据装载时，DMHS 会在程序当前目录下自动生成 DICT 目录，并将装载的字典文件保存在其中。

## Oracle 到 DM 数据同步如何将同步到目的端的表名转换为大写？

【问题描述】

Oracle 到 DM 的数据同步，源端表名为小写，如何将同步到目的端的表名转换为大写？目的端 DM 大小写敏感。

【问题解决】

可以参考如下两种方法来处理该问题。

方法一：修改源端 dmhs.hs 配置文件，将源端小写表名映射到目的端为大写表名。

例如，如下将源端表 “ceshi3” 映射到目的端为 “CESHI3”。

  1. 为了保护数据这里可以配置白名单。修改源端 dmhs.hs 配置文件，在  中修改 item 项写法如：HN_TEST.HN_ORA.ceshi3
  2. 修改映射关系，在中修改 item 项中的写法，将 HN_ORA 用户下的 ceshi3 小写表名映射到 DMHS 目的端 SCOTT 模式下的 CESHI3 大写表名如：HN_TEST.HN_ORA.ceshi3==SCOTT.CESHI3。
  3. 重启 DMHS 服务。
  4. 停止 CPT 服务，执行如下 load 操作，会在目的端创建一个大写表名的表 “CESHI3”。



```
load 0 "SCH.NAME='HN_ORA' AND TAB.NAME='ceshi3'" CREATE|INSERT|PDB|"HN_TEST";
```

  5. 启动 CPT 服务，开始同步。



方法二：当需要将源端新创建的表同步到目的端为大写表名时，可在目的端 EXEC 配置模块中添加 \<toggle_case>2\</toggle_case> 掩码，当源端创建小写的表名时，同步到目的端自动转换为大写表名。

参数 \<toggle_case> 说明：EXEC 模块在映射 CPT 生成的对象名到本地时，是否需要把对象名转换为大小写。0 则是保持大小写不变，1 则是转换为小写，2 表示转换为大写。

## 配置过滤规则后，DMHS 启动报错

【问题描述】

在配置 dmhs 的列过滤规则后，启动 dmhs 报错：

```
mxml: Bare < in element!
MGR[ERROR]: cannot open or find configuration file :dmhs.hs
MGR[ERROR]: unable to load configuration
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2023092109245515G03IETRR9EQXDIA6)

【问题分析】

这是由于在配置文件中使用了小于符号（\<）,小于符号需要转义。

【问题解决】

以下提供两种方法修改过滤规则处理该问题，如下：dmhs 同步时仅同步 TEST1 表中 COL1 大于 100 小于 200 的数据。

方式一：使用大于符号。

```
<item>TEST.TEST1(COL1>'100' and '200'>COL1)</item>
```

方式二：转义小于符号。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230921092959PQI96MQKSAEO6JQV8N)

## DMHS 源端初始化用户时报错：PUB[ERROR]:获取客户端字符集失败

【问题描述】

DMHS 通过备份恢复的方式进行 HS 链路重建。完成备份恢复后，源端还需要初始化需同步的数据库用户，初始化用户时报错：PUB[ERROR]:获取客户端字符集失败。

【问题分析】

在通过 XSHELL 等工具查看 HS 日志时，经常会出现字符乱码的情况，此时通过设置编码 export LANG=C 可有效避免乱码情况。但在该客户端编码环境下初始化用户可能会出现上述报错。

【问题解决】

可以通过以下两种方法来避免该报错：

  1. 由 dmdba 用户退回到 root 用户，然后再登录 dmdba 用户重新初始化用户；
  2. 根据实际情况设置 export LANG=zh_CN.GB18030 再初始化用户。



## Oracle 到 DM 数据同步目的端无数据

【问题描述】

Oracle 到 DM 的数据同步，需要同步的表存放在 Oracle 的 PDB 库中，已经完成表的初始装载，在执行 start cpt 后源端插入数据，日志无报错信息且目的端控制台执行 lag 没有数据，目的端数据库查询不到源端需要同步的数据。

【问题分析】

检查源端和目的端日志不存在报错信息，链路统计信息无实时数据同步消息，检查源端配置文件 dmhs.xml 发现 标签中白名单配置有误。

源端配置文件如下：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231027101416ODRR9S7CW0L7L33HTV)

【问题解决】

  1. 停止 DMHS 同步；



源端进入 DMHS 控制台关闭 CPT ，并关闭 HS 服务。

```
./dmhs_console
stop cpt
exit
./DmhsService stop
```

  2. 修改源端配置文件 dmhs.xml 中  标签中白名单配置；



```
<filter>
<enable>
<item>库名.模式名.*</item>
</enable>
</filter>
```

  3. 源端启动 DMHS 服务，重新装载需要同步的表；



```
##启动DMSH服务
./DmhsService start 
##登录DMHS控制台工具，重新装载需要同步的库
./dmhs_console

COPY 0 “SCH.NAME=’模式名’” DROP|CREATE|INSERT|PDB|”库名”
```

  4. 装载完成后检查源端及目的端日志是否存在报错及同步对象和数据量是否一致；
  5. 装载确认完成后源端开启数据同步；



```
##登录DMHS控制台工具，启动 CPT 开启同步
./dmhs_console
start cpt
```

  6. 源端插入数据测试目的端是否同步成功。



## DMHS 目的端同步长时间打印“CHECKPOINT WAIT SITEID:XXXX，TRXID: XXXX”

【问题描述】

DMHS 目的端同步长时间打印 “CHECKPOINT WAIT SITEID:XXXX，TRXID: XXXX”，并打印出相应表名。如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202312221423372BH8EUIU1H7X5NAO3D)

【问题分析】

一般这种情况分为两种情况：

  1. 源端对相应表进行大批量操作，导致数据同步缓慢；
  2. 源端数据库对相应表的操作长时间未提交，导致相应 CHECKPOINT 不推进。



【问题解决】

  1. 针对源端相应表进行大批量操作，导致数据同步缓慢的情况，通过如下 SQL 定位相应会话。



```
SELECT * FROM V$SESSIONS WHERE TRX_ID=目的端DMHS日志打印的TRXID
```

一般该种情况为对数据量过大的表进行更新或删除操作，且表没有唯一性索引或主键。观察会话状态是否为 ACTIVE(STATE)，根据 clnt_ip 和 appname 定位相应执行程序，通知相关应用人员是否可以终止，尝试使用 `call sp_close_session('查询出的sess_id')` 进行会话强杀。若能强杀成功，通知应用进行相关流程优化，对表建立主键、分批执行相关操作等；若无法进行强杀，建议对源端 dmhs.xml 添加黑名单，并重启源端 dmhs 服务。该处需注意 dmhs 实际同步到哪个 lsn，定位到哪个 lsn，通过 exec siteid 命令获取，如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231222142728ZRFF2KXFQC1ZDMN61B)

  2. 针对源端数据库相应表未提交的情况，需找出对应未提交的 SQL 通过如下 SQL 定位相关长时间未提交 SQL。



```
--执行未提交的SQL
 select
        t1.sql_text,
        t1.state   ,
        t1.trx_id,
        t2.ins_cnt,
        t2.upd_cnt,
        t2.del_cnt,
        t2.upd_ins_cnt 
from
        v$sessions t1,
        v$trx t2
where
        t1.trx_id=t2.id
    and t1.state ='IDLE'
    and t2.status='ACTIVE'
and (ins_cnt>0 or upd_cnt>0 or del_cnt>0 or upd_ins_cnt>0);
--通过上述获取的trx_id在v$lock中相印证，确定是否为目的端日志打印的相关表被上“IX”锁
select * from v$lock t1,sysobjects t2 where t1.table_id=t2.id;
```

根据 clnt_ip 和 appname 定位相应执行程序，通知相关应用人员是否可以终止，尝试使用 `call sp_close_session('查询出的sess_id')` 进行会话强杀。

## DMHS 目的端日志提示：“收到来自 xx.xx.xx.xx 的连接请求，xxxxx，这次请求被关闭”

【问题描述】

DMHS 目的端日志提示：“收到来自 xx.xx.xx.xx 的连接请求，但是这个源站点相应的接收线程仍在工作，这次请求被关闭”。如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231222145906HJEUV5TXA8R0YYT1PC)

【问题分析】

DMHS 数据同步工具目的端执行模块（EXEC）使用多线程方式进行入库，总线程数由配置文件中 \<exec_thr> 进行控制，一般设置为 32。该种问题一般为 DMHS 目的端同步线程耗尽，无法对源端发送过来的连接创建接收线程。分为两种情况：

  1. 存在同步慢的 SQL，导致线程对同一张表操作互锁，耗尽线程。由于对于同一张表，若存在一个线程对表操作缓慢的情况，后续陆续从源端发送过来的对于该表的数据入库需求，DMHS 多线程都会自动进行上锁的状态，等待前一个线程执行完成后，后一个线程才可解锁进行入库。若目的端所有同步线程对于该表上锁陷入等待，则会返回报错；
  2. 目的端存在未提交的 SQL，导致相关表阻塞，耗尽 dmhs 同步线程。



【问题解决】

  1. 针对第一种情况，可通过 dmhs_console 连接数据库，使用 thr 命令，打印相关线程状态。下图为某一时刻数据同步卡住时通过 thr 打印线程运行情况：



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231222150138HBYNNPF2HWTGI3PKVI)

可以通过图片看到线程处于 lock tab 状态，可以通过 thr 具体线程号，打印线程状态为 executio 的具体执行 SQL。一般情况下为目标库某个表存在大量的记录，表上没有唯一性索引或主键，却需要同步该表的 UPDATE 或 DELETE 操作。由于表没有主键或唯一性索引，dmhs 为保证源端和目的端数据一致性，会对表所有列进行全表匹配，导致数据同步速度急剧下降。下图为 dmhs 数据同步目的端对于一个无主键表进行删除数据时执行的具体 SQL 截图：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231222150251O6ZQFFOY8LPOFAPREV)

该种情况，快速恢复的方法为对源端 dmhs.xml 添加黑名单，并重启源端 dmhs 服务，暂时屏蔽该表数据同步。要想彻底解决还是需要对相关表添加主键或者唯一性索引。

  2. 针对情况第二种情况，可通过查询 V$TRXWAIT 表，定位相关阻塞源头，相关 SQL 如下所示：



```
--判断是否存在阻塞
SELECT * FROM V$TRXWAIT;
--通过WAIT_FOR_ID定位阻塞源头
select * from v$sessions where trx_id=查出来的wait_for_id
```

根据 clnt_ip 和 appname 定位相应执行程序，通知相关应用人员是否可以终止，或者尝试使用 `call sp_close_session('查询出的sess_id') ` 进行会话强杀。

## dmhs 初始化脚本如何在 disql 中运行

【问题描述】

hs 的脚本中触发器创建语句中有 & 符号，此 & 在脚本中为逻辑运算符，disql 工具默认的定义变量符号也为 &，发生冲突，dmhs 初始化脚本如何在 disql 中运行。

【问题解决】

在执行脚本前将定义变量功能禁用。

```
--禁用定义变量功能
SQL> set define off;
--dmhs初始化脚本
SQL> start /home/dmdba/ddl_sql_dm8.sql
```

## Oracle 到 DM 数据同步在线装载数据时提示“回滚段版本过旧”

【问题解决】

在装载数据时加上分组掩码 group 和 extent、blocks。

```
insert|thread|32|extent|group|15|blocks|10000
```

参数说明：

extent：分组装载时，以表数据 extent 为单位进行划分。该参数仅对源端 ORACLE 有效。

group：分组装载功能掩码，后面需要紧跟分组的数字掩码，在装载上亿行的大表时使用该掩码可以显著提高性能。

blocks:分组装载时，指定每个分组中包含数据页的页数，该参数仅当源端为 ORACLE 类型数据库时生效。当装载表不包含 lob 字段时，该值默认为 10000；当表包含 lob 字段时，该值默认为 100。

## 如何打印绑定变量的具体变量值

【问题描述】

dmhs 目的端同步日志报错影响行数不一致，如何打印绑定变量的具体变量值，准确定位同步数据的问题。

【问题解决】

dmhs 默认会将同步报错记录保存至 bin 目录下的 trx 目录下，文件名以 blb 结尾，每个大小默认为 1G 左右；以确保出现问题后可以进行问题溯源，具体操作步骤如下：

```
--进入dmhs安装目录bin目录下
./dmhs_console
--新版本默认会连接dmhs服务，老版本需使用connect命令进行连接
connect dmhs服务所在ip：dmhs的管理端口号
--通过print命令打印相应报错SQL的具体绑定变量数据，其中siteid seqid trxid根据报错日志中记录进行填入
print siteid seqid trxid 
--执行完成后会在bin目录生成msg_parsed_sql.sql文件，可以查看具体报错SQL
```

## 目标端 start exec 报错：“please set constraint and trigger on cpt mudule to 0”

【问题描述】

Oracle 作为目标端时 start exec 失败，查看目标端日志报错如下：

```
ERR:[Oracle][ODBC][Ora]ORA-01031: insufficient privileges
EXE[ERROR]: rdbms exec dbms_xstream_gg.set_gg_session() failed, please set constraint and trigger on cpt mudule to 0
```

【问题分析】

通过日志“insufficient privileges”和“rdbms exec dbms_xstream_gg.set_gg_session() failed, please set constraint and trigger on cpt mudule to 0”可知目标端没有调用权限，CPT 模块中 constraint 和 trigger 参数需要置为 0。

【问题解决】

检查源端的 CPT 标签中的  和  参数是否为 1，若为 1 则改成 0。

参数说明：

trigger：指示下一级数据入库时，是否需要禁用触发器，该配置参数只针对下一级的执行模块数据库为 DM 系列时有效，执行模块对数据入库时，如果表上存在触发器，那入库时会就会触发这些触发器，造成不可预期的结果。

constraint：指示下一级数据入库时，是否需要禁用约束，该配置参数只针对下一级的执行模块数据库为 DM 系列才有效，执行模块对数据入库时，如果表上存在约束，那入库时会就会触发这些约束检查，假如是外键约束，则会造成数据入库先后顺序的严格要求，从而降低数据入库的性能。

## 进行 kafka 同步时，启动 start_dmhs_kafka.sh 报错

【问题描述】

DMHS 进行 kafka 同步时，启动 start_dmhs_kafka.sh 出现如下报错：

Error: Could not find or load main class com.dameng.dmhs.dmga.service.impl.ExecDMHSKafkaService。

【问题解决】

可通过如下两个方面来排查出现报错的原因：

  1. 检查 hs bin 目录下是否存在 dmga-dmhs-kafka-service.jar。



```
[root@hadoop01 ~]# cd /opt/dmhs/bin
[root@hadoop01 bin]# ll *.jar
-rwxr-xr-x. 1 root root  19915 Dec  3 12:16 dmga-dmhs-kafka-service.jar
-rwxr-xr-x. 1 root root 671701 Dec  3 12:16 fastjson-1.2.83.jar
[root@hadoop01 bin]#

```

  2. 检查环境变量。



```
[root@hadoop01 bin]# cat ~/.bash_profile
export DMHS_HOME=/opt/dmhs
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/opt/dmhs/bin
```

## Oracle19c 同步到 DM 源端 hs 实例日志告警："无法定位对象 xxxxxx 的 DDL 事件"

【问题描述】

在 Oracle19C 实时同步数据到 dm8 正常运行了一段时间后，dm 目的端 hs 实例日志中报错 ERROR：”操作语句中 WHERE 条件后面没找到主键列“，Oracle 源端 hs 实例日志无报错但告警：”无法定位对象 xxxxxx 的 DDL 事件“。

【问题分析】

Oracle19C 默认日志中缺少支持逻辑复制新数据类型和操作所需的补充日志记录。

【问题解决】

当源端是 Oracle19C 时，源端开启 ENABLE_GOLDENGATE_REPLTCATION 参数可以解决此问题。

开启该参数的命令为：

```
alter system set enable_goldengate_repltcation=true scope=both;
```

参数说明：

ENABLE_GOLDENGATE_REPLTCATION 是一个用于启用或禁用 GoldenGate 数据复制功能的参数。该参数主要控制了支持逻辑复制新数据类型和操作所需的补充日志记录。

## 未找到动态库文件 path: libdrekafka.so

【问题描述】

配置 DM-kafka 的 DRS，目的端启动 DRS 服务时报错："未找到动态库文件 path: libdrekafka.so"。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2024080811221044TGKFE82QNNVUWG5T)

【问题分析】

查询 libdrekafka.so 依赖的动态库发现是由于缺少 libsasl2.so 动态库文件导致。查询方法如下：

```
[dmdba@x86_1_59 bin]$ ldd libdrekafka.so
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202408081125459GEAMJOIUY7UWOMJAX)

【问题解决】

查找到 libsasl2.so 文件后 cp 到 DRS 安装路径 bin 目录下，再次查询 libdrekafka.so 依赖的动态库，确认不缺少动态库即可。

```
[root@x86_1_59 ~]# find / -name libsasl2*
```

## DMHS 源端报错：“检测到 DDL 同步辅助表有缺失，请重建”

【问题描述】

启动 DMHS 源端 CPT 时（源端为 DM8）报错：“ CPT[ERROR]: 配置了 DDL 同步，但是检测到 DDL 同步辅助表有缺失，请重建，或者配置 DDL MASK 为 0”。

【问题分析】

在首次启动 DMHS 前，按照 ddl_sal_dm8.sql 脚本自动创建相关的表及触发器等，但在执行脚本过程中某个触发器创建失败。通过手动执行该脚本发现在创建 DMHS_DDL_TRIGGER_AFTER 触发器时报错：“-2007：第 17 行，第 17 列[IS]附近出现错误，语法分析出错”，但该脚本在其他环境上创建并无语法错误。通过分析 17 行前后的代码，发现引用到 TYPE 关键字，又在 dm.ini 中发现有 EXCLUDE_RESERVED_WORDS=TYPE,XX,XX。最终定位问题原因：在 dm.ini 中屏蔽了 TYPE 关键字，导致触发器中使用的 TYPE 语法出错。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240823105804C4QKO4FSM59SDH75QD)

【问题解决】

取消关键字屏蔽后重新创建触发器。

## DM 同步到 KAFKA ，目的端 HS 服务报错：“Invalid JSON format message”

【问题描述】

DM 同步到 KAFKA 时，启动目的端的 HS 服务时报错：“Invalid JSON format message”，如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2024090409365143J2PRK2OLQXGTPORO)

【问题解决】

该问题为 json 格式校验失败导致的异常，可通过修改 properties 文件中的参数 json.format.check=0，不校验 json 格式来解决，如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240904093835KM5ZOOAH8FZ9HWT0LM)

## DMHS 源端到目的端同步缓慢

【问题描述】

达梦数据库作为源端，DMHS 配置好之后，源端到目的端同步缓慢，需要几分钟才能同步过去，同时检查源端 CPT 状态，发现归档读取时间，几分钟都不更新。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202409261551034XU0QKKJK0GML0VTEW)

【问题分析】

登录数据库执行如下语句发现，归档合并刷盘缓存大小为 2，表示开启归档合并刷盘，归档合并刷盘缓存大小为 2M，由于归档达到 2M 之后才刷盘，导致 DMHS 源端读取归档慢。

```
select ARCH_FLUSH_BUF_SIZE from V$DM_ARCH_INI;
```

【问题解决】

在 dmarch.ini 中将 ARCH_FLUSH_BUF_SIZE 设置为 0，如果没有此配置，添加此配置即可，添加完成后，重启数据库后再次检查同步速度是否正常。

## DMHS 装载自增列报错

【问题描述】

DMHS 装载目的端报错：“仅当指定列列表，且 SET IDENTITY_INSERT 为 ON 时，才能对自增列赋值”。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202410170929246UOJ8N1XC5HND08U8R)

【问题分析】

该错误发生在 DMHS 源端表中包含自增列时，目的端数据在入库时，无法对自增列进行赋值，此时源端日志无报错，目的端数据入库失败。

【问题解决】

在源端 dmhs.hs 配置文件 send 模块下添加 `<identity>1</identity>`，该参数用来实现包含自增列的表的处理方式，配置完成后重启 dmhs 链路生效。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202410170942331BWPL3SUAUFD25DBJ3)

identity 参数配置为 1 的意思为对自增列的处理方式使用 cpt 模块带来的值进行填充，即目的端数据和源端数据保持一致。

## DRS 删除代理报错：“当前选择的代理不可卸载”

【问题描述】

在 DFM 界面删除代理服务时报错：“当前选择的代理不可卸载”。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20241108100038NXFLXORDT1PA36D2JW)

【问题分析】

当待删除的代理服务所在服务器的连接信息填写有误时，平台界面无法连接到服务器删除代理会失败。

【问题解决】

在 DFM 界面的代理管理模块，勾选中待删除的代理服务然后点击“编辑”将此代理所在服务器的连接信息填写正确后，可成功删除代理服务。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20241108100252RNS88FRPGGQXT6CDFF)

## 如何在线替换 dmhs 授权

【问题描述】

如何不停机替换 dmhs 授权。

【问题解决】

示例如下：

源端与目的端都要更换授权，不需要重启 hs 服务。

  1. 将授权文件上传至服务器，改名为 dmhs.key。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20241121145225VPNTNB3ISBANK16FYN)

  2. 更改新 dmhs.key 文件的属主属组为 dmdba 的，并赋予 755 权限。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20241121145304JS7KEFAD392ULWC5NT)

  3. 切换到 dmdba 用户，ps -ef|grep dmhs 看一下 dmhs 的实例服务进程，在进程中找到 dmhs.hs 文件所在的目录，将该目录下原来的 dmhs.key 文件做一个备份，之后把新的 dmhs.key 文件拷贝到该目录。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20241121145340XRHD50E1G6CG1HK5JY)

  4. 在该目录下执行./dmhs_console 进入控制台，执行 lic 命令查看当前授权到期时间。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20241121145420J8IUS16OLDUQGLUFLE)

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202411211454556HZ1OT018M96PO2PXD)

  5. 执行 upgrade 命令使授权生效。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20241121145531UPYE6IX1KUUNV1HME8)

  6. 执行 lic 命令查看授权是否生效。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20241121145622SK9BDVFMGU29JXKVNV)

  7. 确认授权生效后执行 exit 命令退出控制台即可，授权更换成功。


