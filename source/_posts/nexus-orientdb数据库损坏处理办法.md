---
title: nexus orientdb数据库损坏处理办法
tags: nexus,question
originContent: ''
categories: [运维, 持续集成,nexus]
toc: false
---

## 问题记录
过了一个周末过来，jenkins无法构建npm项目了，提示服务器500错误，登陆服务器查看日志后发现是orientdb数据库损坏了，提示需要导出到json后再次导入
	
## 问题如下
```
npm ERR! code E500
npm ERR! 500 javax.servlet.ServletException: com.orientechnologies.orient.core.exception.OPageIsBrokenException: Following files and pages are detected to be broken ['assetdownloadcount_node_id_repository_name_asset_name_date_type_date_idx.sbt' :136;], storage is switched to 'read only' mode. Any modification operations are prohibited. To restore database and make it fully operational you may export and import database to and from JSON.??    DB name="component": @babel/runtime@7.7.7

```
## 解决办法
解决办法为通过容器控制台进入容器中，找到orientdb控制台
然后进行数据库的导入导出

## 实际操作
```
#打开nexus目录
 cd /opt/sonatype/nexus/
#打开orient数据库控制台
java -jar ./lib/support/nexus-orient-console.jar
#界面输出
OrientDB console v.2.2.36 (build d3beb772c02098ceaea89779a7afd4b7305d3788, branch 2.2.x) https://www.orientdb.com
Type 'help' to display all the supported commands.
orientdb>

#连接数据库
CONNECT PLOCAL:/nexus-data/db/component admin admin
#导出数据库
EXPORT DATABASE ./export-demo.export
#删除数据库 
drop database
#创建新的component数据库
create database PLOCAL:/nexus-data/db/component
#再次连接
CONNECT PLOCAL:/nexus-data/db/component admin admin
#导入数据库
import DATABASE ./export-demo.export.gz
#重启nexus
```

## 参考以下链接
[Nexus3 OrientDB Console使用](https://www.jianshu.com/p/999593fd7eb9)
[OrientDB教程](https://www.w3cschool.cn/orientdb/orientdb-mx931xi1.html)

