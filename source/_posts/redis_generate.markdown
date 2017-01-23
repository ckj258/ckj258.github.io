---
layout: post
title: "redis的安装与部署"
date: 2017-1-17 09:55:52
comments: true
reward: false
tags: 
	- node.js学习
---


REmote DIctionary Server(Redis) 是一个由Salvatore Sanfilippo写的key-value存储系统。  
Redis是一个开源的使用ANSI C语言编写、遵守BSD协议、支持网络、可基于内存亦可持久化的日志型、Key-Value数据库，并提供多种语言的API。   

<!-- more -->
## 运行redis
### 下载
下载地址 ：https://redis.io/    
Redis 支持 32 位和 64 位。		
这个需要根据你系统平台的实际情况选择，这里我们下载 Redis-x64-xxx.zip,解压后，将文件夹重新命名为 redis。		

### 配置
Redis 的配置文件位于 Redis 安装目录下，文件名为 redis.conf。			
参数说明 : https://ckj258.github.io/2017/01/17/redis_option/    
 
### 部署
打开一个 cmd 窗口 使用cd命令切换目录/redis 运行 redis-server redis.conf。  
![](/assets/image/redis_running.png)   
### 使用
这时候另启一个cmd窗口，原来的不要关闭，不然就无法访问服务端了。  
切换到redis目录下运行  redis-cli -h host -p port -a password(redis-cli -h 127.0.0.1 -p 6379)。  
设置键值对 set name Dantel  
取出键值对 get name  
![](/assets/image/redis_running2.png)   






