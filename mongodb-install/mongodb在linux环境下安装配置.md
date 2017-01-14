
###[回到首页](https://github.com/xianglingchuan/pitfalls-back-end)
#MongDB在linux服务端配置安装以及数据备份
====

**描述：**主要解在MongDB在linux下面的安装配置及数据备份

**原因：**因为数据日志增长过快，用mysql存储空间站用太多，所以需要换成NoSql形式的数据库存储。

**方法：**

###1、安装mongodb<br/>
下载相应的mongodb源码包，解压以后放入相应的目录即可。<BR>
目前下载的版本为3.3.12，解压以后存放的目录为/usr/local/mongodb/mongodb3.3.12   
 

 
###2、相关命令和配置文件：<br/>
####1) 启动命令
   没有配置文件的启动方式<BR>
   /usr/local/mongodb/mongodb3.3.12/bin/mongod  --dbpath /usr/local/kencery/mongodb/mongodb3.3.12/db
   有统一配置文件的方式<BR>
   /usr/local/mongodb/mongodb3.3.12/bin/mongod -f /usr/local/mongodb/mongodb3.3.12/bin/mongodb.conf
   验证是否启动成功<BR>
   lsof -i :27017  //查看是否存在该端口的进程

####2) 重启命令
   /usr/local/mongodb/mongodb3.3.12/bin/mongod --config /usr/local/mongodb/mongodb3.3.12/bin/mongodb.conf

####3) 关闭命令
#####方法一:
   /usr/local/mongodb/mongodb3.3.12/bin/mongod --shutdown  --config /usr/local/mongodb/mongodb3.3.12/bin/mongodb.conf

#####方法二:
   查看端口是否在使用中: lsof -i :27017<BR>
   找到相关mongod的PID值执行:<BR>
   kill -9 PID<BR>

####4) 进入mongodb 命令环境
    /usr/local/mongodb/mongodb3.3.12/bin/mongo

####5) mongodb.conf配置文件  
```java 
        数据文件存放目录
        dbpath=/usr/local/mongoDB/mongodb/db 
        #日志文件存放目录
        logpath=/usr/local/mongoDB/mongodb/logs/mongodb.log
        #端口号
        port=27017
        #以守护程序的方式启用，即在后台运行
        fork=true
        #绑定的ip地址
        bind_ip=0.0.0.0
        #关闭http接口，默认关闭27018端口访问
        #nohttpinterface=true
        #声明这是一个集群的分片，默认端口27018
        #directoryerdb=true
        #开启认证
        #auth=true
        #设开启简单的rest API,置后打开28017网页端口
        #rest=true
``` 
详细介绍请见http://blog.csdn.net/fdipzone/article/details/7442162




###3、用户管理及开启权限验证.<br/>
用户管理需要做深入的了解<BR><BR>
//创建普通用户<BR>
```java 
use admin;
db.createUser({user: "user01",pwd: "admin",roles: [ { role: "userAdminAnyDatabase", db: "admin"}]}); 
```

//创建超级管理员用户<BR>
```java 
use admin;
db.createUser({user: "superadmin",pwd: "superadmin",roles: [ { role: "root", db: "admin"}]}); 
```
开始权限验证 <BR>
在MongoDB.conf配置文件中将auth=true前面的#去掉即可。<BR>



###4、导出及备份操操作.<br/>

MongoDB数据导入与导出 
#####导出工具：mongoexport 
######1)、概念： 
mongoDB中的mongoexport工具可以把一个collection导出成JSON格式或CSV格式的文件。<BR>
可以通过参数指定导出的数据项，也可以根据指定的条件导出数据。 
######2)、语法： 
mongoexport -d dbname -c collectionname -o file –type json/csv -f field <BR>
参数说明： <BR>
-d：数据库名 <BR>
-c：collection名 <BR>
-o：输出的文件名 <BR>
–type ： 输出的格式，默认为json <BR>
-f：输出的字段，如果-type为csv，则需要加上-f “字段名” <BR>
######3)、示例：
sudo mongoexport -d bank -c Student -o /chroot/bak/mongodbdump/Student.json --type json -f  "_id,name,sex,age"


#####数据导入：mongoimport 
######1)、语法： 
mongoimport -d dbname -c collectionname –file filename –headerline –type json/csv -f field <BR>
参数说明： <BR>
-d ：数据库名 <BR>
-c ：collection名 <BR>
–type ：导入的格式默认json <BR>
-f ：导入的字段名 <BR>
–headerline ：如果导入的格式是csv，则可以使用第一行的标题作为导入的字段 <BR>
–file ：要导入的文件 <BR>
######2)、示例：
sudo mongoimport -d bank -c Student --file /chroot/bak/mongodump/Student.json --type json<BR>



MongoDB备份与恢复 
#####MongoDB数据库备份 
######1)、语法： 
`
mongodump -h dbhost -d dbname -o dbdirectory <BR>
参数说明： <BR>
-h： MongDB所在服务器地址，例如：127.0.0.1，当然也可以指定端口号：127.0.0.1:27017 <BR>
-d： 需要备份的数据库实例，例如：test <BR>
-o： 备份的数据存放位置，例如：/chroot/bak/mongodump/，当然该目录需要提前建立，这个目录里面存放该数据库实例的备份数据。 
`
######2)、实例：
`
        sudo rm -rf   /chroot/bak/mongodump/
        sudo mkdir -p /chroot/bak/mongodump/
        sudo mongodump -h 127.0.0.1:27017 -d bank -o /chroot/bak/mongodump/
`



#####MongoDB数据库恢复 
######1)、语法： 
mongorestore -h dbhost -d dbname –dir dbdirectory
`
    参数或名：
        -h： MongoDB所在服务器地址
        -d： 需要恢复的数据库实例，例如：test，当然这个名称也可以和备份时候的不一样，比如test2
        --dir： 备份数据所在位置，例如：/home/mongodump/itcast/
        --drop： 恢复的时候，先删除当前数据，然后恢复备份的数据。就是说，恢复后，备份后添加修改的数据都会被删除，慎用！
`
######2)、实例：
    mongorestore -h 127.0.0.1:27017 -d bank --dir /chroot/bak/mongodbdump/bank/






###5、开机自启动及数据库自动备份.<br/>
######1) 设置开机自动启动mongodb
`
  vi /etc/rc.d/rc.local 
  /usr/local/mongoDB/mongodb/bin/mongod --config /usr/local/mongoDB/mongodb/bin/mongodb.conf
`
######2) 数据库自动备份脚本 
shell脚本内容：<BR>

`
  #!/bin/sh
  date=`date +"%Y%m%d"`
  mkdir -p /data/data/backupMongoDB/$date
  mongodump -h 127.0.0.1:27017 -u user -p password -o /data/data/backupMongoDB/$date
`


安装mongodb-Linux-x86_64-3.4.1.tgz版本的时候，执行mongodump命令导入单个数据表时出现错误，但一直没有找到其原因。c 
提示错误如下： <BR>
[root@iZ23l37x16xZ bin]# mongodump -d admin -u superuser -p mongodb@2016 <BR>
2016-12-29T14:58:53.044+0800 Failed: error dumping metadata: error converting index (): conversion of BSON value ‘2’ of type ‘bson.Decimal128’ not supported <BR>
最后的解决方法是更新版本到3.3.12版本即可以导出。<BR>
