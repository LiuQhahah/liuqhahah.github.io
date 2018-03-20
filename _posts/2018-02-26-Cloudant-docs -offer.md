---
layout:     post
title:      "Cloudant Docs  : Cloudant Offer「译」"
subtitle:   "Cloudant  NoSQL DB 套餐的分类以及计费原则，数据库优点"
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

> 这篇文章翻译自[IBM Cloud Public](https://console.bluemix.net/docs/services/Cloudant/offerings/bluemix.html#ibm-bluemix) 

公共数据库是最具特色的服务，第一个接收更新以及新特征。价格是基于个人的吞度量以及存储大小，满足任何所需的负载。

免费的套餐 有固定的吞吐量，数据量用于开发和评测。
标准套餐提供可配置的吞出量以及存储空间。
专用硬件套餐也是可行的，通过额外的一个月的费用来运行一个或多个标准套餐实例在次环境中。专用硬件环境只针对你单独使用，如果专用硬件套餐是在美国部署的，你也可以选择[HIPAA健康保险流通与责任法案](https://en.wikipedia.org/wiki/Health_Insurance_Portability_and_Accountability_Act) 兼容配置。

下表总结了提供的吞吐量以及磁盘控件的配置针对与这个计划的。



套餐|精简版|标准版
------------------------------------
基础价格/每小时|0.00|[价格细节](https://www.ibm.com/cloud/cloudant/pricing?cm_mc_uid=37949901416215100219276&cm_mc_sid_50200000=14451621519692253849) 
提供的吞吐量/每秒传输|20|100|1,000|5,000|20,000
吞吐量/每秒写入|10|50|500|2,500|10,000	
吞吐量/每秒查询|5|5|50|250|1,000
最大文件大小|1 MB|1MB
磁盘空间大小|1GB|20GB
超出磁盘空间/GB/hour|未提供|[详细信息](https://www.ibm.com/cloud/cloudant/pricing?cm_mc_uid=37949901416215100219276&cm_mc_sid_50200000=14451621519692253849) 


#1套餐
当你在部署你的Cloudant NoSQL DB服务时，默认是精简版套餐（免费）的。
![](https://console.bluemix.net/docs/api/content/services/Cloudant/images/fb87416a.png?lang=zh-CN) 

Viewing and changing capacity

##精简版套餐
精简版套餐虽然是免费提供的，但是它只有1GB的数据存储量，在查询，查找，写入都有流量的限制。

存储空间也要日常核实，如果你超过限定的存储空间大小，当你在进行HTTP请求时会得到402	错误码，表明“账户超过数据使用的配额，有必要更新当前套餐”。在Cloudant的控制面板上会有小旗来提醒你。此时，你还可以删除与读取数据，但是，不能写入新的数据了，要么升级套餐要么删除数据直到低于限制。

如果你想存储超过1GB的数据，或 有更好的吞吐量的服务，可以选择标准套餐。

##标准套餐
拥有20GB的存储量，超过20GB，以每小时GB支付费用。可以参看最新的[价格表](https://www.ibm.com/cloud/cloudant/pricing?cm_mc_uid=37949901416215100219276&cm_mc_sid_50200000=14451621519692253849) 。可以更改查找，查询，写入的吞吐量。
##专用硬件套餐
你可以在专用套餐环境上运行标准套餐，在[IBM数据中心](https://www.ibm.com/cloud-computing/bluemix/data-centers?cm_mc_uid=37949901416215100219276&cm_mc_sid_50200000=14451621519692253849) 进行按月付费。采购单独的专用硬件套餐所给的地址，可以部署一个或多个标准套餐实例。这个套餐对于健康保险流通与责任答案的合规是很有必要的。
注意：专用硬件套餐只针对于IBM Cloudant公共消费者，对于IBM Cloudant专用消费者是不提供的。

##预置吞吐容量
预置的吞吐量作为识别与测量的三种事件之一：

1. 查找，基于文档的‘_id’读取特定的文档
2.  写入，创建，更改或删除一个单独文档，或根据引索创建的任何更新
3. 查询，CLoudant  NoSQL DB的查询断点的请求模式之一，包含以下类型：
	1. 元引索（[_all_docs](https://console.bluemix.net/docs/services/Cloudant/api/database.html#get-documents) ）
	2. 图视图[_view](https://console.bluemix.net/docs/services/Cloudant/api/creating_views.html#using-views) 
	3. 查找引索[_query](https://console.bluemix.net/docs/services/Cloudant/api/search.html#queries) 
	4. 地理空间引索[_geo](https://console.bluemix.net/docs/services/Cloudant/api/cloudant-geo.html#querying-a-cloudant-geo-index) 
	5. Clouant NoSQL DB 查询[_find](https://console.bluemix.net/docs/services/Cloudant/api/cloudant_query.html#finding-documents-using-an-index) 
	
吞吐量的测试是简单的事件计数，每一种类型，每秒中的事件，第二个则是滑动窗口。如果账户超过来吞吐量的限制，请求会被拒绝直到滑动窗口内事件的数量不再超过预订的数额。这获取可以帮助去考虑滑动窗口设置为1s，在连续1000毫秒后。

例如，标准套餐以1秒200次的查找频率，你的帐号可能会做一个最大200次查找在1s内。随后查找请求在1000ms内将会被拒绝，直到查找的请求数量再次减到200次以下。

当事件数量超出时，请求会被拒绝。应用会收到[429请求太多的回复](https://console.bluemix.net/docs/services/Cloudant/api/http.html#429) 。

对于429异常，一些客户端库（如[java](https://console.bluemix.net/docs/services/Cloudant/libraries/supported.html#java) ，[Nodejs](https://console.bluemix.net/docs/services/Cloudant/offerings/bluemix.html?pos=9#provisioning-a-cloudant-instance-on-bluemix) ，[Python](https://console.bluemix.net/docs/services/Cloudant/libraries/supported.html#python) ）提供了解决方法。在java中会抛出[TooManyRequestsException](http://static.javadoc.io/com.cloudant/cloudant-client/2.5.1/com/cloudant/client/org/lightcouch/TooManyRequestsException.html) 异常。

默认情况下，客户端库不会自动的尝试再次发起请求当收到429回复时。

最好的是确认你的429错误被解决。重新尝试的次数是有限制的，超过常规的请求次数对于转移到另个以平台配置上是很容易发生的。

**注意:**如果你正在移植一个已经存在的应用，它获取处理不了429错误，当你移植时，核实可以正确的处理429错误。

总的来说，你必须确保可以正确的处理429错误。

#2查看与更改容量 

通过账户的套餐可以看到吞吐量的细节，可以通过控制面板选择你想使用的配置。

![](https://console.bluemix.net/docs/api/content/services/Cloudant/images/cloudant_capacity.png?lang=zh-CN) 

移到另一吞吐量时，选择你想要的吞吐量，点击'Change Capacity'按钮，提示确认改变，提醒变化会在24小时内完成。
![](https://console.bluemix.net/docs/api/content/services/Cloudant/images/cloudant_capacity_change.png?lang=zh-CN) 

#3监控情况
利用率的信息可以在控制面板上找到。

![](https://console.bluemix.net/docs/api/content/services/Cloudant/images/cloudant_usage.png?lang=zh-CN) 
更多的细节在这儿，看到吞吐量，以及存储的数据。
监控帮你了解部署你的计划可能时可取的变化，例如，如果你频繁的查找数据的最大值，你可以修改配置通过[服务窗口](https://console.bluemix.net/docs/services/Cloudant/offerings/bluemix.html?pos=9#servicetier) 修改。

#4数据使用情况
##磁盘空间
套餐中包含了存储容量，存储数据以及引索的存储。
##磁盘超出
所有的标准套餐以及轻量级套餐都监测磁盘空间的使用率。如果用户使用超过的存储的容量，会出现溢出，溢出会导致计费，以GB为单位。

支付额外的费用是由于超出了预置的吞吐量，一般以小时来计费。在轻量级套餐中，不存在超出。

例如，使用的标准套餐中，磁盘使用增加到107GB在12小时内，表明比标准的20GB多出了87GB。超出的部分就要算成87GBx12小时来计费。

超出通过总的GB数减去套餐设定的数额再转化成一小时的GB数来计算的。
##磁盘超出举例
假设你选择了标准套餐使用了9GB,在开始的一个月里。然后，在第三天的02：00时分数据量增加到21.5GB在15分钟内，在接下来的10分钟内将数据量降低到9.5GB。在25分钟内增加到108GB。最终，你的实例通过降低到28GB来结束剩下的本月时间。

方案意味着GB数超过了88GB在第三天的02：00时候，在第三天的03：00时分以及剩下的天数中，运行的实例总是比20GB要超出8GB。

因此在第三天的02：00时分，扣费为88GBx1小时为88GB小时。
从第三天的03：00后，一天一个有21个小时，每小时超出的费用为8GB，因此是8x21=168GB  小时
从第四天的0时到月底，费用为8GBx24小时x27天=5184小时。
总费用为88+168+5184 = 5440GB小时。
超出的GB数以小时段计费，02：00——02：59为一段，并且以超出的最高值计费。


##单个文档的最大尺寸
数据以JSON文档的形式存储在CLoudant NoSQL数据库中，存储在IBM云上的Cloudant数据库最大文件大小为1MB，超出会出现[413错误](https://console.bluemix.net/docs/services/Cloudant/api/http.html#413) 。

#5地点和租赁

默认情况下，所有的轻量级与标准套餐都部署在多租户的环境中。作为可选择的部分，可以从IBM公共云区域中选择：

1. 美国南部
2. 联合王国
3. 悉尼
4. 德国

专用硬件套餐大多数部署在[ IBM data center locations](https://www.ibm.com/cloud-computing/bluemix/data-centers) 中。可以在IBM Clouant目录的可用地址的下方看到。

#6安全，加密以及合规性

服务器上所有的套餐都提供[数据在休息](https://en.wikipedia.org/wiki/Data_at_rest) 磁盘加密。访问是通过HTTPS加密访问的，更过的细节，参见[ IBM Cloud Security ](https://console.bluemix.net/docs/services/Cloudant/offerings/security.html#security) 

套餐还提供[安全合规证书](https://console.bluemix.net/docs/services/Cloudant/offerings/security.html#security) ，HIPPA标准需要一个[专有环境](https://console.bluemix.net/docs/services/Cloudant/offerings/compliance.html#cloudant-security-compliance) ，因此需要这个环境是供应过程的一部分。

#7高可用性，备份，恢复
提供高可用性以及发生灾难情况下的数据恢复，全部的数据存储在一式三份的三个不同物理服务器的集群中。在多元数据中心存储数据，然后使用接下来的数据复制来实现高可用性以及应急恢复。Cloudant 数据库不会自动备份，但提供了备份的工具。参阅[ Disaster Recovery and Backup guide](https://console.bluemix.net/docs/services/Cloudant/guides/disaster-recovery-and-backup.html#disaster-recovery-and-backup) 了解更多的高可用性，应急恢复和备份，来达到应用的要求。

#8支持
支持标准版以及专有版本，通过采购标准版技术支持，轻量级不提供。
标准版的支持在 [这儿](https://console.bluemix.net/?direct=classic/#/pricing/cloudOEPaneId=pricing&paneId=pricingSheet) ，关于支持服务等级在[这儿](http://www-03.ibm.com/software/sla/sladb.nsf/pdf/6606-08/$file/i126-6606-08_05-2016_en_US.pdf) 

 


#9部署实例
部署轻量级与标准版有两种方法：

1. 使用控制面板，这是[教程](https://console.bluemix.net/docs/services/Cloudant/tutorials/create_service.html#creating-a-cloudant-instance-on-bluemix) 
2. 使用命令行CFL，[教程](https://console.bluemix.net/docs/services/Cloudant/tutorials/create_service_cli.html#creating-a-cloudant-instance-on-bluemix-using-the-cloud-foundry-tools) 

创建专用硬件实例参照[这个](https://console.bluemix.net/docs/services/Cloudant/tutorials/create_dedicated_hardware_plan.html#creating-and-leveraging-a-cloudant-dedicated-hardware-plan-instance-on-bluemix) 



### 著作权声明


本文由 [@刘强](https://github.com/LiuQhahah/) 翻译，转载请保留原文链接 ;)
