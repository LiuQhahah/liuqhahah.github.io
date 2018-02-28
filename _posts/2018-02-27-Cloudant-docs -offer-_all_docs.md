---
layout:     post
title:      "Cloudant Docs  : Cloudant Offer _all_docs「译」"
subtitle:   "Cloudant  NoSQL DB 查询"
date:       2018-02-27 16:00:00
author:     "LiuQ"
header-img: "img/cloudant-logo.png"
header-mask: 0.3
catalog:    true
tags:
    - noSQL
    - 教程
    - Cloudant
    - 译文
---

***

***

> 这篇文章翻译自[IBM Cloud Databases](https://console.bluemix.net/docs/services/Cloudant/api/database.html#get-documents) 


针对IBM Cloudant数据库包含JSON对象，这些json对象被称为文件，所有的文件都存储在一个数据库中。

[操作指南](https://console.bluemix.net/docs/services/Cloudant/guides/transactions.html#-cloudant-) 提供了一个案例针对于电子商务应用可能会使用	Clouadant非关系型数据库。

对于更加复杂的数据库应用，牵扯到更多的存储空间，进程，以及分析任务，可以在[数据仓库](https://console.bluemix.net/docs/services/Cloudant/guides/warehousing.html#-) 实现。

Cloudant NoSQL DB也支持数据仓库。

#1.创建

 通过发送`PUT`请求到`https://$ACCOUNT.cloudant.com/$DATABASE`创建数据库。
 数据库的名称要以小写开始，只能包含以下字符：
 
 1. 小写字母（a-z）
 2. 数字（0-9）
 3. _,$,(,),+,-,/
 
 案例：
 
 1. 使用`HTTP`创建数据库(不会实现)
 
	 	PUT /$DATABASE HTTP/1.1
	
		HOST: $ACCOUNT.cloudant.com
		
通过Postman（chrome的一个插件，另行安装），输入如下相同的url，选择`PUT`，点击`Send`按钮后，出现
	
	{
    "ok": true
	}
表明成功创建，在Cloudant 账户界面上，同样看到新创建的数据表。
	
 2. 使用命令行创建数据库：
	
		liu@liu:~/mycloudant$ sudo curl https://$url/test -X PUT
		[sudo] password for liu: 
		{"ok":true}
	
$url 为ClouantNoSQLDB中服务凭证中的url，包含了：用户名：密码@用户名.cloudant.com。

错误情况：

	liu@liu:~/mycloudant$ sudo curl https://79f006ec-e65c-4ab0-9280-9d5701534e3f-bluemix.cloudant.com/test -X PUT                                                   
	{"error":"unauthorized","reason":"one of _admin, server_admin is required for this request"}
	//权限不够


如果创建成功，会得到201或202恢复码（未得到，猜测在进行断点调试时，会看到），下表为错误代码指示的具体错误



错误码|原因
201|合法则创建成功
202|至少有一个节点 响应则正确接收
403|数据表名非法
412|数据表已创建
500|返回的节点响应错误

##数据库拓扑

这是有可能的，对于一个在专用数据集群修改分片拓扑的配置是存在可能的，当数据表创建的时候就可以更改了。但是，对影响到数据库性能的数据参数的选择是不多的。

更多关于在专用数据库环境下修改数据表配置的信息可以联系Cloudant NoSQL DB 支持。

**注意**在多用户集群中不可以修改配置信息。

##2获取数据信息

发送`GET`请求到`https://$ACCOUNT.cloudant.com/$DATABASE`，会返回数据库信息，比如包含多少条信息。

使用`HTTP`获取数据表信息：

	GET /$DATABASE HTTP/1.1
使用命令行进行操作：

	curl https://$ACCOUNT.cloudant.com/$DATABASE \
	
	liu@liu:~/mycloudant$ sudo curl https://$ACCOUNT.cloudant.com/test
	[sudo] password for liu: 
	{"update_seq":"0-g1AAAAQneJzLYWBgEMhgTmHQTElKzi9KdUhJMtZLytVNTtYtLdYtzi8tydA1MNZLzskvTUnMK9HLSy3JAWphSmRI4v___39WIgNIsxZcs6ERMbqTBIBkkjzCAITtlkTpVwDp18em34Qo_QYg_fbY9BPl-yQHkH5_CgIgAGRAPPkBkADSn09-ABSA9NeTHQB5LECSoQFIAY3oJzMQIIZMgBgyn7yAgJixAGLGevICA2LGBogZ-ykJkAMQM85TFCAXIIbcpyRAHkDMeE9JgHyAmAFKIVkA0XpbhQ","db_name":"test","sizes":{"file":68292,"external":0,"active":0},"purge_seq":0,"other":{"data_size":0},"doc_del_count":0,"doc_count":0,"disk_size":68292,"disk_format_version":6,"data_size":0,"compact_running":false,"cluster":{"q":16,"n":3,"w":2,"r":2},"instance_start_time":"0"}
	liu@liu:~/mycloudant$ 
	
下表显示了想要返回的元素结构：


Field|描述
compact_running|设置为真如果数据库压缩例程在此数据库上运行
db_name|数据库名称
disk_format_version|物理格式的版本当存储在磁盘的数据
disk_size|存储在磁盘上的字节数，不包含视图检索的检索
doc_count|指定数据库的文档的数量
doc_del_count|被删除文档的数量
instance_start_time|恒为0
other|包含data_size的JSON对象
purge_seq|在数据表中清除操作的次数
sizes|JSON对象，包含file,external,active 大小。active是存储字节数。external是解压缩用户数据的字节大小，这个值是技术数据大小。other/data_size部分是external部分的别名。file是存储在存盘中的数据的字节数。`Index`不在计算中，disk_size也是file的别名，它的大小是等待压缩的大小。
update_seq|不透明的字符串用来描述数据表的状态，不依赖于计算更新次数的字符串
 通过`GET`获取数据库信息
 
 	curl https://$ACCOUNT.cloudant.com/$DATABASE \
 结果如下
 
 	{
    "update_seq": "0-g1AAAAQneJzLYWBgEMhgTmHQSklKzi9KdUhJMjTSS8rVTU7WLS3WLc4vLcnQNTDWS87JL01JzCvRy0styQHqYUpkSOL___9_ViIDSLcmXLclMZqTBIBkkjw2_SZE6VcA6dfHpt-YKP0GIP32CP2k-j7JAWSAP_kBEADSH09-ACSA9OeTHwAFIP315AdAHguQZGgAUkAz-skLBIgZEyBmzCcvICBmLICYsZ68wICYsQFixn6KAuQAxJDzlATIBYgZ9ykJkAcQM95TEiAfIGaAUkgWALT8W4U",
    "db_name": "test1",
    "sizes": {
        "file": 68292,
        "external": 0,
        "active": 0
    },
    "purge_seq": 0,
    "other": {
        "data_size": 0
    },
    "doc_del_count": 0,
    "doc_count": 0,
    "disk_size": 68292,
    "disk_format_version": 6,
    "data_size": 0,
    "compact_running": false,
    "cluster": {
        "q": 16,
        "n": 3,
        "w": 2,
        "r": 2
    },
    "instance_start_time": "0"
	}
	
##获取数据库中所有的数据表
通过`GET	`发送`https://$ACCOUNT.cloudant.com/_all_dbs`可以得到数据库所有数据信息

通过`HTTP`获取所有信息（未实现）
	
	GET /_all_dbs HTTP/1.1

在命令行下操作：

	curl https://$ACCOUNT.cloudant.com/_all_dbs \
 以JSON数组形式返回，结果为4个表
 
 	[
    "iot2040",
    "nodered",
    "test",
    "test1"
	]
 

### 著作权声明


本文由 [@刘强](https://github.com/LiuQhahah/) 翻译，转载请保留原文链接 ;)
