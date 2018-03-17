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
	

	
## mongodb nodejs应用

>参考：http://mongodb.github.io/node-mongodb-native/2.2/quick-start/quick-start/


  演示如何用nodejs和mongodb快速创建一个简单的应用，仅包括设置驱动和实现简单的CRUD操作。更多详情请参见[教程](http://mongodb.github.io/node-mongodb-native/2.2/tutorials/connect/) 。
  
###创建package.json文件

1. 为你的应用创建一个文件夹

	liu@ubuntu:~/mymongo$ mkdir nodejs4mongo/
	liu@ubuntu:~/mymongo$ cd nodejs4mongo/
2. 进入文件目录中，输入以下目录，回答响应的问题，初始化工程的结构

	liu@ubuntu:~/mymongo/nodejs4mongo$ npm init

3. 安装依赖驱动

	liu@ubuntu:~/mymongo/nodejs4mongo$ npm install mongodb@2.2 --save
	
npm下载大量的文件，都在`node_modules`文件中


###开启mongo服务

前提是mongo安装成功，新建存储数据库的文件夹（比如在/data目录下），启动mongo进程

创建存储数据的文件夹

	liu@ubuntu:~/mymongo/nodejs4mongo$ ls
	data  node_modules  package.json
	liu@ubuntu:~/mymongo/nodejs4mongo$ 

开启服务：

	liu@ubuntu:~/mymongo/nodejs4mongo$ mongod --dbpath=data



同时运行mongo:

	liu@ubuntu:~$ mongo
	MongoDB shell version v3.6.2
	connecting to: mongodb://127.0.0.1:27017
	MongoDB server version: 3.6.2
	Server has startup warnings: 
	2018-03-17T09:40:03.390+0800 I STORAGE  [initandlisten] 
	2018-03-17T09:40:03.390+0800 I STORAGE  [initandlisten] ** WARNING: Using the XFS filesystem is strongly recommended with the WiredTiger storage engine
	2018-03-17T09:40:03.390+0800 I STORAGE  [initandlisten] **          See http://dochub.mongodb.org/core/prodnotes-filesystem
	2018-03-17T09:40:04.462+0800 I CONTROL  [initandlisten] 
	2018-03-17T09:40:04.462+0800 I CONTROL  [initandlisten] ** WARNING: Access control is not enabled for the database.
	2018-03-17T09:40:04.462+0800 I CONTROL  [initandlisten] **          Read and write access to data and configuration is unrestricted.
	2018-03-17T09:40:04.462+0800 I CONTROL  [initandlisten] ** WARNING: You are running this process as the root user, which is not recommended.
	2018-03-17T09:40:04.462+0800 I CONTROL  [initandlisten] 
	2018-03-17T09:40:04.462+0800 I CONTROL  [initandlisten] ** WARNING: This server is bound to localhost.
	2018-03-17T09:40:04.462+0800 I CONTROL  [initandlisten] **          Remote systems will be unable to connect to this server. 
	2018-03-17T09:40:04.462+0800 I CONTROL  [initandlisten] **          Start the server with --bind_ip <address> to specify which IP 
	2018-03-17T09:40:04.462+0800 I CONTROL  [initandlisten] **          addresses it should serve responses from, or with --bind_ip_all to
	2018-03-17T09:40:04.463+0800 I CONTROL  [initandlisten] **          bind to all interfaces. If this behavior is desired, start the
	2018-03-17T09:40:04.463+0800 I CONTROL  [initandlisten] **          server with --bind_ip 127.0.0.1 to disable this warning.
	2018-03-17T09:40:04.463+0800 I CONTROL  [initandlisten] 
	> 
	

创建app.js
创建mongo客户端，连接到服务器上，

执行app.js

	var MongoClient = require('mongodb').MongoClient,assert = require('assert');

	//连接到url
	var url = "mongodb://localhost:27017/myproject";

	//连接到服务器上
	MongoClient.connect(url,function(err,db){
	  assert.equal(null,err);
	  console.log("Connected successfully to server");

	  db.close();
	});

显示结果如下：

	liu@ubuntu:~/mymongo/nodejs4mongo$ node app.js 
	Connected successfully to server
	liu@ubuntu:~/mymongo/nodejs4mongo$ 



###插入文档

在app.js中添加方法：

	var insertDocuments = function(db,callback){
	  //获取文件collection
	  var collection = db.collection('documents');

	  //插入文档
	  collection.insertMany([
	    {a:1},{a:2},{a,3}
	  ],function(err,result){
	    assert.equal(err,null);
	    assert.equal(3,result.result.n);
	    assert.equal(3,result.ops.length);
	    console.log("Inserted 3 documents into the collection");
	    callback(result);
	  })
	}
	
	
插入命令返回的以下对象内容：

- result 包含MongoDB的文档内容
- ops 包含添加的`_id`内容
- connection 


运行结果，插入成功：

	liu@ubuntu:~/mymongo/nodejs4mongo$ node app.js 
	Connected successfully to server
	Inserted 3 documents into the collection
	liu@ubuntu:~/mymongo/nodejs4mongo$ 
	
	
###查找所有文档

查找函数

	var findDocuments = function(db,callback){
	  var collection = db.collection('documents');

	  collection.find({}).toArray(function(err,docs){
	    assert.equal(err,null);
	    console.log("Found the following records");
	    console.log(docs);
	    callback(docs);
	  });
	}

运行结果：通过数组形式输出结果：

	liu@ubuntu:~/mymongo/nodejs4mongo$ node app.js 
	Connected successfully to server
	Inserted 3 documents into the collection
	Found the following records
	[ { _id: 5aac75c91e09730f9178639d, a: 1 },
	  { _id: 5aac75c91e09730f9178639e, a: 2 },
	  { _id: 5aac75c91e09730f9178639f, a: 3 },
	  { _id: 5aac75f6414d3e0fc556e188, a: 1 },
	  { _id: 5aac75f6414d3e0fc556e189, a: 2 },
	  { _id: 5aac75f6414d3e0fc556e18a, a: 3 },
	  { _id: 5aac7c85f2781614523eb8f9, a: 1 },
	  { _id: 5aac7c85f2781614523eb8fa, a: 2 },
	  { _id: 5aac7c85f2781614523eb8fb, a: 3 },
	  { _id: 5aac7d8c7ea30815d50399d9, a: 1 },
	  { _id: 5aac7d8c7ea30815d50399da, a: 2 },
	  { _id: 5aac7d8c7ea30815d50399db, a: 3 } ]
	liu@ubuntu:~/mymongo/nodejs4mongo$ 


###使用query过滤查找文档

	var findDocuments = function(db,callback){
	  var collection = db.collection('documents');

	  //collection.find({}).toArray(function(err,docs){
	  collection.find({'a':3}).toArray(function(err,docs){
	    assert.equal(err,null);
	    console.log("Found the following records");
	    console.log(docs);
	    callback(docs);
	  });
	}

查找`a`为3的id
输出结果：

	liu@ubuntu:~/mymongo/nodejs4mongo$ node app.js 
	Connected successfully to server
	Inserted 3 documents into the collection
	Found the following records
	[ { _id: 5aac75c91e09730f9178639f, a: 3 },
	  { _id: 5aac75f6414d3e0fc556e18a, a: 3 },
	  { _id: 5aac7c85f2781614523eb8fb, a: 3 },
	  { _id: 5aac7d8c7ea30815d50399db, a: 3 },
	  { _id: 5aac81511b016f17bf23e545, a: 3 } ]
	liu@ubuntu:~/mymongo/nodejs4mongo$ 


###更新数据信息

先移除数据;
插入数据;
更新数据。

更新信息：


	~~//update
	var updateDocument = function(db,callback){
	  var collection=  db.collection('documents');
	  collection.updateOne({a:3},{$set:{b:10}},function(err,result){
	    assert.equal(err,null);
	    assert.equal(1,result.result.n);
	    console.log("Updated the document with filed a equal to 2");
	    callback(result);
	  });
	}

运行结果：

	liu@ubuntu:~/mymongo/nodejs4mongo$ node app.js 
	Connected successfully to server
	Removed the document 
	Inserted 3 documents into the collection
	Updated the document with filed a equal to 2
	Found the following records
	[ { _id: 5aac84525dfc941b7e664300, a: 1 },
	  { _id: 5aac84525dfc941b7e664301, a: 2 },
	  { _id: 5aac84525dfc941b7e664302, a: 3, b: 10 } ]
	liu@ubuntu:~/mymongo/nodejs4mongo$ 


###删除一个元素

删除函数：

	var removeOneDocument = function(db,callback){
	  var collection = db.collection('documents');

	  collection.deleteOne({a:3},function(err,result){
	    assert.equal(err,null);
	    assert.equal(1,result.result.n);
	    console.log("Remove the document with the field a equal to 3");
	    callback(result);
	  })
	}

运行结果：

	liu@ubuntu:~/mymongo/nodejs4mongo$ node app.js 
	Connected successfully to server
	Removed the document 
	Inserted 3 documents into the collection
	Updated the document with filed a equal to 2
	Found the following records
	[ { _id: 5aac86d540cf0f1df2add1b4, a: 1 },
	  { _id: 5aac86d540cf0f1df2add1b5, a: 2 },
	  { _id: 5aac86d540cf0f1df2add1b6, a: 3, b: 10 } ]
	Remove the document with the field a equal to 3
	Found the following records
	[ { _id: 5aac86d540cf0f1df2add1b4, a: 1 },
	  { _id: 5aac86d540cf0f1df2add1b5, a: 2 } ]
	liu@ubuntu:~/mymongo/nodejs4mongo$ 



##检索一个集合collection


	//检索一个集合

	var indexCollection = function(db,callback){
	  db.collection('document').createIndex({
	    "a":2
	  },null,function(err,results){
	    console.log("results:  "+results);
	    callback();
	  })
	}


输出结果：

	liu@ubuntu:~/mymongo/nodejs4mongo$ node app.js 
	Connected successfully to server
	Removed the document 
	Inserted 3 documents into the collection
	Updated the document with filed a equal to 2
	Found the following records
	[ { _id: 5aac89274dcedc2065cf4133, a: 1 },
	  { _id: 5aac89274dcedc2065cf4134, a: 2 },
	  { _id: 5aac89274dcedc2065cf4135, a: 3, b: 10 } ]
	Remove the document with the field a equal to 3
	results:  a_2
	Found the following records
	[ { _id: 5aac89274dcedc2065cf4133, a: 1 },
	  { _id: 5aac89274dcedc2065cf4134, a: 2 } ]
	liu@ubuntu:~/mymongo/nodejs4mongo$ 


**更多教程信息**：http://mongodb.github.io/node-mongodb-native/2.2/tutorials/main/