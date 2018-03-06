---
layout:     post
title:      "Mongodb     教程"
subtitle:   "Mongodb Tutorial"
date:       2018-02-11 18:00:00
author:     "LiuQhahah"
header-img: "img/post-bg-mongo.png"
header-mask: 0.3
catalog:    true
tags:
    - NoSQL
    - MongoDB
    - 数据库
---
 
>  本[教程](https://docs.mongodb.com/manual/mongo/) 以ubuntu为例

---------------------------------------



##启动mongo
	
先关闭mongodb ,再start
	
	liu@liu:~/mymongo$ sudo service mongod stop
	liu@liu:~/mymongo$ sudo service mongod start
	liu@liu:~/mymongo$ mongo
	MongoDB shell version v3.6.2
	connecting to: mongodb://127.0.0.1:27017
	MongoDB server version: 3.6.2
	Server has startup warnings: 
	2018-02-27T22:51:15.767+0800 I STORAGE  [initandlisten] 
	2018-02-27T22:51:15.767+0800 I STORAGE  [initandlisten] ** WARNING: Using the XFS filesystem is strongly recommended with the WiredTiger storage engine
	2018-02-27T22:51:15.767+0800 I STORAGE  [initandlisten] **          See http://dochub.mongodb.org/core/prodnotes-filesystem
	2018-02-27T22:51:16.612+0800 I CONTROL  [initandlisten] 
	2018-02-27T22:51:16.612+0800 I CONTROL  [initandlisten] ** WARNING: Access control is not enabled for the database.
	2018-02-27T22:51:16.612+0800 I CONTROL  [initandlisten] **          Read and write access to data and configuration is unrestricted.
	2018-02-27T22:51:16.612+0800 I CONTROL  [initandlisten] 
	> exit
	bye
	liu@liu:~/mymongo$ 

在Termial敲入运行mongodb
	
	liu@liu:~/mymongo$ sudo service mongod start &
	[1] 15675

查找mongo安装目录

	liu@liu:~/mymongo$ which mongo
	/usr/bin/mongo

停止运行mongoDB

	
网上下载json数据

	liu@liu:~/mymongo$ wget https://raw.githubusercontent.com/mongodb/docs-assets/primer-dataset/primer-dataset.json

下载完成：
	
	liu@liu:~/mymongo$ ls
	primer-dataset.json
	
导入失败：
	
	liu@liu:~/mymongo$ mongoimport --db test --collection restaurants --drop --file primer-dataset.json 
	2018-02-27T21:18:45.229+0800    [........................] 	test.restaurants    0B/11.3MB (0.0%)
	2018-02-27T21:18:45.749+0800    [........................] 	test.restaurants    0B/11.3MB (0.0%)
	2018-02-27T21:18:45.749+0800    Failed: error connecting to db server: no reachable servers
	2018-02-27T21:18:45.749+0800    imported 0 documents
	
导入localhost成功
	
	liu@liu:~/mymongo$ mongoimport --db test --collection restaurants --drop --file primer-dataset.json 
	2018-02-27T21:36:25.435+0800    connected to: localhost
	2018-02-27T21:36:25.443+0800    dropping: test.restaurants
	2018-02-27T21:36:26.930+0800    imported 25359 documents
	
##运行mongo shell，进入mongo窗口
	
	
	liu@liu:~/mymongo$ mongo
	MongoDB shell version v3.6.2
	connecting to: mongodb://127.0.0.1:27017
	MongoDB server version: 3.6.2
	Server has startup warnings: 
	2018-02-27T21:29:04.976+0800 I STORAGE  [initandlisten] 
	2018-02-27T21:29:04.976+0800 I STORAGE  [initandlisten] ** WARNING: Using the XFS filesystem is strongly recommended with the 	WiredTiger storage engine
	2018-02-27T21:29:04.976+0800 I STORAGE  [initandlisten] **          See http://dochub.mongodb.org/core/prodnotes-filesystem
	2018-02-27T21:29:05.872+0800 I CONTROL  [initandlisten] 
	2018-02-27T21:29:05.873+0800 I CONTROL  [initandlisten] ** WARNING: Access control is not enabled for the database.
	2018-02-27T21:29:05.873+0800 I CONTROL  [initandlisten] **          Read and write access to data and configuration is unrestricted.
	2018-02-27T21:29:05.873+0800 I CONTROL  [initandlisten] 
	> 

	
在mongo壳中，敲入 `help ` ，会得到命令提示

	> help
        db.help()                    help on db methods
        db.mycoll.help()             help on collection methods
        sh.help()                    sharding helpers
        rs.help()                    replica set helpers
        help admin                   administrative help
        help connect                 connecting to a db help
        help keys                    key shortcuts
        help misc                    misc things to know
        help mr                      mapreduce

        show dbs                     show database names
        show collections             show collections in current database
        show users                   show users in current database
        show profile                 show most recent system.profile entries with time >= 1ms
        show logs                    show the accessible logger names
        show log [name]              prints out the last segment of log in memory, 'global' is default
        use <db_name>                set current database
        db.foo.find()                list objects in collection foo
        db.foo.find( { a : 1 } )     list objects in foo where a == 1
        it                           result of the last line evaluated; use to further iterate
        DBQuery.shellBatchSize = x   set default number of items to display on shell
        exit                         quit the mongo shell
	> 
	
##插入数据

在mongo shell切换到test数据表中
	
	> use test
	switched to db test
	> 

	
	


