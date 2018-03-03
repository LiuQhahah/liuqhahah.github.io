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
 
##得到设计文档（design document）

通过发送`GET`到`https://$ACCOUNT.cloudant.com/$DATABASE/_design_docs`获取设计文档。

HTTP操作

	GET /{$DATABASE}/_design_docs
命令行操作： 

	curl https://$ACCOUNT.cloudant.com/_design_docs
	
	
返回的结果

	{
    "error": "forbidden",
    "reason": "server_admin access is required for this request"
	}

在`_design_docs`端点会返回在你指定的数据库的所有文档中的一个JOSN结构
。返回的信息是JSON结构包含关于返回结构的元信息，包括一系列所有设计文档和他们基础内容，有ID ，版本号，key。这个key由设计文档的_id决定。

**注意**在_design_docs端点中可以使用`GET`以及`POST`方法，以下是例子。

`_design_docs`端点包含以下查询参数。


查询参数|描述|选项|类型|默认值

conflicts|在回复中包含冲突信息，如果_docs不为真，则忽略|Yes|布尔值|false
descending|根据key进行降序排列|Yes|布尔值|false
endkey|当到达指定值时，停止数据返回|Yes|字符串|null
end_key|endkey的别号|Yes|字符串|null
include_docs|返回设计文档的所有详细内容|Yes|布尔值
inclusive_end|在结果中是否包含特有的key|Yes|布尔值|true
key|返回专有key的设计文档|Yes|字符串
keys|返回专有key的设计文档|字符串列表
limit|限制设计文档的个数|Yes|number
skip|在开始返回结果时跳过的记录值的个数|Yes|number
startkey|从此key开始返回|Yes|字符串
start_key|同startkey|字符串
update_seq|响应包含一个更新的update_seq值表明哪一个序列ID|Yes|布尔值|false

用以下的JSON对象返回结果：


响应JSON对象|描述|类型
offset|从设计文档哪里开始算|number
rows|视图行对象的数组，返回的信息仅包含文档的ID以及版本号|数值
total_rows|数据库中总行数，注：并不是请求中的行数|number
update_seq|数据库的当前更新序列|number

如果成功完成请求，状态码则是200


>例：
`https://79f006ec-e65c-4ab0-9280-9d5701534e3f-bluemix.cloudant.com/iot2040/_all_docs?inclusive_end=false&start_key=“_design“&end_key=”_design0“`


返回结果：

	{
	total_rows: 1035547,
	offset: 660468,
	rows: [ ],
	}

`_all_docs`
`inclusive_end`=false
`start_key`="_design"
`end_key` ="_design0"

表明当前的`iot2040`数据表中的所有文档没有以关键字`_design`开始，以`_design0`结束的。inclusive_end为false/true不影响结果的产生

创建过design documents之后，再次输入[链接](https://79f006ec-e65c-4ab0-9280-9d5701534e3f-bluemix.cloudant.com/iot2040/_all_docs?inclusive_end=false&start_key=“_design“&end_key=”_design0“) 
例：用`GET`方法请求design documents

https://79f006ec-e65c-4ab0-9280-9d5701534e3f-bluemix.cloudant.com/iot2040/_all_docs?inclusive_end=false&start_key=”_design“&end_key=“_design0”
结果为：

`
{
total_rows: 1036494,
offset: 661090,
rows: [
{
id: "_design/0c803213c8023b7398f75c52b281a01da51076fc",
key: "_design/0c803213c8023b7398f75c52b281a01da51076fc",
value: {
rev: "1-a3d9d6e8096d729c0b258dd96ab59fa8"
},
},
{
id: "_design/demo",
key: "_design/demo",
value: {
rev: "1-8d361a23b4cb8e213f0868ea3d2742c2"
},
},
{
id: "_design/search",
key: "_design/search",
value: {
rev: "1-8fda19d078cb605016ffcee3896f9b48"
},
},
{
id: "_design/test",
key: "_design/test",
value: {
rev: "1-f16e968cba5fe4d5aae9edb03d3da96d"
},
},
],
}`

##Get Documents

用`GET`请求`https://$ACCOUNT.cloudant.com/$DATABASE/_all_docs`列出数据库中的所有文件。

在`_all_docs`后可跟以下查询参数：

参数|描述|可选|类型|默认值
conflicts|只有include_docs设置true才可以设置|yes|布尔值|false
deleted_conflicts|返回被删的冲突版本|yes|布尔值|false
descending|降序排列|yes|布尔值|false
endkey|截止关键字|yes|字符串
include_docs|范围文档所有信息|yes|布尔值|false
inclusive_end|key值等于`endkey`的值的行数|yes|布尔值|true
key|返回ID与key匹配的文件|yes|字符串
keys|返回ID与其中一个key匹配的文件|yes|字符串序列
limit|返回文档的个数|yes|number
meta|包含3个参数：conflicts,deleted_confilcts,以及revs_info.当meta=true等同于conflicts=true&deleted_conflicts=true&revs_info=true.|yes|布尔值|false
r|指定[read_quorum](https://console.bluemix.net/docs/services/Cloudant/api/document.html#quorum---writing-and-reading-data) |yes|number|2
revs_info|所知道文档版本的所有细节信息|yes|布尔值|false
skip|跳过多少条记录|yes|number|0
startkey|从专有key开始返回|yes|字符串

**注意**：使用`include_docs=true`会有[性能影响](https://console.bluemix.net/docs/services/Cloudant/api/using_views.html#include_docs_caveat) 


**注意**使用`key`参数时，发送`POST`请求要比`GET`响应速度更快。

命令行操作：

	curl https://$ACCOUNT.cloudant.com/$DATABASE/_all_docs?keys=["somekey","someotherkey"]
	

返回的时JSON对象，包含数据库中所有匹配参数的文档。接下来的表描述了个体领域的意义：

领域|描述|类型
offset|文件列表开始的位移|number
rows|文件对象的数组|数组
total_rows|数据库中的文件数或匹配查询参数的view|number
update_seq|当前更新序列|字符串


案例：请求数据库中所有文件的响应：

	{
	    "total_rows": 3,
	    "offset": 0,
	    "rows": [
		{
		    "id": "5a049246-179f-42ad-87ac-8f080426c17c",
		    "key": "5a049246-179f-42ad-87ac-8f080426c17c",
		    "value": {
		        "rev": "2-9d5401898196997853b5ac4163857a29"
		    }
		},
		{
		    "id": "96f898f0-f6ff-4a9b-aac4-503992f31b01",
		    "key": "96f898f0-f6ff-4a9b-aac4-503992f31b01",
		    "value": {
		        "rev": "2-ff7b85665c4c297838963c80ecf481a3"
		    }
		},
		{
		    "id": "d1f61e66-7708-4da6-aa05-7cbc33b44b7e",
		    "key": "d1f61e66-7708-4da6-aa05-7cbc33b44b7e",
		    "value": {
		        "rev": "2-cbdef49ef3ddc127eff86350844a6108"
		    }
		}
	    ]
	}
	[design document](https://console.bluemix.net/docs/services/Cloudant/api/design_documents.html) 

##Get Changes

发送`GET`请求`https://$ACCOUNT.cloudant.com/$DATABASE/_changes`，返回一系列更改，在数据库中文件的更改。包括插入，更新，删除。

当收到`_changes`时，数据库的每个碎片的复制版本提供一系列更改。将这些内容返回到请求的客户端。

`_changes`接收以下请求参数：

参数|描述|支持值|默认值
conflicts|仅当`include_docs`为true时，每个文档的冲突信息都添加|boolean|false
descending|以降序返回|布尔值|false
doc_ids|过滤器设置了`_doc_ids`才有效，发送专有文档的更改情况，注：doc_ids参数仅当Cloudant NoSQL DB兼容CouchDB才可以，[详细内容](https://console.bluemix.net/docs/services/Cloudant/api/advanced.html#get-/) |文档ID的JSON数组
feed|[细节](https://console.bluemix.net/docs/services/Cloudant/api/database.html#the-feed-argument) |"continuous","longpoll","normal"|"normal"
filter|过滤的功能达到更新目的，filter定义在[design document](https://console.bluemix.net/docs/services/Cloudant/api/design_documents.html)|字符串|no filter
heartbeat|当feed=longpoll或feed=continus时，在时间会发送空的线|所有可能的数字|no heartbeat
include_docs|文档作为最后结果的一部分|布尔值|false
limit|返回限定个数|所有的非负数|none(无限制)
seq_interval|seq的值包含在结果中的频率，高值增加了_changes的吞吐量，减少恢复的大小。注：在非连续的_changes模式中，最后一个_seq值被移走的|	任意正数|1
since|特定语句识别后，才返回的改变结果，[更多细节](https://console.bluemix.net/docs/services/Cloudant/api/database.html#the-since-argument) |序列识别|0
style|在更改的数组中返回版本数，`main_only`返回当前"winning"版本，`all_docs`返回所有叶子版本，包含冲突以及被删除之前的冲突版本|main_only,all_docs|main_only
timeout|超时门限|正数即可

**注：**使用`include_docs =true`时，可能出现[ performance implications](https://console.bluemix.net/docs/services/Cloudant/api/using_views.html#include_docs_caveat) 











### 著作权声明


本文由 [@刘强](https://github.com/LiuQhahah/) 翻译，转载请保留原文链接 ;)
