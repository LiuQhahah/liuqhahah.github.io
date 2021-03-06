---
layout:     post
title:      "使用rsa密钥登录远程服务器"
subtitle:   "保护vps的安全措施"
date:       2018-03-02 16:00:00
author:     "LiuQ"
header-img: "img/ssh-vps.jpeg"
header-mask: 0.3
catalog:    true
tags:
    - noSQL
    - 教程
    - Cloudant
    - 译文
---



本地主机：ubuntu
远程主机：centos

## 本地主机操作

1. 生成ssh公钥密钥对：

		liu@liu:~$ ssh-keygen -b 2048 -t rsa
		Generating public/private rsa key pair.
		Enter file in which to save the key (/home/liu/.ssh/id_rsa):
		/home/liu/.ssh/id_rsa already exists.
 		Overwrite (y/n)? y
		Enter passphrase (empty for no passphrase):
		Enter same passphrase again:
		Your identification has been saved in /home/liu/.ssh/id_rsa.
		Your public key has been saved in /home/liu/.ssh/id_rsa.pub.
		The key fingerprint is:
		SHA256:1svgfZNAne+Ny+SBI/7KuhUNf6bf3hpr05s28wona4s liu@liu
		The key's randomart image is:
		+---[RSA 2048]----+
		|                 |
		|           . .   |
		|          o o    |
		|         o + .   |
		|        S + o +  |
		|       o + + B o |
		|        . * X B..|
		|         + +.%oX+|
		|        ooE++oXBX|
		+----[SHA256]-----+
		liu@liu:~$


2. 将公钥送到远程服务器上

		liu@liu:~$ sudo scp /home/liu/.ssh/id_rsa.pub 远程服务器的ip:/root/

创建`.ssh`文件夹


	[root@centos ~]# mkdir ~/.ssh
	[root@centos ~]# chmod 700 ~/.ssh
	[root@centos ~]# mv id_rsa.pub ~/.ssh
	[root@centos ~]# cd .ssh
	[root@centos ~]# mv id_rsa.pub authorized_keys
	[root@centos ~]# chmod 600 authorized_keys

3. 在远程服务器中，关闭ssh的密码登录选项


		[root@centos ~]# vim /etc/ssh/sshd_config

		#PasswordAuthentication yes
		//改为
		PasswordAuthentication no
保存后，重启以下配置文件，如下：

		[root@centos ~]# /etc/init.d/sshd restart
		Stopping sshd:                                             [  OK  ]
		Starting sshd:                                             [  OK  ]

4. 回到本地服务器，重新登录下：

		liu@liu:~$ ssh root@ip

	如果出现`sign_and_send_pubkey: signing failed: agent refused operation Permission denied (publickey,gssapi-keyex,gssapi-with-mic)`错误时，在本地计算机，输入：

		liu@liu:~$ eval "$(ssh-agent -s)"
		Agent pid 15896
		liu@liu:~$ ssh-add
		Identity added: /home/liu/.ssh/id_rsa (/home/liu/.ssh/id_rsa)
		liu@liu:~$
再次尝试即可成功。


 参考链接：
 [购买了VPS之后你应该做足的安全措施](https://www.logcg.com/archives/884.html)
 [ssh出错 sign_and_send_pubkey: signing failed: agent refused operation](https://chchc.me/2016/05/26/ssh%E5%87%BA%E9%94%99-sign-and-send-pubkey-signing-failed-agent-refused-operation/)
