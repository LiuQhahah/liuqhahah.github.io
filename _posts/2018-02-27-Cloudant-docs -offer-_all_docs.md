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

使用`HTTP`获取数据库中更改的文件
	
	GET /$DATABASE/_changes HTTP/1.1
	
使用命令行操作

	curl https://$ACCOUNT.cloudant.com/$DATABASE/	_changes \


`https://$ACCOUNT.cloudant.com/iot2040/_changes?limit=10&include_docs=true&key="temp"`

返回结果为

		{
   		 "results": [
        {
            "seq": "1-g1AAAARheJzLYWBgEMhgTmHQSklKzi9KdUhJMjTUS8rVTU7WLS3WLc4vLcnQNTDWS87JL01JzCvRy0styQHqYUpkSOL___9_ViIDSLcmQjcxmpMEgGSSPDb9FkTpVwDp18em35Qo_QYg_fYg_RnMiYy5QB67RWKiZXJyGilmkRNuSQ4gq_3JD7oAkP54bPpNiNKfANKfT37QFYD01yP0kxoAeSxAkqEBSAHN6CcvECBmTICYMZ-8NAQxYwHEjPXkBQbEjA0QM_ZTFCAHIIacpyRALkDMuE9JgDyAmPGekgD5ADEDlEKyAENvbd0",
            "id": "8eca5a82dc1134941739995808399316",
            "changes": [
                {
                    "rev": "1-aeafb34c9d0e5b76dda628203f936e8c"
                }
            ],
            "doc": {
                "_id": "8eca5a82dc1134941739995808399316",
                "_rev": "1-aeafb34c9d0e5b76dda628203f936e8c",
                "topic": "iot-2/type/GataWays/id/IoT2040/evt/update/fmt/json",
                "payload": {
                    "d": {
                        "temp": 18.5,
                        "humidity": 61,
                        "location": {
                            "longitude": -98.49,
                            "latitude": 29.42
                        }
                    }
                },
                "deviceId": "IoT2040",
                "deviceType": "GataWays",
                "eventType": "update",
                "format": "json"
            }
        },
        {
            "seq": "2-g1AAAASbeJzLYWBgEMhgTmHQSklKzi9KdUhJMjTUS8rVTU7WLS3WLc4vLcnQNTDWS87JL01JzCvRy0styQHqYUpkSOL___9_ViIDSLcmQjcxmpMEgGSSPDb9FkTpVwDp18em35Qo_QYg_fYg_RnMiYy5QB67RWKiZXJyGilmkRNuSQ4gq_3JD7oAkP54bPpNiNKfANKfT37QFYD01yP0kxoAeSxAkqEBSAHN6CcvECBmTICYMZ-8NAQxYwHEjPXkBQbEjA0QM_Yj0lJammlikgnN0xLE-gMQ689TEpQXIGbcpyQoH0DMeE9JUH6AmAFKW1kAUUWARA",
            "id": "f338341849e8dc2c7085faf2cd225ea4",
            "changes": [
                {
                    "rev": "1-e903f1dd767e28482fbbd547e06ca2f6"
                }
            ],
            "doc": {
                "_id": "f338341849e8dc2c7085faf2cd225ea4",
                "_rev": "1-e903f1dd767e28482fbbd547e06ca2f6",
                "topic": "iot-2/type/GataWays/id/IoT2040/evt/update/fmt/json",
                "payload": {
                    "d": {
                        "temp": 24,
                        "humidity": 53,
                        "location": {
                            "longitude": -98.49,
                            "latitude": 29.42
                        }
                    }
                },
                "deviceId": "IoT2040",
                "deviceType": "GataWays",
                "eventType": "update",
                "format": "json"
            }
        },
        {
            "seq": "3-g1AAAATVeJzLYWBgEMhgTmHQSklKzi9KdUhJMjTUS8rVTU7WLS3WLc4vLcnQNTDWS87JL01JzCvRy0styQHqYUpkSOL___9_ViIDSLcmQjcxmpMEgGSSPDb9FkTpVwDp18em35Qo_QYg_fYg_RnMiYy5QB67RWKiZXJyGilmkRNuSQ4gq_3JD7oAkP54bPpNiNKfANKfT37QFYD01yP0kxoAeSxAkqEBSAHN6CcvECBmTICYMZ-8NAQxYwHEjPXkBQbEjA0QM_Yj0lJammlikgnN0xLE-gMQ689TEpQXIGbcpyQoH0DMeE9JUH6AmIGULU1SLCwMUpJIMS8LADTGkks",
            "id": "e3eea6ef3cad0cca655951ad44ae29a3",
            "changes": [
                {
                    "rev": "1-9c4b8b9af4cf602be0aec474cc06f657"
                }
            ],
            "doc": {
                "_id": "e3eea6ef3cad0cca655951ad44ae29a3",
                "_rev": "1-9c4b8b9af4cf602be0aec474cc06f657",
                "topic": "iot-2/type/GataWays/id/IoT2040/evt/update/fmt/json",
                "payload": {
                    "d": {
                        "temp": 21.5,
                        "humidity": 65,
                        "location": {
                            "longitude": -98.49,
                            "latitude": 29.42
                        }
                    }
                },
                "deviceId": "IoT2040",
                "deviceType": "GataWays",
                "eventType": "update",
                "format": "json"
            }
        },
        {
            "seq": "4-g1AAAAUPeJzLYWBgEMhgTmHQSklKzi9KdUhJMjTUS8rVTU7WLS3WLc4vLcnQNTDWS87JL01JzCvRy0styQHqYUpkSOL___9_ViIDSLcmQjcxmpMEgGSSPDb9FkTpVwDp18em35Qo_QYg_fYg_RnMiYy5QB67RWKiZXJyGilmkRNuSQ4gq_3JD7oAkP54bPpNiNKfANKfT37QFYD016MEnWVqSqJlmgWtgy6PBUgyNAApoO395AUfxIwJEDPmk5f6IGYsgJixnrxghJixAWLGfkRQpqWZJiaZ0DwVQqw_ALH-PCVBeQFixn1KgvIBxIz3lATlB4gZSKnSJMXCwiAliRTzsgDsh6SO",
            "id": "32c300cb5ef952b269d92116b65a3789",
            "changes": [
                {
                    "rev": "1-6025cd30f6115104c8d08850d2f89724"
                }
            ],
            "doc": {
                "_id": "32c300cb5ef952b269d92116b65a3789",
                "_rev": "1-6025cd30f6115104c8d08850d2f89724",
                "topic": "iot-2/type/GataWays/id/IoT2040/evt/update/fmt/json",
                "payload": {
                    "d": {
                        "temp": 17,
                        "humidity": 55,
                        "location": {
                            "longitude": -98.49,
                            "latitude": 29.42
                        }
                    }
                },
                "deviceId": "IoT2040",
                "deviceType": "GataWays",
                "eventType": "update",
                "format": "json"
            }
        },
        {
            "seq": "5-g1AAAAUPeJzLYWBgEMhgTmHQSklKzi9KdUhJMjTUS8rVTU7WLS3WLc4vLcnQNTDWS87JL01JzCvRy0styQHqYUpkSOL___9_ViIDSLcmQjcxmpMEgGSSPDb9FkTpVwDp18em35Qo_QYg_fYg_RnMiUy5QB67RWKiZXJyGilmkRNuSQ4gq_3JD7oAkP54bPpNiNKfANKfT37QFYD010ODjhEcdJapKYmWaRa0Dro8FiDJ0ACkgLb3kxd8EDMmQMyYT17qg5ixAGLGevKCEWLGBogZ-xFBmZZmmphkQvNUCLH-AMT685QE5QWIGfcpCcoHEDPeUxKUHyBmIKVKkxQLC4OUJFLMywIA8JKkjw",
            "id": "8eca5a82dc11349417399958083cf941",
            "changes": [
                {
                    "rev": "1-aeafb34c9d0e5b76dda628203f936e8c"
                }
            ],
            "doc": {
                "_id": "8eca5a82dc11349417399958083cf941",
                "_rev": "1-aeafb34c9d0e5b76dda628203f936e8c",
                "topic": "iot-2/type/GataWays/id/IoT2040/evt/update/fmt/json",
                "payload": {
                    "d": {
                        "temp": 18.5,
                        "humidity": 61,
                        "location": {
                            "longitude": -98.49,
                            "latitude": 29.42
                        }
                    }
                },
                "deviceId": "IoT2040",
                "deviceType": "GataWays",
                "eventType": "update",
                "format": "json"
            }
        },
        {
            "seq": "6-g1AAAAUPeJzLYWBgEMhgTmHQSklKzi9KdUhJMjTUS8rVTU7WLS3WLc4vLcnQNTDWS87JL01JzCvRy0styQHqYUpkSOL___9_ViIDSLcmQjcxmpMEgGSSPDb9FkTpVwDp18em35Qo_QYg_fYg_RnMiUy5QB67RWKiZXJyGilmkRNuSQ4gq_3JD7oAkP54bPpNiNKfANKfT37QFYD010ODjhEcdJapKYmWaRa0Dro8FiDJ0ACkgLb3kxd8EDMmQMyYT17qg5ixAGLGevKCEWLGBogZ-xGpMC3NNDHJhOapEGL9AYj15ykJygsQM-5TEpQPIGa8pyQoP0DMQEqVJikWFgYpSaSYlwUA8hekkA",
            "id": "8eca5a82dc11349417399958084007fc",
            "changes": [
                {
                    "rev": "1-8eca3c7c7b3d3a84b6813816cee59638"
                }
            ],
            "doc": {
                "_id": "8eca5a82dc11349417399958084007fc",
                "_rev": "1-8eca3c7c7b3d3a84b6813816cee59638",
                "topic": "iot-2/type/GataWays/id/IoT2040/evt/update/fmt/json",
                "payload": {
                    "d": {
                        "temp": 23,
                        "humidity": 60,
                        "location": {
                            "longitude": -98.49,
                            "latitude": 29.42
                        }
                    }
                },
                "deviceId": "IoT2040",
                "deviceType": "GataWays",
                "eventType": "update",
                "format": "json"
            }
        },
        {
            "seq": "7-g1AAAAUPeJzLYWBgEMhgTmHQSklKzi9KdUhJMjTUS8rVTU7WLS3WLc4vLcnQNTDWS87JL01JzCvRy0styQHqYUpkSOL___9_ViIDSLcmQjcxmpMEgGSSPDb9FkTpVwDp18em35Qo_QYg_fYg_RnMiUy5QB67RWKiZXJyGilmkRNuSQ4gq_3JD7oAkP54bPpNiNKfANKfT37QFYD010ODjhEcdJapKYmWaRa0Dro8FiDJ0ACkgLb3kxd8EDMmQMyYT17qg5ixAGLGevKCEWLGBogZ-xGpMC3NNDHJhOapEGL9AYj15ykJygsQM-5TEpQPIGa8pyQoP0DMQMrQJikWFgYpSaSYlwUA8lGkkQ",
            "id": "e3eea6ef3cad0cca655951ad44aff771",
            "changes": [
                {
                    "rev": "1-e903f1dd767e28482fbbd547e06ca2f6"
                }
            ],
            "doc": {
                "_id": "e3eea6ef3cad0cca655951ad44aff771",
                "_rev": "1-e903f1dd767e28482fbbd547e06ca2f6",
                "topic": "iot-2/type/GataWays/id/IoT2040/evt/update/fmt/json",
                "payload": {
                    "d": {
                        "temp": 24,
                        "humidity": 53,
                        "location": {
                            "longitude": -98.49,
                            "latitude": 29.42
                        }
                    }
                },
                "deviceId": "IoT2040",
                "deviceType": "GataWays",
                "eventType": "update",
                "format": "json"
            }
        },
        {
            "seq": "8-g1AAAAVKeJzLYWBgEMhgTmHQSklKzi9KdUhJMjTUS8rVTU7WLS3WLc4vLcnQNTDWS87JL01JzCvRy0styQHqYUpkSOL___9_VgZzImMuUIDdxMDAwCg5hSSTQBZrIpQTY2-SAJBMkgdZnciAqt-CKP0KIP362PSbEqXfAKTfHup1JrDXLRITLZOT00gxi5wgT3IAWe2PzenE6Q8A6Y_Hpt-EKP0JIP355AddAUh_PUqqsUxNSbRMs6B10OWxAEmGBiAFtL2fvOCDmDEBYsZ88lIfxIwFEDPWkxeMEDM2QMzYj0iFaWmmiUkmNE-FEOsPQKw_T0lQXoCYcZ-SoHwAMeM9JUH5AWIGUoY2SbGwMEhJIsW8LADVy7aF",
            "id": "7bf8f3d987b76dc08f2c65488c54f5a6",
            "changes": [
                {
                    "rev": "1-aeafb34c9d0e5b76dda628203f936e8c"
                }
            ],
            "doc": {
                "_id": "7bf8f3d987b76dc08f2c65488c54f5a6",
                "_rev": "1-aeafb34c9d0e5b76dda628203f936e8c",
                "topic": "iot-2/type/GataWays/id/IoT2040/evt/update/fmt/json",
                "payload": {
                    "d": {
                        "temp": 18.5,
                        "humidity": 61,
                        "location": {
                            "longitude": -98.49,
                            "latitude": 29.42
                        }
                    }
                },
                "deviceId": "IoT2040",
                "deviceType": "GataWays",
                "eventType": "update",
                "format": "json"
            }
        },
        {
            "seq": "9-g1AAAAWFeJzLYWBgEMhgTmHQSklKzi9KdUhJMjTUS8rVTU7WLS3WLc4vLcnQNTDWS87JL01JzCvRy0styQHqYUpkSOL___9_VgZzImMuUIDdxMDAwCg5hSSTQBZrIpQTY2-SAJBMkgdZnciAqt-CKP0KIP362PSbEqXfAKTfHup1JrDXLRITLZOT00gxi5wgT3IAWe2PEurJKeYGpom0D_UAkNXx2ELNhCj9CSD9-eSHegFIfz2K1y1TUxIt0yxoHep5LECSoQFIAW3vx3A-CWZMgJgxn7yECzFjAcSM9eQFI8SMDRAz9iMScFqaaWKSCc0TMMT6AxDrz1MSlBcgZtynJCgfQMx4T0lQfoCYgVQWmKRYWBikJJFiXhYA68_I5A",
            "id": "e3eea6ef3cad0cca655951ad44ad00fc",
            "changes": [
                {
                    "rev": "1-8eca3c7c7b3d3a84b6813816cee59638"
                }
            ],
            "doc": {
                "_id": "e3eea6ef3cad0cca655951ad44ad00fc",
                "_rev": "1-8eca3c7c7b3d3a84b6813816cee59638",
                "topic": "iot-2/type/GataWays/id/IoT2040/evt/update/fmt/json",
                "payload": {
                    "d": {
                        "temp": 23,
                        "humidity": 60,
                        "location": {
                            "longitude": -98.49,
                            "latitude": 29.42
                        }
                    }
                },
                "deviceId": "IoT2040",
                "deviceType": "GataWays",
                "eventType": "update",
                "format": "json"
            }
        },
        {
            "seq": "10-g1AAAAW_eJzLYWBgEMhgTmHQSklKzi9KdUhJMjTUS8rVTU7WLS3WLc4vLcnQNTDWS87JL01JzCvRy0styQHqYUpkSOL___9_VgZzImMuUIDdxMDAwCg5hSSTQBZrIpQTY2-SAJBMkgdZnciAqt-CKP0KIP362PSbEqXfAKTfHup1JrDXLRITLZOT00gxi5wgT3IAWe2PEurJKeYGpom0D_UAkNXx2ELNhCj9CSD9-eSHegFIfz2K1y1TUxIt0yxoHep5LECSoQFIAW3vx3A-CWZMgJgxn7yECzFjAcSM9YhgME9JtjQ1NyXFPDIiAGL7Bojt-xFJPy3NNDHJhOZJH2L9AYj15ymJhAsQM-5TEgkPIGa8Jy8tQ8z4ADEDqRQxSbGwMEhJIsW8LACNttrI",
            "id": "d4a627225b7d7c5fd83415c2b8ffd9c8",
            "changes": [
                {
                    "rev": "1-28114b879d3f4922038198bab46c104c"
                }
            ],
            "doc": {
                "_id": "d4a627225b7d7c5fd83415c2b8ffd9c8",
                "_rev": "1-28114b879d3f4922038198bab46c104c",
                "topic": "iot-2/type/GataWays/id/IoT2040/evt/update/fmt/json",
                "payload": {
                    "d": {
                        "temp": 22.2,
                        "humidity": 49,
                        "location": {
                            "longitude": -98.49,
                            "latitude": 29.42
                        }
                    }
                },
                "deviceId": "IoT2040",
                "deviceType": "GataWays",
                "eventType": "update",
                "format": "json"
            }
        }
    ],
    "last_seq": "10-g1AAAAW_eJzLYWBgEMhgTmHQSklKzi9KdUhJMjTUS8rVTU7WLS3WLc4vLcnQNTDWS87JL01JzCvRy0styQHqYUpkSOL___9_VgZzImMuUIDdxMDAwCg5hSSTQBZrIpQTY2-SAJBMkgdZnciAqt-CKP0KIP362PSbEqXfAKTfHup1JrDXLRITLZOT00gxi5wgT3IAWe2PEurJKeYGpom0D_UAkNXx2ELNhCj9CSD9-eSHegFIfz2K1y1TUxIt0yxoHep5LECSoQFIAW3vx3A-CWZMgJgxn7yECzFjAcSM9YhgME9JtjQ1NyXFPDIiAGL7Bojt-xFJPy3NNDHJhOZJH2L9AYj15ymJhAsQM-5TEgkPIGa8Jy8tQ8z4ADEDqRQxSbGwMEhJIsW8LACNttrI",
    "pending": 1116267
	}
	
	
-----------------------



###在分布式数据库中的更改
Cloudant NoSQL DB数据库是分布式的，他们有共享和容错的符号，这些符号意味着导致`_changes`返回的结果不同于你期待的那样。

尤其是在请求中得到返回的信息，使用了`_since`对一个序列识别。在序列识别检测变化之前，可以得到变化的信息。这些额外变化的原因，以及对应用的意义，在[复制指南](https://console.bluemix.net/docs/services/Cloudant/guides/replication_guide.html#how-does-replication-affect-the-list-of-changes?) 


**注：**任何应用使用`_changes`请求时，必须正确处理一系列变化，这些变化可能是：

- 比起统一信息的较早的请求，这些变化都在回复中列出来，对于更改有不同的顺序。

- 在序列识别特定的变化中。。。。

###feed参数

`feed`参数，可以改变Cloudant NoSQL DB是如何发送回复的。默认情况下`_changes`报告所有的更改情况，然后关闭连接，等同于将feed设置成normal的时候。

如果设置`feed=longpoll` ,  当更改出现时，请求会发送到开启的服务器上， 此项可以连续监视更改的情况。
如果设置`feed= continuous`，报告更改不需要关闭连接。表明数据库保持连接知道明确关闭，发生之后，变化会尽可能快的返回到客户端。


每一行的连续回复要么是空要么JSON对象，表明着单一的变化。这选项表明：

- report格式反映changes本质
- JSON输出的有效性还存在

输入url：`https://$ACCOUNT.cloudant.com/iot2040/_changes?limit=10&feed=continuous&timeout=2000`
输出结果:

	{"seq":"1-g1AAAARheJzLYWBgEMhgTmHQTElKzi9KdUhJMtJLytVNTtYtLdYtzi8tydA1MNZLzskvTUnMK9HLSy3JAWphSmRI4v___39WIgNIsxZcs6EBMbqTBIBkkjzCAITt5kTpVwDp18em34wo_QYg_fbY9BPl-yQHkH5_CgIgAGRAPPkBkADSnw_Sn8GcyJgL5LGbJpokWyQnkWIWOUFXALK5nuygy2MBkgwNQApoRD-ZwQcxZALEkPnkBSHEjAUQM9aTl44gZmyAmLGfkgA5ADHjPEUBcgFiyH1KAuQBxIz3lATIB4gZoBSSBQCGC22u","id":"3205738e805c52794c33304a59e27508","changes":[{"rev":"1-8e27132ff78aa48d2a501e158bba5239"}]}
	{"seq":"2-g1AAAASbeJzLYWBgEMhgTmHQTElKzi9KdUhJMtJLytVNTtYtLdYtzi8tydA1MNZLzskvTUnMK9HLSy3JAWphSmRI4v___39WIgNIsxZcs6EBMbqTBIBkkjzCAITt5kTpVwDp1wfpz2BOZMwF8tjNUkySUy1SSDEL1WYzomw2ANlsj83lRIVbkgNIvz8FQRcAMiCe_KBLAOnPRwk600STZIvkJFoHXQHI5nqygy6PBUgyNAApoBH9ZAYfxJAJEEPmkxeEEDMWQMxYj2EGUYEBMWMDxIz9lATIAYgZ5ykKkAsQQ-5TEiAPIGa8pyRAPkDMAKWQLACkaX_r","id":"475fa9c10786794c57e0d95818d33db4","changes":[{"rev":"1-8e27132ff78aa48d2a501e158bba5239"}]}
	{"seq":"3-g1AAAATVeJzLYWBgEMhgTmHQTElKzi9KdUhJMtJLytVNTtYtLdYtzi8tydA1MNZLzskvTUnMK9HLSy3JAWphSmRI4v___39WIgNIsxZcs6EBMbqTBIBkkjzCAITt5kTpVwDp1wfpz2BOZMwF8tjNUkySUy1SSDEL1WYzomw2ANlsj83lRIVbkgNIvz8FQRcAMiCe_KBLAOnPRwk600STZIvkJFoHXQHI5nqygy6PBUgyNAApoBH9ZAYfxJAJEEPmkxeEEDMWQMxYj2EGUYEBMWMDxIz9lATIAYgZ5ykKkAsQQ-5TEiAPIGa8R6SrFEsTY7OUZBqnK4jtHyC2g9JWFgAgJpH3","id":"50fe62bdb4bd254ac557b4073739c620","changes":[{"rev":"1-5f4a09a26f183b821e8a1c017a6c9718"}]}
	{"seq":"4-g1AAAAUPeJzLYWBgEMhgTmHQTElKzi9KdUhJMtJLytVNTtYtLdYtzi8tydA1MNZLzskvTUnMK9HLSy3JAWphSmRI4v___39WIgNIsxZcs6EBMbqTBIBkkjzCAITt5kTpVwDp1wfpz2BOZMwF8tjNUkySUy1SSDEL1WYzomw2ANlsj83lRIVbkgNIvz8FQRcAMiCe_KBLAOnPRwk600STZIvkJFoHXQHI5nqygy6PBUgyNAApoBH9ZAYfxJAJEEPmkxeEEDMWQMxYjwhGy9TURAsDMxoHI8T2DRDb91MSlAcgZpynKCgvQAy5T0lQPoCY8R4RlCmWJsZmKcl0CcoPENtBqTILALRApAQ","id":"d4a627225b7d7c5fd83415c2b8ffd9c8","changes":[{"rev":"1-28114b879d3f4922038198bab46c104c"}]}
	{"seq":"5-g1AAAAUPeJzLYWBgEMhgTmHQTElKzi9KdUhJMtJLytVNTtYtLdYtzi8tydA1MNZLzskvTUnMK9HLSy3JAWphSmRI4v___39WIgNIsxZcs6EBMbqTBIBkkjzCAITt5kTpVwDp1wfpz2BOZMwF8tjNUkySUy1SSDEL1WYzomw2ANlsj83lRIVbkgNIvz8FQRcAMiCe_KBLAOnPhwYdEzjoTBNNki2Sk2gddAUgm-vJDro8FiDJ0ACkgEb0kxl8EEMmQAyZT14QQsxYADFjPSIFWqamJloYmNE4GCG2b4DYvp-SoDwAMeM8RUF5AWLIfUqC8gHEjPeIoEyxNDE2S0mmS1B-gNgOSpVZALdQpAU","id":"8eca5a82dc11349417399958083baab0","changes":[{"rev":"1-6025cd30f6115104c8d08850d2f89724"}]}
	{"seq":"6-g1AAAAUPeJzLYWBgEMhgTmHQTElKzi9KdUhJMtJLytVNTtYtLdYtzi8tydA1MNZLzskvTUnMK9HLSy3JAWphSmRI4v___39WIgNIsxZcs6EBMbqTBIBkkjzCAITt5kTpVwDp1wfpz2BOZMwF8tjNUkySUy1SSDEL1WYzomw2ANlsj83lRIVbkgNIvz8FQRcAMiCe_KBLAOnPhwYdMzjoTBNNki2Sk2gddAUgm-vJDro8FiDJ0ACkgEb0kxl8EEMmQAyZT14QQsxYADFjPSIFWqamJloYmNE4GCG2b4DYvp-SoDwAMeM8RUF5AWLIfUqC8gHEjPeIoEyxNDE2S0mmS1B-gNgOSpVZALpgpAY","id":"8dff43f82c24aced047e2a8b2d349f79","changes":[{"rev":"1-a4e2a02f1aa089bf5e83eab153866f3a"}]}
	{"seq":"7-g1AAAAUPeJzLYWBgEMhgTmHQTElKzi9KdUhJMtJLytVNTtYtLdYtzi8tydA1MNZLzskvTUnMK9HLSy3JAWphSmRI4v___39WIgNIsxZcs6EBMbqTBIBkkjzCAITt5kTpVwDp1wfpz2BOZMwF8tjNUkySUy1SSDEL1WYzomw2ANlsj83lRIVbkgNIvz8FQRcAMiCe_KBLAOnPhwYdMzjoTBNNki2Sk2gddAUgm-vJDro8FiDJ0ACkgEb0kxl8EEMmQAyZT14QQsxYADFjPSIFWqamJloYmNE4GCG2b4DYvp-SoDwAMeM8RUF5AWLIfUqC8gHEjPegoGQCB2WKpYmxWUoyXYLyA8R2UKrMAgC63qQH","id":"a352777d1d0a4ad75d8a9291b9985614","changes":[{"rev":"1-9c4b8b9af4cf602be0aec474cc06f657"}]}
	{"seq":"8-g1AAAAUPeJzLYWBgEMhgTmHQTElKzi9KdUhJMtJLytVNTtYtLdYtzi8tydA1MNZLzskvTUnMK9HLSy3JAWphSmRI4v___39WIgNIsxZcs6EBMbqTBIBkkjzCAITt5kTpVwDp1wfpz2BOZMoF8tjNUkySUy1SSDEL1WYzomw2ANlsj83lRIVbkgNIvz8FQRcAMiCe_KBLAOnPhwYdMzjoTBNNki2Sk2gddAUgm-vJDro8FiDJ0ACkgEb0kxl8EEMmQAyZT14QQsxYADFjPSgYGcHBaJmammhhYEbjYITYvgFi-35KgvIAxIzzFAXlBYgh9ykJygcQM94jMnOKpYmxWUoyXYLyA8R2UKrMAgC_KaQI","id":"7bf8f3d987b76dc08f2c65488c576eb4","changes":[{"rev":"1-d7e6d0eb3eaab43109c53053d2151dc5"}]}
	{"seq":"9-g1AAAAUPeJzLYWBgEMhgTmHQTElKzi9KdUhJMtJLytVNTtYtLdYtzi8tydA1MNZLzskvTUnMK9HLSy3JAWphSmRI4v___39WIgNIsxZcs6EBMbqTBIBkkjzCAITt5kTpVwDp1wfpz2BOZMoF8tjNUkySUy1SSDEL1WYzomw2ANlsj83lRIVbkgNIvz8FQRcAMiCe_KBLAOnPhwYdMzjoTBNNki2Sk2gddAUgm-vJDro8FiDJ0ACkgEb0kxl8EEMmQAyZT14QQsxYADFjPSIFWqamJloYmNE4GCG2b4DYvp-SoDwAMeM8RUF5AWLIfUqC8gHEjPeIoEyxNDE2S0mmS1B-gNgOSpVZAMDypAk","id":"e3eea6ef3cad0cca655951ad44b0131a","changes":[{"rev":"1-28114b879d3f4922038198bab46c104c"}]}
	{"seq":"10-g1AAAAUPeJzLYWBgEMhgTmHQTElKzi9KdUhJMtJLytVNTtYtLdYtzi8tydA1MNZLzskvTUnMK9HLSy3JAWphSmRI4v___39WIgNIsxZcs6EBMbqTBIBkkjzCAITt5kTpVwDp1wfpz2BOZMoF8tjNUkySUy1SSDEL1WYzomw2ANlsj83lRIVbkgNIvz8FQRcAMiCe_KBLAOnPhwYdCzjoTBNNki2Sk2gddAUgm-vJDro8FiDJ0ACkgEb0kxl8EEMmQAyZT14QQsxYADFjPSIFWqamJloYmNE4GCG2b4DYvp-SoDwAMeM8RUF5AWLIfUqC8gHEjPeIoEyxNDE2S0mmS1B-gNgOSpVZAMQCpAo","id":"7bf8f3d987b76dc08f2c65488c608acb","changes":[{"rev":"1-a4e2a02f1aa089bf5e83eab153866f3a"}]}


	{"last_seq":"10-g1AAAAUPeJzLYWBgEMhgTmHQTElKzi9KdUhJMtJLytVNTtYtLdYtzi8tydA1MNZLzskvTUnMK9HLSy3JAWphSmRI4v___39WIgNIsxZcs6EBMbqTBIBkkjzCAITt5kTpVwDp1wfpz2BOZMoF8tjNUkySUy1SSDEL1WYzomw2ANlsj83lRIVbkgNIvz8FQRcAMiCe_KBLAOnPhwYdCzjoTBNNki2Sk2gddAUgm-vJDro8FiDJ0ACkgEb0kxl8EEMmQAyZT14QQsxYADFjPSIFWqamJloYmNE4GCG2b4DYvp-SoDwAMeM8RUF5AWLIfUqC8gHEjPeIoEyxNDE2S0mmS1B-gNgOSpVZAMQCpAo","pending":1120011}
	
	
###filter 参数

filter参数指定一个预定义过滤功能应用在chang反馈上。并且，以下参数是可以过滤的：
	
- `_design`:仅针对于design document

- `_doc_ids`:仅接收来自doc_ids参数是特有的文件ID的变化

**注：** `_doc_ids`有效仅Cloudant NoSQL DB兼容Couch DB 2.0

- `_selector`：使用同样的selector 语法

**注：**`_selector`有效仅Cloudant NoSQL DB兼容Couch DB 2.0

- `view`:存在[map 功能](https://console.bluemix.net/docs/services/Cloudant/api/creating_views.html#a-simple-view) 可以使用

###since参数

当`since`为0时，请求返回的时所有的变化，`since`为`now`时，请求是从当前时间的所有变化。

Cloudant NoSQL DB自带的分布式特性可以影响回复的结果。比如，如请一系列变化两次，都使用了`since`，在结果中返回的变化的顺序可能会不同。

您可能还会看到一些结果，这些结果似乎来自since参数之前，原因可能来自分享的不同副本。
因此，共享的副本会自动地以及不停地互相复制达到最终数据相同。然而，共享的文件总有不同由于还没有完成互相复制。

当你请求一系列变化，回复的都是相同的副本。但，拥有共享副本的节点不可达，系统的代用品会相应地复制另一个节点的数据。为了确保看到所有应用的changes，使用的是副本之间的核实点。使用卡口可以有效地回滚最近的changes在共享的副本已经互相确认时。回滚意味着在提供的`since`序列鉴定之前，可以看到发生的changes

如果请求几次的`_changes`时，你的应用必须可以处理多次请求的回复。

更多关于`_changes`的内容可参见[Replication](https://console.bluemix.net/docs/services/Cloudant/guides/replication_guide.html#how-does-replication-affect-the-list-of-changes?) 


###_changes请求的回复
_changes返回的是JSON对象，包含着一系列数据库文档中的changes。接下来的表格描述每个filed的含义：

Fileds|描述|类型
changes|以数组形式返回changes|数组
deleted|相应的文件是否删除|布尔值
id|文件的识别号|字符串
last_seq|最后一个序列的识别号|字符串
results|对数据库进行的一系列修改|数组
seq|更新序列的识别号|字符串

示例对_changes请求的响应：

	{
	    "results": [
		{
		    "seq": "1-g1A...sIg",
		    "id": "foo",
		    "changes": [
		        {
		            "rev": "1-967...84d"
		        }
		    ]
		}
	    ],
	    "last_seq": "1-g1A...sIg",
	    "pending": 0
	}

###关于_changes的注意事项

- 通过_changes返回的结果是部分排序的。换句话说，多个请求时有的排序可能就不存在。获取当前的列表可以通过在_changes中包含last_changes。结果列表为使用自查询参数的后续_changes列表提供了起点。
- 尽管相同范围的分片副本包含相同的数据，但它们的_changes历史记录通常是唯一的。 这种差异是写入如何应用于碎片的结果。 例如，它们可能以不同的顺序应用。 为确保所有更改都按照您的指定顺序进行报告，可能需要进一步回溯到碎片的历史记录以找到合适的起点。 然后从该起点报告更改。 这种“回滚”可能会导致重复更新的出现，或者更新显然在指定的以后的值之前。
- 由碎片报告的变化总是按顺序呈现。 但是所有贡献碎片之间的顺序可能看起来不同。 有关更多信息，[请参阅此示例外部链接图标](https://gist.github.com/smithsz/30fb97662c549061e581) 。

- 序列值对于分片是唯一的，但在分片之间可能会有所不同。 这种变化意味着如果您拥有来自不同分片的序列值，则不能认为相同的序列值引用了不同分片中的同一文档。

###Using POST to get changes

您也可以使用POST来查询更改提要，而不是使用GET。 唯一的区别是，如果您正在使用POST，并且您正在使用docs_ids或选择器过滤器，则可以将“doc_ids”：[...]或“selector”：{...}中的部分包含在 请求正文。 预期所有其他参数都在查询字符串中，与使用GET相同。

命令行操作：

	curl -X POST "https://$ACCOUNT.cloudant.com/$DATABASE/_changes?filter=_selector" -d @request.json
	
返回结果：

	{
    "selector": {"z" : {"$gte" : 1}}
	}
	
实际结果：
	
	{
    "error": "bad_content_type",
    "reason": "Content-Type must be application/json"
	}

##Deleting a database

使用`DELETE`请求

	curl https://$ACCOUNT.cloudant.com/$DATABASE \
    -X DELETE \
    
    
 ###Backing up
 [恢复数据](https://console.bluemix.net/docs/services/Cloudant/guides/backup-guide.html#-) 
 
 
 
 ##Creating database applications
 
 除了存储在数据库文档中的数据之外，您还可能在数据库中的文档中包含客户端应用程序代码。 应用程序代码通常使用JavaScript编写。 存储在数据库中的数据和客户端代码的两层组合称为[CouchApps](https://console.bluemix.net/docs/services/Cloudant/guides/couchapps.html#couchapp) 。
 
 
 
### 著作权声明


本文由 [@刘强](https://github.com/LiuQhahah/) 翻译，转载请保留原文链接 ;)
